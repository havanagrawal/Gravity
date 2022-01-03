---
layout: post
title:  "Thoughts on Code Generation"
date:   2022-01-03 12:25:00 -0700
category: ["pinned", "learning"]
---

After completing my undergraduate degree, my first stint was a software developer role at Morgan Stanley. As part of per-work, they gifted all new to-be employees two books:

 - [_Programming Pearls_ by Jon Bentley](https://www.goodreads.com/book/show/52084.Programming_Pearls)
 - [_The Pragmatic Programmer_ by Andy Hunt and Dave Thomas](https://www.goodreads.com/book/show/4099.The_Pragmatic_Programmer)

While I didn't know it then, these would become some of the most influential books I would read in my career, and ones I would return to time and again. Some of those ideas made intuitive sense; knowing the shell has indeed paid off compounded dividends over the years, version control helped keep a lot of my college projects sane, programming deliberately and methodically (as opposed to programming by coincidence) cultivated good long-term habits, and broadening one's knowledge portfolio is just common sense.

However, as a fresh graduate, with no experience on enterprise or large-scale software, some of the key ideas in the book were lost to me. Why write elaborate DSLs when I can knock out a decent parser in a few hours? Why spend time estimating when you can spend all that time developing? And in what circumstances would I want to _generate code_?

In the past few years, however, I've learned to appreciate and leverage the immense power of code generation. _**Passive code generators**_ generate code as a one-off task. Unknowingly, I've used this form of code generation may times, even as a student:

1.   I'd use Eclipse/IntelliJ plugins to generate getters, setters, `toString` and `hashCode` implementations for my Java classes
1.   Spring Boot/Spring MVC projects and their ilk typically had a "quick start" invocation that would generate all the boilerplate for a working "Hello, world!" application that you could use as a starting point.

In contrast, _**active code generators**_ generate code on-the-fly, and the generated code reflects changes in the original source file.
1.    In Java, one of the most common implementations is _annotation processing_. For example, if you use Project Lombok, you can annotate your fields with `@Getter` and `@Setter`, and the annotation processor will generate those methods for you at build time. A Python equivalent is the `dataclasses` library, which is a must-know tool in your Python.
1.   Yet another form is to generate code at build-time from data or template (e.g. [`mustache`](https://mustache.github.io/mustache.5.html)) files. Protocol buffers are a prime example; `message`s in a `.proto` file describe the schema of data that might be transmitted over the network or persisted into a data store. Build systems can then generate source code (typically classes) in a variety of languages.

A key difference between passively generated code vs actively generated code is that the former typically gets checked into source control as is, whereas the latter is not (since it is generated at build time from other input source files).

Code generation has changed the way I perceive a lot of problems in software engineering now, and has become a powerful tool in my "software engineering toolkit". As an exercise, can you think of a clean way to solve the following problems?

### Exercise(s)

1.   You own an API that sends and receives JSON to manage books and their reviews (think Goodreads). You have a few different objects, and plan to add more in the future. Currently, you have `Book` and `Author` that typically look like this:

```json
{
    "book_id": 3,
    "title": "Three-Body Problem",
    "description": "...",
    "author_ids": [5],
    "genres": ["sci-fi", "fantasy"]
}
```

```json
{
    "name": "Cixin Liu",
    "description": "...",
    "author_id": 5
}
```

Instead of accessing these fields as `JSON.parse(s).getFieldAsString("title")`, you want to be able to instead do something like:

```java
// jsonBook is a JSON string
Book book = Book.parse(jsonBook);
System.out.println(book.getTitle());
for (String genre: book.getGenres()) {
    System.out.println(genre);
}
String jsonString = book.toJsonString();
assert jsonBook.equals(jsonString);
```

Given a JSON schema, how can you achieve this? Can you easily add new object types? How would you handle objects with missing fields?

2.   Given a set of `Book` and `Author` JSON objects, you want to create a website that serves corresponding **static** webpages for them (think Goodreads sans Javascript). The author page should contain a list of all the books that author has written. The book page should list the title, description author and genres.

How can you achieve this? Would this be an instance of _passive_ or _active_ code generation? Can you easily add styling (CSS) across your website? When would you want to move to dynamic pages?