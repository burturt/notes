- `[w for w in [w.split() for w in s] if w not in d]`
- imperative, oop, functional, logic
- Imperative: statements, loops, mutable variables
- oop: organized into classes/objects
- Functional: math-like functions only (expressions, functions, constants, recursion)
- Logic: programs define facts & rules, then query against facts and rules
### Using PLs
- Compiler: convert program source into object modules (e.g. machine language or bytecode)
- Linker: combines multiple object modules and libraries into single executable/library
# Python
- Python uses object references
```python
import copy

c2 = copy.deepcopy(c)
c3 = copy.copy(c) # shallow, copy just top level

class Person:
  def __init__(self, name):
    self.name = name

  def talk(self):
    print('Hi!\n')

class Student(Person):
  def __init__(self, name):
    super().__init__(name)
    self.units = 0

  def talk(self):
    print(f"Heya, I'm {self.name}.")
    super().talk()

def chat(p):
 p.talk()

def cs131_lecture():
 s = Student('Angelina')
 chat(s)

```
- Python `==` tests equality, `is` or `is not` tests for object reference (like pointer equality)
- Mutation to default parameters changes default for all future calls
- `__eq__`, `__str__`