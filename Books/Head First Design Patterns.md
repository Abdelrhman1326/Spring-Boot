
# Chapter One - Strategy Pattern

### Inheritance vs. The Strategy Pattern
- **How Inheritance works:** `whiteDuck` _is a_ `Duck`. It automatically knows or inherits everything `Duck` can do. It doesn't "use" `Duck` as an outside component; it literally embeds it.
- **How the Strategy Pattern works:** This pattern relies on **Composition ("Has-A" relationship)** instead of inheritance. Instead of _being_ a type of duck, a `Duck` object _has_ a specific behavior component plugged into it at runtime.


The whole point of the Strategy Pattern is to delay decisions from **Compile Time** to **Runtime**. It allows your code to decide _how_ to do something while the program is actively running, rather than locking it in when you compile the code.
### What the Compiler sees (Compile Time)
At compile time, you use an **Interface** to set up a generic placeholder. The compiler doesn't care _how_ the job gets done; it just checks that the method exists.

``` java
public class Duck {
    private FlyBehavior flyBehavior; // Interface placeholder

    public void performFly() {
        flyBehavior.fly(); // The compiler just checks: "Does FlyBehavior have a fly() method?" Yes. Code compiles!
    }
}
```

At compile time, the computer has **no idea** if the duck is going to fly with wings, fly like a rocket, or not fly at all. It just knows that whatever goes into that slot will have a `.fly()` method.

``` java
Duck myDuck = new Duck();

// RUNTIME DECISION 1:
myDuck.setFlyBehavior(new FlyWithWings()); 
myDuck.performFly(); // Prints: "Flapping wings!"

// RUNTIME DECISION 2: (The app is still running, no recompiling needed!)
myDuck.setFlyBehavior(new FlyNoWay()); 
myDuck.performFly(); // Prints: "Sits silently on the ground."
```
