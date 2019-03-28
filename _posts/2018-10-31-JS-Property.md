## Adding New Properties

[原文](https://www.w3schools.com/js/js_object_properties.asp)

You can add new properties to an existing object by simply giving it a value.

Assume that the person object already exists - you can then give it new properties:

### Example

``` js
person.nationality = "English";
```

### js arrays Pop and Push

**Popping**
The pop() method removes the last element from an array:

```
Example
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.pop();              // Removes the last element ("Mango") from fruits

The pop() method returns the value that was "popped out":

Example
var fruits = ["Banana", "Orange", "Apple", "Mango"];
var x = fruits.pop();      // the value of x is "Mango"
```

**Pushing**
The push() method adds a new element to an array (at the end):

```
Example
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.push("Kiwi");       //  Adds a new element ("Kiwi") to fruits

The push() method returns the new array length:

Example
var fruits = ["Banana", "Orange", "Apple", "Mango"];
var x = fruits.push("Kiwi");   //  the value of x is 5
```

