# Lambda Expression

## Learning Goals

- Define a lambda expression as an anonymous function.
- Describe lambda expression syntax.
- Use lambda expressions to implement a functional interface.
- Refactor redundant code to use lambda expressions.

## Introduction

A lambda expression is a way to create behaviors without tying them to specific
objects. As we’ll see later, this communicates our code’s intention better while
also making it more concise.

Java is a class-based object-oriented language, which means all functionality is defined in a class
and there are no stand-alone functions.  Lambda expressions were added in Java 8 (2014) to support
functional-style programming.

## What is a Lambda Expression?

A lambda expression represents an **anonymous function** that can be **passed
around**. Let’s take a deeper look at the bolded words:

- **anonymous** → A lambda doesn’t have a name unlike methods
- **function** → A lambda isn’t associated with a class like methods are. But
  lambdas group code just like methods do, have parameters, and return values.
- **passed around** → Lambdas can be stored in variables and passed as arguments
  to other methods or lambdas.

| Syntax                       | Example Lambda Expression                                                                                                      |
|------------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| parameter -> expression      | `x -> x + 1`                                                                                                                   |
| (parameters) -> expression   | `(x , y) -> x * y`                                                                                                             |
| (parameters) -> {statements} | `(x , y) -> {`<br>&nbsp;&nbsp;&nbsp;&nbsp;`System.out.println(x);`<br>&nbsp;&nbsp;&nbsp;&nbsp;`System.out.println(y);`<br>`}`  |

The simplest lambda expression maps a single parameter to an expression.
The parameter does not require a type declaration as the type will eventually be inferred (more on that below).
An expression may consist of variables, operators, and method invocations that evaluate to a single value.
The expression on the right hand side should not be a statement (i.e. no semi-colon, variable declarations, assignment statements, conditionals, loops, etc).
The expression value is automatically returned from the function, so an explicit return statement is unnecessary.

```java
x -> x + 1
```

Multiple parameters should be enclosed in parentheses:

```java
(x , y) -> x * y 
```

One or more statements should be enclosed in a code block using curly braces:

```java
(x , y) -> {
    System.out.println(x);
    System.out.println(y);
}
```

A lambda expression that takes no parameters requires empty parentheses:

```java
( ) -> System.out.println("hello")
```

A lambda expression can provide parameter types, although they are unnecessary:

```java
(int i, int j) -> i + j
```

Since a lambda expression represents an unnamed function, how can we call it?

In Java, we can assign a lambda expression to a variable. We can also pass a lambda expression into a method call,
binding the lambda expression to a parameter variable.
The variable's declared type should be a functional interface, ensuring there is a single abstract method. 
The lambda expression implements that abstract method, which allows us to infer the
method signature, i.e, the method name, parameters, and return type.

Let’s see what it looks like in a program:

```java
@FunctionalInterface
interface BinaryFunction {
    int apply(int x, int y);
}
```

```java
public class Example1 {
    public static void main(String[] args) {
        BinaryFunction sumSquares = (x, y) -> x*x + y*y;
        System.out.println(sumSquares.apply(2,3));  //13
    }
}
```

- The functional interface `BinaryFunction` defines one abstract method named `apply`.
- The `main` method declares the variable `BinaryFunction sumSquares` and assigns the lambda expression `(x, y) -> x*x + y*y` to the variable.
- The lambda expression implements the `apply` method, since that is the only abstract method in the `BinaryFunction` interface.
  - The lambda expression's parameter types are inferred from the `apply` method.
  - The lambda expression's return type is inferred from the `apply` method.
- The method call `sumSquares.apply(2,3)` evaluates the expression as `2*2 + 3*3` and returns the result `13`.

An example of a lambda expression requiring a code block is shown below.
A conditional statement is not an expression, so it must be enclosed within curly braces to create a code block.
The `return` statements are required as the `apply` method signature indicates a return type `int`.

```java
public class Example2 {
    public static void main(String[] args) {
        BinaryFunction min = (x, y) ->
            {
                if (x < y)
                    return x ;
                else
                    return y;
            };
        System.out.println( min.apply(5, 12) );  //5
        System.out.println( min.apply(23, 14) );   //14
    }
}
```

The conditional statement can be rewritten using the ternary operator `?`.
Thus, we can rewrite the lambda expression to avoid the code block:

```java
BinaryFunction min = (x, y) -> (x < y) ? x : y;
```

## Redundant Code

Consider the functional interface `StringOperator`, which transforms the string passed as a parameter:

```java
@FunctionalInterface
public interface StringOperator {
    String apply(String s);
}
```

A lambda expression is a concise way of expressing a function in Java.  What does this mean?
Let's consider two classes that implement `StringOperator` without lambda expressions. 

- `QuestionOperator` appends "?" to the string.
- `CapitalizeOperator` converts the first letter in the string to uppercase.

```java
public class QuestionOperator implements StringOperator {
    @Override
    public String apply(String s) {
        return s + "?";
    }
}

public class CapitalizeOperator implements StringOperator {
    @Override
    public String apply(String s) {
        return s.isEmpty() ? s : s.substring(0,1).toUpperCase() + s.substring(1);
    }
}
```

