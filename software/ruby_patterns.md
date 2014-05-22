Design Patterns in Ruby
=======================

General Principles
-------------
These principles apply to any kind of software pattern. They are general
guidelines for developing better software and make all the difference in big
projects.

1.Separate the things that change from those that stay the same
---------------------------------------------------------------

Always try to identify the parts of your system that are likely to change
from those that stay that are more stable. This will hopefully make further
requirements and bug fixing focus on those areas that are more likely to
change, while the stable parts are left untouched. 

if you write strongly copuled, low-cohesion spaguetti code, any change can
provoke a chain reaction that will make you change a lot of pieces of software
that were not supposed to be related.

2.Program to an interface, not to an implementation
-------------------------------------------------------

A good start is to write code that is less coupled to itself in the first
place. Focus on what your classes need to know about each other. Here is
an example:

```ruby
# Bad interface example
my_car = Car.new
my_car.drive(500)

# this is bad code because it leads to:
if is_car
  my_car = Car.new
  my_car.drive(500)
else
  my_plane = Plane.new
  my_plane.fly(500)
end

# Good interface example
# when we think of programing to an interface, we can do
my_vehicle = Vehicle.new
my_vehicle.travel(500)
```

Remember that this problem scales out: every class that would have to handle
cars and planes are likely to have that nasty "if", while classes that use
vehicles will not have to worry about it. If you have 10 classes that support
cars and planes in the first scenario, a change on the vehicles (like adding
boat) would make you propagate the changes across all the 10 files, while using
vehicles none of these changes are required.

Always try to program to the most general type you can. You may find that it
may be better call a car a 'vehicle', or even a 'movable object', depending on
your context. This will help you separate the things that change (vehicles)
from those that stay the same (the other 10 classes).

3.Prefer Composition over Inheritance
-------------------------------------

When you create a subclass of an existing class, you are not really creating
two separate entities: you are building two classes that are bound together by
a common implementation core. Inheritance will marry the subclass to the
superclass, in a way that changes to the superclass are likely to cause changes
in the subclass as well. And more: subclasses often know too much about their
superclasses details, and often have access to stuff it shouldn't touch.

The solution for this inheritance problems is the use of composition: instead
of bringing the functionality from the superclass, we can build it from the
bottom up. To do so, we equip our objects with references to other objects
(that supply the functionality we need). In short, instead of saying an object
is a kind of something, we want to say it has something.

```ruby
# Good Composition Example

class Engine

  def start
    #start the engine
  end

  def stop
    #stop the engine
  end

end

class Car

  def initialize
    @engine = engine.new
  end

  def drive
    @engine.start
    # Cruise in USA
    @engine.stop
  end

end
```

This approach haves several advantages against inheritance from a "vehicle"
class with these same funcitons:
* The engine code is factored into its own class, ready for reuse The vehicle
* class became more simple Encapsulation is increased. We created a wall of
* interface between the car and its engine.  We can also create now types of
* engines. Our engine class can be abstract, and we may have a variety of
* engines deriving from it (GasolineEngine, ElectricEngine, etc). Our car can
* even swap its engine during runtime!

4. Delegate, Delegate, Delegate
-------------------------------

The last car example does not show the engine start/stop methods to the outside
world. If we want to do that, we can simply pass the work to the engine class,
in a process called delegation.

```ruby
# Good delegation example
class Car

# Whatever car stuff...

  def start_engine
    @engine.start
  end

  def stop_engine
    @engine.stop
  end

end
```
This combination of composition and delegation is a powerful and flexible
alternative to inheritance. It gives us more flexibility and no side effects.

5.YAGNI
-------

This principle is important to build and actually finishing real systems. YAGNI
says that you should not implement features or design flexibility that you
don`t need right now, because chances are, you ain't gonna need it later,
either.

This approach helps you focusing on what really need to be done, to spend our
time and energy implementing the things that we definitely need right now. This
come from the fact that we tend to be wrong when we try to antecipate exactly
what we will need in the future. Also, you will be adding complexity that may
not be needed, remember "Premature optimization is the root of all evil" and
"predictions are hard, specially about the future".

Beware of the over-engineering with software patterns. They can incur a
considerable cost and they are not always needed. Patterns are useful
techniques, rather than ends themselves.
