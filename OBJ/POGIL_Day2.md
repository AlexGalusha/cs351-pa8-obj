# PA8 POGIL Activity - Day 2: Polymorphism and Advanced Scoping

## CS351 Programming Languages - OBJ Language Deep Dive

**Instructions**: Continue working in your groups from Day 1. Today we explore the more advanced features of OBJ that make it unique among object-oriented languages.

---

Group Member 1:
Group Member 2:
Group Member 3:
Group Member 4:

## Part 5: Combined Inheritance Patterns (20 minutes)

_Based on Lessons 9-11_

### Model 5A: Static and Object Inheritance Together

```obj
% Program I
define Company = class
    static companyName = "TechCorp"
    static employeeCount = 0

    field employeeId
    field salary

    method init = proc(sal) {
        set <Company>employeeCount = add1(<Company>employeeCount);
        set employeeId = <Company>employeeCount;
        set salary = sal;
        this
    }

    method getId = proc() employeeId
    method getSalary = proc() salary
    method getCompanyName = proc() <myclass>companyName
end

define Manager = class extends Company
    static companyName = "TechCorp Management"  % Override static
    field teamSize

    method init = proc(sal, team) {
        .<super>init(sal);
        set teamSize = team;
        this
    }

    method getTeamSize = proc() teamSize
end

define emp1 = .<new Company>init(50000)
define mgr1 = .<new Manager>init(80000, 5)
define emp2 = .<new Company>init(55000)

.<emp1>getId()           % 1
.<mgr1>getId()           % 2
.<emp2>getId()           % 3
.<mgr1>getCompanyName()  % "TechCorp Management"
.<mgr1>getTeamSize()     % 5

% What's the total count?
<Company>employeeCount
<Manager>employeeCount  % Is this the same?
```

**Critical Thinking Questions:**

1. What is `<Company>employeeCount` after creating all three employees?

2. What is `<Manager>employeeCount`? Why?

3. Which `companyName` does the manager's `getDetails` show? Why is `<myclass>` important here?

### Model 5B: Advanced Shadowing

```obj
% Program J
define Base = class
    static x = 100
    field x  % Same name!

    method init = proc() {
        set x = 200;
        this
    }

    method getStaticX = proc() <myclass>x
    method getInstanceX = proc() x
end

define Derived = class extends Base
    static x = 300  % Shadow static
    field x  % Shadow instance

    method init = proc() {
        .<super>init();
        set x = 400;
        this
    }
end

define obj = .<new Derived>init()
.<obj>getStaticX()    % 300 (Derived's static)
.<obj>getInstanceX()  % 400 (Derived's instance)

% Direct access tests:
<Base>x        % What value?
<Derived>x     % What value?
<obj>x         % What value?
```

**Critical Thinking Questions:**

4. When a `Derived` object calls `getStaticX` and `getInstanceX`, what values are returned?

5. How does `<myclass>` know whether to use Base's or Derived's static `x`?

6. Can you access Base's instance `x` (value 200) from outside the object? How?

**Synthesis Question:**

7. This shadowing behavior is unlike Java/C++. What advantage might allowing field shadowing provide? What danger?

---

## Part 6: Polymorphism and Dynamic Dispatch (25 minutes)

_Based on Lesson 12_

### Model 6A: Understanding `self` vs `this`

```obj
% Program K
define Shape = class
    field color

    method init = proc(c) {
        set color = c;
        this
    }

    method describeWithSelf = proc() {
        % Using self for dynamic dispatch
        .<self>getType()
    }

    method describeWithThis = proc() {
        % Using this for static dispatch
        .<this>getType()
    }

    method getType = proc() 1  % Returns 1 for shape
end

define Circle = class extends Shape
    field radius

    method init = proc(c, r) {
        .<super>init(c);
        set radius = r;
        this
    }

    method getType = proc() 2  % Returns 2 for circle
end

define Square = class extends Shape
    field side

    method init = proc(c, s) {
        .<super>init(c);
        set side = s;
        this
    }

    method getType = proc() 3  % Returns 3 for square
end

define c = .<new Circle>init("red", 5)
define s = .<new Square>init("blue", 10)

% Test with self (dynamic dispatch):
.<c>describeWithSelf()  % Returns 2 (Circle's getType)
.<s>describeWithSelf()  % Returns 3 (Square's getType)

% Test with this (static dispatch):
.<c>describeWithThis()  % Returns 1 (Shape's getType)
.<s>describeWithThis()  % Returns 1 (Shape's getType)
```

**Critical Thinking Questions:**

