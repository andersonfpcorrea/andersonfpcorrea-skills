# Chapter 7: Different Layer, Different Abstraction

## Core Idea
Each layer in a well-designed system provides a different abstraction from the layers above and below it. If adjacent layers have similar abstractions, it's a red flag that the decomposition is wrong.

## Key Concepts
- **Pass-through methods**: Methods that do almost nothing except invoke another method with the same or similar signature. They indicate confusion about responsibility, increase interface complexity without adding functionality, and create coupling between classes.
- **Dispatcher**: A legitimate form of same-signature methods — it selects which method to invoke based on arguments. Provides value (routing logic); not a pass-through.
- **Decorator pattern**: Wraps an existing object to extend it while providing the same API. Often creates pass-through methods and shallow classes. Usually better alternatives exist: add directly to the underlying class, merge with use case, or merge decorators.
- **Interface vs. implementation**: A class's interface should use different representations than its implementation. If they're the same, the class isn't providing much value. (Line-oriented text API on top of line-oriented storage = no added value.)
- **Pass-through variables**: Variables passed through a long chain of methods that only the bottommost method uses. Intermediate methods must know about something they don't use.
  - Solutions: store in shared object; global variable (usually bad); **context object** — stores all global/application state, available everywhere, enables multiple instances in one process.

## Code Examples

Pass-through methods (red flag):
```java
public class TextDocument {
    private TextArea textArea;
    public Character getLastTypedCharacter() {
        return textArea.getLastTypedCharacter(); // pure pass-through
    }
    public void insertString(String text, int offset) {
        textArea.insertString(text, offset); // pure pass-through
    }
}
// 13 of 15 public methods in the original were pass-throughs
```

## Anti-patterns (Red Flags)
- **Pass-Through Method**: Does nothing except pass its arguments to another method, usually with the same API. Indicates unclear division of responsibility.
- Decorator overuse: Every small feature gets its own wrapper class → explosion of shallow classes.

## Mental Models
- Each method call crossing a layer boundary should change the abstraction. If the abstraction hasn't changed, the layer isn't contributing value.
- When you see pass-through methods between two classes, ask: "Which class should actually own this responsibility?" Then: expose lower class directly to callers, redistribute responsibilities, or merge the classes.
- Context objects are far from perfect, but they're better than pass-through variables when the alternative is threading globals through dozens of method signatures.

## Key Takeaways
1. Adjacent layers must provide different abstractions — same abstractions = insufficient value added.
2. Pass-through methods are a symptom of unclear responsibility; eliminate by reassigning or merging.
3. Decorators are almost always better replaced by merging into the underlying class.
4. Interface and implementation should represent the data differently — the transformation is the value.
5. Pass-through variables cause every intermediate method to know about something it doesn't use.

## Connects To
- **Ch 4**: Deep modules naturally have different interface and implementation abstractions
- **Ch 6**: General-purpose lower layers, special-purpose upper layers
- **Ch 9**: Splitting and joining modules based on responsibility clarity
