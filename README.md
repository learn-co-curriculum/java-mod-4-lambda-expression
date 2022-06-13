# Lambda Expression

## Learning Goals

- Understand how lambda expressions can help us write more meaningful, concise
  code.
- Create lambda expressions with no parameters.
- Understand the difference between lambda expressions and anonymous classes.
- Create lambda expressions with parameters and return values.

## Introduction

A lambda expression is a way to create behaviors without tying them to specific
objects. As we’ll see later, this communicates our code’s intention better while
also making it more concise.

## What is a Lambda Expression?

A lambda expression represents an **anonymous function** that can be **passed
around**. Let’s take a deeper look at the bolded words:

- **anonymous** → A lambda doesn’t have a name unlike methods
- **function** → A lambda isn’t associated with a class like methods are. But
  lambdas group code just like methods do, have parameters, and return values.
- **passed around** → Lambdas can be stored in variables and passed as arguments
  to other methods or lambdas.

Here’s what a lambda expression without any parameters and a single line looks
like:

```java
() -> System.out.println("Hello!");
```

Let’s see what it looks like in a program:

```java
@FunctionalInterface
interface Greeter {
    void display();
}

public class Example {
    public static void main(String[] args) {
        Greeter test = () -> System.out.println("Hello!");
        test.display();
    }
}
```

Functional interfaces and lambdas work together. In order for a lambda to work,
it needs to be associated with a functional interface. In this case, the `test`
variable is being associated with the `Greeter` functional interface.

The lambda expression is associated with the body of the abstract function in
the functional interface. We don’t need to specify any return type for the
lambda expression since it can infer the type from the abstract method (we’ll
discuss this in detail later). It can do this because functional interfaces must
have only one abstract method and since a lambda expression is defining the body
of that method there’s no ambiguity.

If we need to add multiple lines to the lambda expression definition, we can use
`{}` to wrap them.

```java
@FunctionalInterface
interface Greeter {
    void display();
}

public class Example {
    public static void main(String[] args) {
        Greeter test = () -> {
            System.out.println("Hello!");
            System.out.println("Hope you're having a wonderful day!");
        };
        test.display();
    }
}
```

## Lambda Expression vs Anonymous Class

An anonymous class is a way of writing concise code and a lambda expression is
sometimes compared with it. But these are two different programming constructs.
Let’s take a look at an anonymous class implementation and then compare it with
the implementation with the lambda expression.

```java
@FunctionalInterface
interface Greeter {
    void display();
}

public class Example {
    public static void main(String[] args) {
        Greeter test = new Greeter() {
            @Override
            public void display() {
                System.out.println("Hello!");
                System.out.println("Hope you're having a wonderful day!");
            }
        };
        test.display();
    }
}
```

Notice two key differences between this implementation and the one with the
lambda expression:

1. The lambda version is more concise.
2. The lambda version communicates the intent of the code much better. Although
   the end result is the same, the anonymous class code is creating an _object
   with the desired behavior_ while the lambda version is creating only the
   desired behavior.

You might hear that lambdas are “syntactic sugar” for anonymous classes but
that’s not the case since they perform the desired behavior differently.

## Lambdas with Parameters

When creating lambdas with parameters we only have to specify the types in the
interface definition and not when writing the lambda definition.

```java
@FunctionalInterface
interface Adder {
    int add(int x, int y);
}

public class Example {
    public static void main(String[] args) {
        Adder adder = (x, y) -> x + y;
        int res = adder.add(3, 2);
        System.out.println(res);
    }
}
```
