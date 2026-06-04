
# Chapter One - Strategy Pattern

### Inheritance vs. The Strategy Pattern
- **How Inheritance works:** `whiteDuck` _is a_ `Duck`. It automatically knows or inherits everything `Duck` can do. It doesn't "use" `Duck` as an outside component; it literally embeds it.
- **How the Strategy Pattern works:** This pattern relies on **Composition ("Has-A" relationship)** instead of inheritance. Instead of _being_ a type of duck, a `Duck` object _has_ a specific behavior component plugged into it at runtime.