8. What's the difference in output between using `.<self>getType()` and `.<this>getType()`?

9. When Shape's `describeWithSelf` method calls `.<self>getType()`, which version of `getType` runs?

10. Why might you want to use `<this>` instead of `<self>` sometimes?

### Model 6B: External vs Internal Dispatch

```obj
% Program L
define Logger = class
    method log = proc(msg) {
        .<self>getPrefix();  % Returns prefix code
        msg  % Just return the message
    }

    method getPrefix = proc() 1  % 1 = LOG
end

define TimestampLogger = class extends Logger
    method getPrefix = proc() 2  % 2 = TIMESTAMP
end

define ErrorLogger = class extends Logger
    method getPrefix = proc() 3  % 3 = ERROR

    method logError = proc(msg) {
        .<super>log(msg)  % Call parent's log
    }
end

define tLogger = new TimestampLogger
define eLogger = new ErrorLogger

.<tLogger>log("test")      % getPrefix returns 2, then "test"
.<eLogger>log("test")      % getPrefix returns 3, then "test"
.<eLogger>logError("test") % Still returns 3 due to self!
```

**Critical Thinking Questions:**

11. What prefix code is returned when `TimestampLogger` logs a message?

12. When `ErrorLogger`'s `logError` calls `.<super>log(msg)`, which `getPrefix` gets called?

13. How would the behavior change if Logger's `log` used `.<this>getPrefix()` instead?

**Synthesis Question:**

14. This demonstrates the Template Method pattern. Can you think of a real-world example where you'd want this behavior?

---

## Part 7: Environment Navigation Symbols (20 minutes)

_Based on Lessons 13-15_

### Model 7A: The Power of `<!@>` (Lexical Environment)

```obj
% Program M
let
    outerVar = 999
in
    define ClassWithClosure = class
        field instanceVar

        method init = proc(val) {
            set instanceVar = val;
            this
        }

        method accessLexical = proc()
            <!@>outerVar  % Access let-binding variable

        method accessInstance = proc()
            instanceVar
    end

    define obj = .<new ClassWithClosure>init(111)
    .<obj>accessLexical()    % 999
    .<obj>accessInstance()   % 111

    % Can we change outerVar?
    set outerVar = 777
    .<obj>accessLexical()  % 777 now!
```

**Critical Thinking Questions:**

15. What value does `<!@>outerVar` access?

16. After changing `outerVar` to 777, what does `accessLexical` print?

17. Can you access `outerVar` from outside the let-binding?

### Model 7B: Current Environment with `@`

```obj
% Program N
define ContextClass = class
    method getEnv = proc()
        @  % Just @ by itself - returns current environment
end

let
    x = 100
    obj = new ContextClass
in
    let
        x = 200
    in
        .<obj>getEnv()  % Returns environment object
    end
end
```

**Critical Thinking Questions:**

18. What does `@` represent?

19. How is `@` different from `<this>`?

### Model 7C: Practical Symbol Usage

```obj
% Program O
define ConfigurableClass = class
    static defaultSize = 10
    field size
    field data

    method init = proc() {
        set size = <myclass>defaultSize;
        set data = "initial";
        this
    }

    method resetToDefault = proc() {
        set size = <superclass>defaultSize;  % If in subclass
        this
    }

    method cloneFrom = proc(other) {
        set size = <other>size;
        set data = <other>data;
        this
    }
end

define CustomConfig = class extends ConfigurableClass
    static defaultSize = 20

    method getMyDefault = proc() <myclass>defaultSize
    method getParentDefault = proc() <superclass>defaultSize
end

define obj1 = .<new CustomConfig>init()
<obj1>size  % What value?
```

**Critical Thinking Questions:**

20. What's the initial size of `obj1`? Which `defaultSize` was used?

21. When would you use `<superclass>` instead of naming the parent class directly?

**Synthesis Question:**

22. OBJ has 6 special symbols: `self`, `this`, `super`, `myclass`, `superclass`, `<!@>`. Create a one-sentence description for when to use each.

---

## Part 8: Advanced Features (15 minutes)

_Based on Lessons 16-18_

### Model 8A: Working with Display and Output

```obj
% Program P
define FormattedPrinter = class
    method formatHeader = proc(title)
        % Returns a header code
        title

    method formatList = proc(items)
        % Note: In real OBJ, you'd need proper list support
        % This is pseudocode for demonstration
        .<self>formatHeader("List Items")
end

define Report = class
    field title
    field content

    method init = proc(t, c) {
        set title = t;
        set content = c;
        this
    }

    method getTitle = proc() title
    method getContent = proc() content
end
```

