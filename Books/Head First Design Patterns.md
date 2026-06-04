
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

## 1. Identify the changing aspects in your program and separate them from the constant ones

**The Core Idea:** Every piece of software changes over time. Instead of mixing the parts of your code that change frequently with the parts that stay the same, you should isolate ("encapsulate") the moving parts. That way, when a change happens, it only affects one small area instead of breaking the entire system.
### A Real-World Analogy

Think of a **desktop computer**. The parts that change or upgrade rapidly (RAM, graphics card, storage) are modular and separate from the parts that stay constant (the PC case or the motherboard's basic structure). Because they are separate, you can swap out your graphics card without buying a whole new computer.
### The Example: An E-Commerce Shipping System
Imagine you are building an online store.

- **The Constant:** The checkout process. It always takes an order, calculates the total, and processes the shipping.
    
- **The Changing Aspect:** The _shipping calculation logic_. Today you use FedEx. Tomorrow you might add UPS or DHL.

**Bad Approach (Mixed together):** If you hardcode FedEx's pricing logic directly inside your `OrderCheckout` class, you'll have to rewrite and re-test your entire checkout system every time FedEx changes its rates or you decide to switch to UPS.

**Good Approach (Separated):** You pull the shipping logic out of the checkout class.

## 2. Code to an interface, not to an implementation
**The Core Idea:** Don't tie your code directly to a specific, concrete class (the implementation). Instead, tie your code to a contract or a role (the interface). This makes your code "polymorphic," meaning you can swap out the actual object doing the work behind the scenes without changing the code that uses it.

### A Real-World Analogy
Think of a **wall power outlet**. The outlet is an _interface_. It defines a strict contract: "If you have two prongs of this size and shape, I will give you 120V of electricity."

The outlet doesn't care _what_ is plugging into it—a lamp, a vacuum, or a phone charger (the _implementations_). If it "coded to an implementation," you would have a specific wire coming out of the wall that _only_ connects to a specific model of a 1998 Sony television.

### The Example: A Notification System
Let's say your application needs to send alerts to users.

**Coding to an Implementation (Bad):** You write your system to specifically expect a `GmailEmailService` object.

If your company suddenly switches to SendGrid or wants to send SMS text messages instead, you have to tear apart the `NotificationManager` and rewrite it.

**Coding to an Interface (Good):** First, you define a contract (an Interface) called `NotificationService`. It simply promises that anything implementing it will have a `.send()` method.

``` Plaintext
                    +------------------------+
                    |     <<interface>>      |
                    |  NotificationService   |
                    +------------------------+
                    |    + send(message)     |
                    +------------------------+
                               ^
                               | (Implements)
        +----------------------+----------------------+
        |                                             |
+-------------------+                         +-----------------+
| GmailEmailService |                         | SMSTextService  |
+-------------------+                         +-----------------+
| + send(message)   |                         | + send(message) |
+-------------------+                         +-----------------+
```
