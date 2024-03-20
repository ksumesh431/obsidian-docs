
`__init__` or anything starting and ending with __ are dunder methods


# Constructors

``` python
class Person:

	def __init__ (self, n, o):
		print("Hey I am a person")
		self.name = n
		self.occ = 0

	def info(self):
	print(f"{self.name} is a {self.occ}")

a = Person("Harry", "Developer")f
b = Person( "Divya", "HR")

```

Constructors will run whenever a object is cerated for a class.
output for above will be harry is a  developer and divya is a developer



# Decorators
decorators take a function as a input and returns a modified function as output. in the example, `@greet` is a decorator
![](Images/Pasted%20image%2020240223233515.png)


# Super() function 
super is used to refer to the parent/super class methods
``` python
class Employee:
  def __init__(self, name, id):
    self.name = name
    self.id = id

class Programmer(Employee):
  def __init__(self, name, id, lang):
    super().__init__( name, id)
    self.lang = lang

rohan = Employee("Rohan Das", "420")
harry = Programmer("Harry", "2345", "Python")
print(harry.name)
print(harry.id)
print(harry.lang)

```
