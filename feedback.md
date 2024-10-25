# Feedback for Homework 1

## Score: 13.9/15

1. -0.1%: Missing instructions in the documentation to build and run the project.
2. -0.3%: One use of `var`.
3. -0.7%: Logic gates are not *reusable* with different inputs.

```scala
// Example of gate reuse; assume this in your syntax
Assign(LogicGate("exampleGate"), ADD(MULTIPLY(v1, v2)))
Eval("exampleGate", Inputs(i1, i2))
Eval("exampleGate", Inputs(i3, i4)) // exampleGate reused
```

Is this possible in your DSL?