We create instances of both classes and call the `apply` method to transform a string according to the operator function:

```java
public class Example4 {
    public static void main(String[] args) {
        StringOperator question = new QuestionOperator();
        StringOperator capitalize = new CapitalizeOperator();
        
        System.out.println(question.apply("is it raining")); //is it raining?
        System.out.println(capitalize.apply("hello"));  //Hello
        System.out.println(capitalize.apply(question.apply("seriously"))); //Seriously?
    }
}
```

The program produces as output:

```text
is it raining?
Hello
Seriously?
```

`QuestionOperator` and `CapitalizeOperator` provide the desired functionality; however,
there is redundant code between the two classes.  The classes are identical except for (1) the class name, and (2) the method body.  

```java
public class NAME implements StringOperator {
    @Override
    public String apply(String s) {
        METHOD BODY
    }
}
```

Normally we name a class to create instances using the `new` operator, as shown in the main methods above.
A lambda expression represents an anonymous function, so we won't need a name and in fact, we don't need a class at all.
The only thing we need for a lambda expression is the parameter and method body.

## Concise Code

A lambda expression can implement the `StringOperator` functional interface in a concise manner,
avoiding the need to create extraneous classes.

| Class Implementation                                                                                                                                     | Lambda Implementation                                                                   |
|----------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| public class QuestionOperator implements StringOperator {<br>&nbsp;&nbsp;@Override<br>&nbsp;&nbsp;public String apply(String s) { return s + "?"; }<br>} |                                                                                         |
| StringOperator question = new QuestionOperator();<br>System.out.println( question.apply("why") );                                                        | StringOperator question = s -> s + "?";<br>System.out.println( question.apply("why") ); | 


We can avoid creating classes `QuestionOperator` and `CapitalizeOperator`, and simply
use a lambda expression to implement each function:

```java
public class Example5 {
    public static void main(String[] args) {
        StringOperator question = s -> s + "?";
        StringOperator capitalize = s -> s.isEmpty() ? s : s.substring(0,1).toUpperCase() + s.substring(1);

        System.out.println(question.apply("is it raining")); //is it raining?
        System.out.println(capitalize.apply("hello"));  //Hello
        System.out.println(capitalize.apply(question.apply(capitalize.apply("seriously")))); //Seriously?
    }
}
```

## Naming lambda parameters 

When you override a method in a subclass, the number of parameters and the parameter types must be the 
same as the superclass method.
However, the parameter names need not be identical between the superclass and subclass methods.

The same rule applies to lambda expression parameters.  The lambda expression must define
the same number of parameters as the abstract method in the functional interface, but the parameter names need not match.

For example, while the `apply` method declares a parameter named `s`:

```java
@FunctionalInterface
public interface StringOperator {
    String apply(String s);
}
```

The lambda expression may implement the abstract method using a parameter named `t`:

```java
public class Example6 {
    public static void main(String[] args) {
        StringOperator startTag = t -> "<" + t  +">";
        StringOperator endTag = t -> "</" + t + ">";

        System.out.println(startTag.apply("div")); //<div>
        System.out.println(endTag.apply("div"));  //</div>
    }
}

```

## Identifying Syntax Errors 

Assume the following functional interfaces:

```java
@FunctionalInterface
interface MyFunction {
    int apply(int i, int j) ;
}
```

```java
@FunctionalInterface
interface MyConsumer {
    void consume(String s) ;
}
```

The table below points out some common errors when writing lambda expressions.

| Erroneous Code                                                              | Explanation                                                                                          | Solution                                                                                           |
|-----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| `MyFunction f =`<br>`  (a,b) -> { a + b };`                                 | A code block should contain<br>1 or more statements.<br>`a + b` is an expression<br>not a statement. | `MyFunction f =`<br>`(a,b) -> a + b;` <br>OR<br> `MyFunction f =`<br>`(a,b) -> {return a + b;};`   |
| `MyFunction f =`<br>`  (s, t, u) -> s + t + u;`                             | Lambda expression has 3<br>parameters, `apply` method<br> declares 2.                                | `MyFunction f =`<br>`(s, t) -> s + t`                                                              | 
| `MyFunction f =`<br>`  (a,b) -> if (a<b) System.out.println(a);`            | Conditional statement must be<br>in a code block.                                                    | `MyFunction f =`<br>`(a,b) -> {if (a<b) System.out.println(a);}`                                   | 
| `MyFunction f =`<br>`  (i,j) -> {System.out.println(i+j);};`                | `System.out.println`<br>is a `void` function.<br>The `apply` function<br>return type is `int`.       | `MyFunction f =`<br>`(i,j) ->`<br>`{`<br>`  System.out.println(i+j);`<br>`  return i + j;`<br>`};` |
| `MyConsumer f =`<br>`  s -> System.out.println(s);`<br><br>`f.apply("hi");` | `MyConsumer`abstract method<br>is named `consume`,<br>not `apply`.                                   | `MyConsumer f =`<br>`  s -> System.out.println(s);`<br><br>`f.consume("hi");`                      |


## Summary

Lambda expressions support functional-style programming in Java by providing a
concise implementation of a functional interface. 