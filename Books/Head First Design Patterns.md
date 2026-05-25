
## chapter One
### That ducks problem:
`program to an interface 'a superType' not for an implementation:`
`Define the aspects that change in your code, and separate them:`

#### The implementation for the idea of behavior collections:
```python
from abc import ABC, abstractmethod

class QuackBehavior(ABC):
	@abstractmethod
	def quack(self):
		pass

class FLyBehavior(ABC):
	@abstractmethod
	def fly(self):
		pass
# now implement concrete behaviour Classes:
class CanQuack(QuackBefavior):
	def quack(self):
		print("i can quack, quack quack!")
		
class CanNotQuack(QuackBehavior):
	def quack(self):
		print("i can't quack, ... !")
		
class CanFly(FlyBehavior):
	def fly(self):
		print("i can fly!")
		
class CanNotFly(FlyBehavior):
	def fly(self):
		print("i can't fly!")

# the super duck class:
class Duck:
	def __init__(self, fly_behavior: FlyBehavior, quack_behavior: QuackBehavior):
		def perform_quack(self):
			self.quack_behavior.quack()
		def perform_fly(self):
			self.fly_behavior.fly()

# now we can create type-based ducks classes:
class MallardDuck(Duck):
	def __init__(self):
		super().__init__(FlyWithWings(), CanQuack())
```
