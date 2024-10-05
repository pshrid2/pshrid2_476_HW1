# Welcome to Brakespeare
## To be, to not be, to maybe?
### By Pranav Shridhar


Breakspeare is a domain-specific language (DSL) designed to facilitate the creation, manipulation, 
and evaluation of fuzzy sets within the context of fuzzy logic, allowing users to work with sets that have varying 
degrees of membership rather than binary inclusion.

#### **Key Features**
1. **Fuzzy Set Creation:** Define fuzzy sets using flexible membership functions that determine how much 
an element belongs to a set. These sets are not limited to a single data type, allowing for membership 
functions defined over any generic type. Member functions are abstract when created on their own. See instructions for how to do that.

2. **Scope Management:** Supports nested scoping, enabling the creation and manipulation of fuzzy sets within
distinct contexts. This feature ensures that variables in different scopes do not interfere with each other, 
maintaining data integrity across operations.

3. **Fuzzy Set Operations:** Breakspeare includes an extensive set of operations to manipulate fuzzy sets:
    - Union: Combines two fuzzy sets, reflecting the maximum membership degree for each element.
    - Intersection: Computes the minimum membership degree for elements across two sets.
    - Complement: Calculates the negation of a fuzzy set, reversing the membership values.
    - Addition, Multiplication, and Difference: Provides numerical manipulations of fuzzy sets for more advanced operations.
    - Alpha Cut: Produces a crisp set that contains elements whose membership values exceed a specified threshold.

4. **Abstraction:** There are two kinds of FuzzySets, but thankfully you won't have to worry about any of them. You need only to 
interact with the FuzzySet type.

#### **Example Usage**
The language allows users to define fuzzy sets, perform set operations, and validate logical properties concisely:
```scala
// Create two fuzzy sets
val setA = createFuzzySet((x: Double) => if (x > 0.5) 1.0 else 0.0)
val setB = createFuzzySet((x: Double) => if (x < 0.5) 1.0 else 0.0)

// Perform union operation
val unionSet = combine(setA, FuzzySetOperation.Union, Some(setB))

// Test De Morgan's law
val complementUnion = combine(unionSet, FuzzySetOperation.Complement, None)
val complementA = combine(setA, FuzzySetOperation.Complement, None)
val complementB = combine(setB, FuzzySetOperation.Complement, None)
val intersectionOfComplements = combine(complementA, FuzzySetOperation.Intersection, Some(complementB))

// Verification
assert(complementUnion.membership(0.3) == intersectionOfComplements.membership(0.3))
```

To fulfill the requirement of specifying how to create and evaluate expressions in your language, we need to provide a step-by-step guide on how users can:
1. **Create fuzzy sets using `createFuzzySet`.**
2. **Combine fuzzy sets using operations like union, intersection, and complement.**
3. **Assign fuzzy sets to variables.**
4. **Retrieve and evaluate expressions using the `TestGate` or other utility methods.**

Here is a detailed explanation that can be included in your documentation:

---

### **How to Create and Evaluate Expressions in Breakspeare**

Breakspeare provides an intuitive set of functions and constructs to define, manipulate, and evaluate fuzzy sets and logical expressions. This guide explains how to use the language’s core features.

#### **1. Creating Fuzzy Sets**
Fuzzy sets in Breakspeare are defined using custom membership functions. These functions determine the degree of membership for any given element in the set.

**Example:**
```scala
// Create a fuzzy set where membership is 1.0 if the element is greater than 0.5
// Notice the function is defined as alambda function
val setA = createFuzzySet((x: Double) => if (x > 0.5) 1.0 else 0.0)

// Create another fuzzy set where membership is 1.0 if the element is less than 0.5
val setB = createFuzzySet((x: Double) => if (x < 0.5) 1.0 else 0.0)
```
- `createFuzzySet` accepts a lambda function defining the membership logic.
- The membership function is a simple predicate that returns a `Double` between 0.0 and 1.0 based on the input.

#### **2. Combining Fuzzy Sets Using Operations**
You can combine fuzzy sets using operations like union, intersection, complement, etc., defined in the `FuzzySetOperation` enumeration.

**Example: Union Operation**
```scala
// Perform the union of setA and setB
// I tried to make it so that you didn't have to put Some as a cast, but that's what it is when you deal with Option
val unionSet = combine(setA, FuzzySetOperation.Union, Some(setB))
```
- The `combine` method performs operations like union, intersection, and more on two fuzzy sets.
- The third parameter is optional (`Option[FuzzySet[T]]`), allowing for operations like complement that require only one set.

**Example: Complement Operation**
```scala
// Perform the complement of setA
//`None passed as the third argument because the complement operation does not require a second set.
val complementSet = combine(setA, FuzzySetOperation.Complement, None)
```

#### **3. Assigning Fuzzy Sets to Variables**
```scala
// Enter a new scope
enterScope()

// Assign the fuzzy set to a variable named "temperature"
assignGate(Assign("temperature", setA))
```
- Use `enterScope` to define a new scope. Variables assigned within this scope are isolated from other scopes.
- `assignGate` associates a fuzzy set with a variable name in the current scope.

#### **4. Retrieving and Evaluating Fuzzy Sets**
To evaluate expressions or retrieve assigned fuzzy sets, use the `get` and `evaluate` methods.

**Example: Retrieving and Evaluating a Fuzzy Set**
```scala
// Retrieve the fuzzy set associated with the variable "temperature"
val retrievedSet = get("temperature").asInstanceOf[FuzzySet[Double]]

// Evaluate the membership of a specific value
val membershipValue = retrievedSet.membership(0.6) // Returns the membership degree of 0.6
```
- `get` retrieves a fuzzy set by its variable name. It returns an `Any` type, so it needs to be cast to the appropriate `FuzzySet[T]` type.
- `membership` evaluates the degree of membership for a specific element in the set.

**Example: Using `TestGate` for Evaluation**
```scala
// Create a TestGate to evaluate the membership of a specific element
val testGate = TestGate(retrievedSet, 0.6)
val result = evaluate(testGate) // Returns the membership value for 0.6
```
- `TestGate` provides a more intuitive way to evaluate expressions in Breakspeare.
- `evaluate` processes the `TestGate` and returns the membership value.

#### **5. De Morgan's Laws and Other Logical Operations**
You can implement and test logical operations such as union, intersection, and complement using the methods provided in Breakspeare. For example:
```scala
val unionAB = combine(setA, FuzzySetOperation.Union, Some(setB))
val complementUnionAB = combine(unionAB, FuzzySetOperation.Complement, None)
val complementA = combine(setA, FuzzySetOperation.Complement, None)
val complementB = combine(setB, FuzzySetOperation.Complement, None)
val intersectionComplementAB = combine(complementA, FuzzySetOperation.Intersection, Some(complementB))

// Verify the law for specific values
assert(complementUnionAB.membership(0.3) == intersectionComplementAB.membership(0.3))
```

#### **6. Exiting a Scope**
When you are done working within a specific scope, call `exitScope` to return to the previous environment:
```scala
exitScope()
```
- This removes the current environment and restores the previous one, ensuring proper scope isolation.

### This is a design for a DSL. I'm treating this like it's going to be incorporated as part of a compilation/parsing process later on.