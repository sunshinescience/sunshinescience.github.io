---
layout: post
title: "A Brief Introduction to Object Oriented Programming"
---

## What is Object Oriented Programming?
Object oriented programming (OOP) is a pattern of programming in which the solution to a programming problem is modelled as a collection of collaborating objects. 

Prior to the development of OOP, procedural programming divided a program into a set of functions. The data was stored in variables and the functions operated on that data. It was relatively simple, however, complications would arise when functions were changed. For example, another function may depend on the function that was changed, which could lead to the code becoming difficult to maintain. Essentially, OOP came about to help with this and other programming problems. Some benefits of using OOP include: reducing complexity, isolating the impact of changes made to code, eliminating redundant code, and refactoring code (e.g., condensing lengthy if/else statements). It also reduces development time by supporting programming by customization.

#### Why use OOP in Python? 
In Python, you could use OOP to build a data type(s) that matches real world objects. You can re-use and build upon code. You can work with abstract objects related to your domain. 

Python is an object oriented language. For example, its class model supports polymorphism, operator overloading, and multiple inheritance (see: [Learning Python](http://shop.oreilly.com/product/0636920028154.do) by Mark Lutz). But OOP is an option in Python, you don’t need to become an OOP expert and immediately start applying it because Python supports a procedural programming mode as well.

## What are the four core principles of OOP?
It is often said that there are four main concepts in OOP, which are: encapsulation, abstraction, inheritance, and polymorphism. 
I generally try to find a mnemonic way to remember things. What came to mind when I first learned this was, apple (a from apple is the first letter in *abstraction*) pie, or even ‘a pie.’
- A - abstraction
- P - polymorphism
- I  - inheritance
- E – encapsulation

While it worked for me to remember these concepts, lets look at these concepts in a logical order to better understand them. 

**Encapsulation** – It wraps up the data and code into a single unit (i.e., a class). This keeps the data and code safe from outside interference and misuse.

**Abstraction** – This can be thought of as an extension of encapsulation. Abstraction is the process of hiding unnecessary details from the user. Essentially, it hides the complexity and provides only essential information to the user.

**Inheritance** – It’s the process of creating a new object or class (called a derived class or subclass or child class) from an existing object or class (called a base class or super class or parent class), while retaining similar implementation. The derived class inherits all of the capabilities of the base class, but it can be extended and refined. Basically, the derived class is a more specialized one and it provides reusability. There are multiple types of inheritance, which include: single, multiple, multi-level, hierarchical, and hybrid. These types are based on the paradigm and specific language.

**Polymorphism** – The word means ‘having many forms’. It refers to the ability to process objects differently depending on their data type or class (I really like this definition found here: [Polymorphism](https://www.webopedia.com/TERM/P/polymorphism.html)). One is able to redefine methods for derived classes. It essentially describes the concept that objects of different types can be accessed through the same interface. And each type can provide its own, independent implementation of this interface.

These OOP concepts can help our work become organized, modular and maintainable. They help us to omit repetition, keep data safe, and make our work user friendly.

## What are the SOLID Principles?
SOLID is an acronym for five class design principles that are intended to help a software design become more understandable, flexible, and easier to maintain. SOLID can be used as a checklist, something worth revisiting to help you create a well-made class design, but each part may not be necessary to implement. 

<img src="/assets/img/solid.png" width="500" height="300">

Robert C. Martin (known as ["Uncle Bob"](https://en.wikipedia.org/wiki/Robert_C._Martin)) defined principles of OOD, which can be found here: [SOLID principles](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod).

#### **Single responsibility principle** ([SRP](https://drive.google.com/file/d/0ByOwmqah_nuGNHEtcU5OekdDMkk/view))
“A class should have one, and only one, reason to change.”

An object/class should have one primary responsibility. The object should have one reason to exist and that reason should be encapsulated within the class. It can have different behaviors, but those behaviors are oriented around that sole reason for existence. One may not want to create god objects that try to do too many different things. 

#### **Open/closed principle** ([OCP](https://drive.google.com/file/d/0BwhCYaYDn8EgN2M5MTkwM2EtNWFkZC00ZTI3LWFjZTUtNTFhZGZiYmUzODc1/view))
"You should be able to extend a classes behavior, without modifying it."

A system should be open for extension, but closed for modification. For example, if you want to add a feature, you could do this by writing new code, without modifying the existing code. Robert Martin provides an updated definition of the initial one provided by (Meyer 1988).

#### **Liskov substitution principle** ([LSP](https://drive.google.com/file/d/0BwhCYaYDn8EgNzAzZjA5ZmItNjU3NS00MzQ5LTkwYjMtMDJhNDU5ZTM0MTlh/view)) 
"Derived classes must be substitutable for their base classes."

Objects in a program should be replaceable with instances of their subclasses (or child classes or derived classes, whichever term you use for them) without altering the correctness of the program. So, you should be able to treat the child classes like their parent classes. As Robert Martin states, “Functions that use pointers or references to base classes must be able to use objects of derived classes without knowing it.”

#### **Interface segregation principle** ([ISP](https://drive.google.com/file/d/0BwhCYaYDn8EgOTViYjJhYzMtMzYxMC00MzFjLWJjMzYtOGJiMDc5N2JkYmJi/view)) 
"Make fine grained interfaces that are client specific."

Many specific interfaces are better than one general purpose interface. The idea of the interface here is having formalized lists of method names that a class can choose to support. And those lists of methods should be as small as possible and if they start to get bigger, then they should be split up into smaller interfaces as classes can choose to implement multiple smaller interfaces. A class should not have to support enormous lists of methods that it doesn’t need.

#### **Dependency Inversion Principle** ([DIP](https://drive.google.com/file/d/0BwhCYaYDn8EgMjdlMWIzNGUtZTQ0NC00ZjQ5LTkwYzQtZjRhMDRlNTQ3ZGMz/view)) 
"Depend on abstractions, not on concretions."

Depend on abstractions, not on concretions. This is the idea of not directly tying concrete objects together, but making them deal with abstractions in order to minimize dependencies between our objects. For example, if you have a high-level object, which depends on two low-level objects - you disconnect these two very concrete classes (i.e., remove the two low-level objects that are very specific), so you remove those connections and then insert a new layer between them, which might be abstract classes that are more basic. Therefore, it allows substitutions and flexibility. You can replace and extend the object. 

Using some or all of the above principles can help keep your code maintainable, testable, and it can reduce the complexity. That's a win-win result in my book!
