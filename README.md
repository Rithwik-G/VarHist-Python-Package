# Varhist Python Package Desicription  

## Overview  

`varhist` is a python package that makes it significantly easier to debug code. This makes you work more efficiently as you are writing fewer random `print` statements. It does this by allowing the user to easily view a variable's history. Standard debuggers do not have this capability. Once initialized, varhist will quickly and quietly track a variable's historic values and where/when the values were changed.

## Installation

<br>

Type `pip install varhist` in your command line or terminal

<br>

## IMPORTANT

For the varhist library to work, you have to type this at the start of your python code.
```python
import sys
from varhist import varhist
sys.settrace(varhist.trace)
```
This is equivalent to initializing the module. Without this, the module will not function.
You also must put your code in a main function. This is easily done by highlighting all of your code (potentially omitting imports and functions/classes), pressing tab, and then adding a line saying
```python
def main():
```
to the top (the function name is arbritary). Then, call the function at the end of the code. Simple!

E.g.

```python
import sys
from varhist import varhist
sys.settrace(varhist.trace)
print("Hello World")
```
to

<br>

```python
import sys
from varhist import varhist
sys.settrace(varhist.trace)
def main():
    print("Hello World") # Inside a function now!

main() # Call the function!
```

## Usage  

### Basic Usage

<br>

#### `varhist.track()`

To start tracking a variable, use the .track method. This takes as many arguments as you want and will start tracking all of those variables.
Note: If the variable is created later on in the code (after calling this function), the package will start tracking the variable when it is created. If the variable was created before this was called, only the changes after this call will be tracked.

#### `varhist.history()`

When you want to access the history, you can use the `varhist.history()` function. This neatly prints out the history of all the variable put in as arguments. 

#### `varhist.HIST`
To directly see the history to do something with it, you can use the varhist.HIST variable. This is a dictionary, so to access the history of a variable such as `c`, use `varhist.HIST['c']`. This will be a list of lists. Every sub-list is a line of history. The first value in the sub-list is the new value of the variable. The second is the line number and the third is the function in which the variable was changed.

NOTE: If you are tracking a variable that has not been created yet, accessing that variables history through .HIST will result in an error as that variable has not been created yet.

##### Example

```python
import sys
from varhist import varhist

sys.settrace(varhist.trace)

a = 4 # This is not included in the history of a as we start tracking it later

varhist.track('a', 'b', 'c') # Start tracking a, b, c

def main():
	a = 3
	b = a + 2 # 5
	for i in range(2):
		c += 1

main()

varhist.history('a', 'b', 'c')
print(varhist.HIST['c']) # Print history for c. You can also do this for the other variables that are being tracked.
```

##### Output

```
----------Start of History for 'a'----------
Variable 'a' changed to '3' on line '73' in function 'main'
----------End of History for 'a'----------


----------Start of History for 'b'----------
Variable 'b' changed to '5' on line '74' in function 'main'
----------End of History for 'b'----------


----------Start of History for 'c'----------
Variable 'c' changed to '0' on line '75' in function 'main'
Variable 'c' changed to '1' on line '77' in function 'main'
Variable 'c' changed to '2' on line '77' in function 'main'
----------End of History for 'c'----------
[[0, 75, 'main'], [1, 77, 'main'], [2, 77, 'main']] <--- History for 'c'. The first sub-list
says the variable c changed to 0 on line 75 in function main.
```


### Advance Usage

#### Tracking Attributes of Objects

This module can also track Attributes of Objects. For example, if you want to track the attribute `name` of a object of type `Person` (this is arbritary) named `Me`. You would seperate the attribute from the object with a `.`. Use `varhist.track('Me.name')`. You can also extend infinitely. You could, for example, track `varhist.track('Me.name.spelling')` if the name was an object with a spelling attribute, etc.

##### Example

```python
import sys
from varhist import varhist

sys.settrace(varhist.trace)
varhist.track('somebody.name')

class Person:
	def __init__(self, name):
		self.name = name

def main():
    varhist.track('x', 'y')
    somebody = Person("Rithwik")
    # Modify the name
    somebody.name = "John"
    somebody.name += ' Green'
    
main()
varhist.history('somebody.name')
```

##### Output

```
----------Start of History for 'somebody.name'----------
Variable 'somebody.name' changed to 'Rithwik' on line '71' in function 'main' # Changed in the __init__ function of the Person class. But, the function is still 'main' as the variable's scope is the main function.
Variable 'somebody.name' changed to 'John' on line '77' in function 'main'
Variable 'somebody.name' changed to 'John Green' on line '78' in function 'main'
----------End of History for 'somebody.name'----------
```

## Features in Progress

<br>

1. Running the module through the command line.
2. Tracking certain elements of a list.

I hope that this package comes in handy!
