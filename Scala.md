# Scala Notes

## What is Scala?

Scala is a programming language based on Java that includes many syntactic sugar additions. It is a hybrid language that can be used for both functional and object-oriented programming paradigms.

## How does Scala provide both functional and object-oriented paradigms?

Scala treats every value as an object, including function expressions, therefore functions are treated as first-class citizens, i.e. they can be passed as function arguments. In these ways Scala can be used for both the object-oriented programming paradigm, like Java, and the functional programming paradigm.

## What frameworks are supported by Scala?

- Akka
- Spark
- Play
- Scalding
- Neo4j
- Lift
- Bowler

## What types of variables are there in Scala? What is the difference between them?

There are two main types of variables in Scala:
1. Mutable Variables - declared with the var keyword, these variables can be reassigned during operation.
2. Immutable Variables - declared using the val keyword, these variables are static and cannot be changed during operation.

## What are Streams in Scala?

Streams are lazily evaluated lists, that is they are not evaluated until some action requires an element's value. This lazy computation enhances the performance speed of the program.

## What are some advantages of Scala?

- Highly scalable
- Highly testable
- Highly maintainable and productive
- Facilitates concurrent programming
- Allows both functional and object-oriented programming models
- Has no boilerplate code
- Singleton objects are cleaner code than static variables
- Arrays use regular generics
- Has native tuples and concise code

## Explain the operators in Scala.

In Scala there are:
- Arithmetic operators
- Relational operators
- Logical operators
- Bitwise operators
- Assignment operators

## What is a recursion tail in Scala?

When in the method of a recursive function, the recursive call is the last function performed.

## Explain tuples in Scala.

Tuples are an immutable set of values, finite in number, that can be passed as a single unit. Unlike Arrays or Lists, Tuples can hold objects of different datatypes.

## What is the difference between a class and an object?

A class is a blueprint for an object that describes the variables an object has and the methods that it can perform.
An object is the instantiation of class into memory so that a program can interact with it.

## What is the purpose of App in Scala?

App is the superclass for an executable program. The main method of a Scala program needs to be an object which extends App.

## What are higher-order functions?

Higher-order functions take another function as an argument and returns a function as its result.

## Explain the scope provided for variables in Scala.

There are there different scopes for variables in Scala, they are:

1. Fields: variables declared inside of an object that can be accessed anywhere in a program by use of access modifiers. They can be either var or val.

2. Method Parameters: variables passed to methods for their operation. It is possible to access them outside of their method by using References. They are strictly immutable.

3. Local Variables: variables declared inside of a method and are generally accessible only inside the method, although they can be returned from the method if needed.

## What is a closure?

A closure is any method 