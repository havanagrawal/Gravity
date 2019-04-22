---
layout: post
title:  "The Builder Pattern in Python"
date:   2019-04-01 11:13:00 -0700
category: ["learning", "pinned"]
---

While writing a small scraper, I came across a design problem that I would have mostly solved with a builder in Java, which led me to wonder if an equivalent exists in Python.

1. [The Builder Pattern](#the-builder-pattern)
2. [Why is there no Builder pattern in Python?](#why-is-there-no-builder-pattern-in-python)
3. [Key Takeaways](#key-takeaways)

### The Builder Pattern

The builder pattern is a creational design pattern that solves one or more of the following problems:
1. creating complex objects with a complex representation,
2. many optional arguments for creating an object, and
3. similar types for constructor arguments.

For example:

```java
class Person {
  public Person(String firstName, String middleName, String lastName) {
    ...
  }
}

...

// No way of knowing if the format was first-middle-last or last-middle-first
// or something else entirely
Person p = new Person("Jonathan", "John", "Johnson");
```

While most modern IDE's will annotate the arguments, one cannot always rely on developers using an IDE to read your code. This problem can be solved in two ways:

```java
// Using inline comments to annotate the arguments
Person p = new Person(
  /* firstName = */ "Jonathan",
  /* middleName = */ "John",
  /* lastName = */ "Johnson"
);
```

This works well for problem 3, but doesn't really solve problem 2. For instance, if we have a class that has reasonable default values, like so:

```java
class CartesianPoint {
  private double x;
  private double y;
  private double z;

  public CartesianPoint(double x, double y, double z) {
    this.x = x;
    this.y = y;
    this.z = z;
  }

  public CartesianPoint(double x, double y) {
    this(x, y, 0.0);
  }

  public CartesianPoint(double x) {
    this(x, 0.0, 0.0);
  }

  public CartesianPoint() {
    this(0.0, 0.0, 0.0);
  }
}
```

The problem with this is that we end up with multiple overloaded constructors, and there is no convenient way of creating points on the y or z axes. Individual setters would force us to have mutable objects, an undesirable aspect. The Builder pattern solves all of these problems:

```java
// Easy to identify each string as a part of the Person object
Person p = new PersonBuilder().setFirstName("Jonathan")
                              .setMiddleName("John")
                              .setLastName("Johnson")
                              .build();

// x and y default to 0 in the builder
CartesianPoint pt = new CartesianPointBuilder().setZ(10).build();
```

### Why is there no Builder pattern in Python?

I was initially tempted to create a literal translation of the builder pattern in Python, but I decided to Google it anyway, which led me to this [excellent StackOverflow answer](https://stackoverflow.com/a/11977454/4014685). It turns out that Python as a language does not suffer from these problems because it supports both named arguments and default values for arguments, allowing one to do this:

```python
class Person:
  def __init__(self, first_name, middle_name, last_name):
    self.first_name = first_name
    ...

# Easy to identify each string as a part of the Person object
p = Person(
  first_name="Jonathan",
  middle_name="John",
  last_name="Johnson"
)


class CartesianPoint:
  def __init__(self, x=0.0, y=0.0, z=0.0):
    self.x = x
    ...

# x and y default to 0 in the builder
pt = CartesianPoint(z=5.0)
```

### Key Takeaways

For me, this was an important lesson in design patterns. These patterns are intended to solve problems that are most common in the software engineering world, but it is important to identify these patterns in the wild, and their suitability.

1. Don't use design patterns on auto-pilot
2. Learn to identify design patterns baked into the language
