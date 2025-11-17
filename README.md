# PA8 - OBJ

## Overview

**PA8 - OBJ: Object-Oriented Language Implementation**

In this assignment, you'll explore **OBJ**, a class-based object-oriented programming language implemented in PLCC. OBJ provides a foundation for understanding how object-oriented features work at the implementation level, including:

- **Class definitions** with inheritance (`extends`)
- **Static members** (class-level variables and methods)
- **Instance fields** and **methods**
- **Object creation** with the `new` keyword
- **Dynamic dispatch** and method resolution
- **Special reference symbols** for accessing different scopes:
  - `self` - Reference to the base object (supports deep inheritance chains)
  - `this` - Reference to the current object (shallow)
  - `super` - Reference to the parent object
  - `myclass` - Reference to the class itself
  - `superclass` - Reference to the parent class
  - `!@` - Reference to the lexical environment where the class was defined

### What You'll Learn

Through six progressive questions (Q1-Q6), you'll gain hands-on experience with:

1. **Scoping in OOP**: Understanding how static, instance, and parameter scopes interact
2. **Inheritance mechanics**: How classes extend other classes and access parent members
3. **Symbol resolution**: When to use `myclass`, `superclass`, `self`, `super`, and `this`
4. **Environment navigation**: Using the `<expr>member` syntax to access different scopes
5. **Method dispatch**: How objects resolve method calls through the inheritance chain
6. **Practical OOP patterns**: Implementing common object-oriented design patterns

Each question presents a small OBJ program with a specific challenge—you'll modify the code to achieve the desired behavior using OBJ's special symbols and scoping rules.

## Repository Structure

```
pa8/
└── OBJ/          # Object oriented language
    ├── grammar       # OBJ grammar specification
    ├── Programs/     # Programs to complete (Q1-6)
    ├── Lessons/      # In-class OBJ programs
    └── Examples/     # Sample OBJ programs
```

## Quick Start

### Using GitHub Codespaces

1. Click the green "Code" button above
2. Select "Codespaces" tab
3. Click "Create codespace on main"
4. Wait for the environment to build (first time takes ~2-3 minutes)
5. The terminal will open with PLCC ready to use

### Using VS Code DevContainers

1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
2. Install [VS Code](https://code.visualstudio.com/)
3. Install the "Dev Containers" extension in VS Code
4. Clone this repository locally
5. Open in VS Code and click "Reopen in Container" when prompted
6. The environment will build with PLCC installed

## Assignments Tasks

### Q1-6

In these questions you'll explore the use of the special symbols:
myclass, self, super, superclass, this, and !@.

OBJ/Programs contains files q1.obj, q2,obj, ..., q6.obj. These files
contains starter code and instructions. Complete your work in these
files.

## Submission

**To Submit:**

1. Test your OBJ programs to ensure they work correctly
2. From inside your container: `tar -czf /workspace/pa8-YOURNAME.zip /workspace`
3. Download and submit the zip file to Kodiak

**Grading Criteria:**

- **Submission (33.3%):** Files are properly named and located as specified
- **Completeness (33.3%):** All questions attempted (incomplete = incorrect)
- **Correctness (33.3%):** Solutions demonstrate understanding of type systems

**Late Policy:** 10% per day, maximum 5 days late

Good luck with your assignment!

---

_Course content developed by Declan Gray-Mullen for WNEU with Claude_
