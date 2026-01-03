Blacklist:
![[Pasted image 20251231043951.png]]


Solution: 
We can create a generator in python to iterate all global variables and filter the callable object (functions, method, etc)
Then, we need to find the first callable global variable, which is `open_chest()` in this challenge, so we can use the `__next__()` method to get the element in the iterator.
This will return the first callable function in this program, the last thing is to call it by adding `()` to it!
```python
(f for f in globals().values() if callable(f)).__next__()()
```