**Critical Thinking Questions:**

23. How would you extend this to support different output formats?

24. What pattern would you use to separate presentation from data?

---

## Integration Challenge: Design Patterns (20 minutes)

### Challenge: Implement the Strategy Pattern

The Strategy pattern lets you change an algorithm at runtime. Complete this implementation:

```obj
% Strategy Pattern in OBJ
define SortStrategy = class
    method sort = proc(data) {
        % Basic sort - returns 0 as identifier
        0
    }
end

define BubbleSort = class extends SortStrategy
    method sort = proc(data) {
        % Bubble sort - returns 1 as identifier
        1
    }
end

define QuickSort = class extends SortStrategy
    method sort = proc(data) {
        % Quick sort - returns 2 as identifier
        2
    }
end

define DataProcessor = class
    field strategy
    field data

    method init = proc(d) {
        set data = d;
        set strategy = new SortStrategy;  % Default
        this
    }

    method setStrategy = proc(s) {
        % TODO: Set the strategy
        % set strategy = ?
        % this
    }

    method processData = proc() {
        % TODO: Use the strategy to sort data
        % .<strategy>sort(data)
    }
end

% Test your implementation:
define processor = .<new DataProcessor>init("mydata")
.<processor>processData()  % Should use basic sort

.<processor>setStrategy(new BubbleSort)
.<processor>processData()  % Should use bubble sort

.<processor>setStrategy(new QuickSort)
.<processor>processData()  % Should use quick sort
```

**Group Tasks:**

25. Complete the `setStrategy` and `processData` methods

26. How does this pattern take advantage of OBJ's features?

27. Create another strategy (like `MergeSort`) and test it

---

## Final Challenge: Putting It All Together (10 minutes)

### Create a Mini Framework

Design a simple event system using everything you've learned:

```obj
% Your task: Create an event handling system
define Event = class
    static totalEvents = 0
    field type
    field data

    % TODO: Initialize events with type and data
end

define EventHandler = class
    % TODO: Add a handle method that processes events
end

define ClickHandler = class extends EventHandler
    % TODO: Override handle for click events
end

define KeyHandler = class extends EventHandler
    % TODO: Override handle for key events
end

define EventDispatcher = class
    field handlers  % Pretend this is a list

    % TODO: Add methods to:
    % - Register handlers
    % - Dispatch events to all handlers
    % - Use polymorphism effectively
end

% Test scenario:
% 1. Create a dispatcher
% 2. Register both types of handlers
% 3. Create and dispatch different events
% 4. Verify polymorphic behavior
```

**Design Questions:**

28. How did you use `<self>` vs `<this>` in your implementation?

29. Where did you use static members and why?

30. How does inheritance help in this design?

---

## Day 2 Reflection (5 minutes)

**Individual Synthesis:**

31. What's the most powerful feature of OBJ that you discovered?

32. How do OBJ's scope symbols (`self`, `super`, `myclass`, etc.) compare to other languages you know?

33. What programming pattern or technique from OBJ would you like to see in other languages?

**Group Discussion:**

34. As a group, create one example where OBJ's unique features solve a problem that would be harder in Java/Python/C++.

35. What questions do you still have about OBJ for the homework assignment?

---

## Key Takeaways

You've now explored:

- The difference between `self` (deep/dynamic) and `this` (shallow/static)
- How `super`, `myclass`, and `superclass` navigate inheritance
- The power of `<!@>` for lexical scope closure
- Polymorphism through dynamic dispatch
- How OBJ treats classes and environments as first-class values

**For the Homework:**

- Q1-Q6 will ask you to use these symbols in specific scenarios
- Remember: each symbol has a specific purpose
- Draw diagrams of object structure when confused
- Test your understanding with small examples first

---

## Summary Cheat Sheet

| Symbol       | Description           | Use Case                          |
| ------------ | --------------------- | --------------------------------- |
| `self`       | Base object (dynamic) | Polymorphic internal calls        |
| `this`       | Current object part   | Access current class's members    |
| `super`      | Parent object part    | Call overridden parent methods    |
| `myclass`    | Current class         | Access static members dynamically |
| `superclass` | Parent class          | Access parent's static members    |
| `<!@>`       | Lexical environment   | Access closure variables          |
| `@`          | Current environment   | Reflection/debugging              |
| `<expr>`     | Computed environment  | Dynamic member access             |

---

_Course content developed by Declan Gray-Mullen for WNEU with Claude_
