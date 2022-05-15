# Programming Paradigms Cheatsheet - JavaScript

This document includes informations on programming paradigms.\
Eventhough some explanations are shallow; due to the fact that at this time I'm involved in JavaScript mostly, there are some deeper informations and usages on functional programming and OOP since these are related to JavaScript and me.

---

Programming Paradigm is the style of the code: "how programmer write and organize code"

- [Imperative vs Declarative](#imperative-vs-declarative)
- [Imperative](#imperative)

  - [Procedural Programming](#procedural-programming)
  - [Parallel Processing Approach](#parallel-processing-approach)
  - [Object Oriented Programming](#object-oriented-programming)
    - [Prototypal Inheritance / Delegation](#prototypal-inheritance-/-delegation)
    - [OOP Inheritance](#oop-inheritance)
    - [Solid Principles](#solid-principles)

- [Declarative](#declarative)
  - [Functional Programming](#functional-programming)
  - [Database Processing Approach](#database-arocessing-approach)
  - [Logic Programming](#logic-programming)

## Imperative vs Declarative

**Imperative programming** is a programming paradigm that uses statements that change a program’s state. Clearly-defined sequence of instructions to a computer.\
**Declarative programming** is a programming paradigm that expresses the logic of a computation without describing its control flow.

**Imperative Programming** is like your friend calling your father that tells her how to fix your car step by step.\
**Declarative Programming** is like asking your friend to fix your car. You don’t care how to fix it, that’s up to her.

# Procedural Programming

Derived from imperative programming; deals with procedure calls, which are also known as routines or functions.\
Often, the terms "procedural programming" and "imperative programming" are used synonymously. However, procedural programming relies heavily on blocks and scope, whereas imperative programming as a whole may or may not have such features.

# Parallel Processing Approach

Break up different parts of a task among multiple processors, which simultaneously work on a problem.\
Many numbers of processor with the objective of running a program in less time by dividing them\
It’s like having someone make a sauce while you prepare the pizza base - you reduce the time it takes to complete the whole task.

# Object Oriented Programming

Organizes software design around data, or objects, rather than functions and logic.

**Abstraction:** Hiding details that don't matter.\
**Encapsulation:** Keeping things private so external code can't accidentally manipulate internal state. And no risk of breaking external code while changing internal implementation.\
_JS does not yet support **real** data privacy and encapsulation at this time. Developers mainly using underscore naming convention to know that it's wrong to access the property directly._\
**Inheritance:** Sharing properties and methods of certain class to child class.\
**Polymorphism:** Overwrite an inherited method from parent class.

_Traditional OOP consists of classes(blueprint which is used to create new objects) and instances. JS does not support \_real_ classes.\_

- **Constructor Functions:** Create objects from a function. (built-in objects: Arrays, Sets, Maps)
- **ES6 Classes:** Syntactic sugar. Works exactly like constructor functions.
- **Object.create():** Straight forward way to link an object to a prototype object.

## Prototypal Inheritance / Delegation

### **Constructor Functions**

Each object that's created by a certain constructor function, it will get access to all the methods and properties that is defined on the constructors prototype property.\
If a method or property can not be found in a certain object, JS will look into its prototype.

```
const Cat = function(name, age) {this.name = name; this.age = age}
console.log(new Cat('Simba', 1)) //returns kitty as Cat object.
// Constructor Function is called using the new keyword. 'this' keyword in constructor function call is set to the new object.
// name and age are instance properties.
```

_Never create methods inside constructor functions - bad practice;\
Each time an object is created using that constructor function, that method will be copied and carried around. Bad performance. Instead; use prototypes._

```
Cat.prototype.meow = function () {console.log('meow')}
Cat.prototype.species = 'animal'
```

Function _meow_ won't get duplicated every time an object is created.

```
// Constructor function has a prototype property.
ConstructorFunction.prototype  ->  Object.prototype.constructor
                                  (Prototype of created objects through constructor function)

// Prototype has a reference back to with the constructor property
ConstructorFunction            <-  Object.prototype.constructor
catObject.__proto__            ->  Cat.prototype
```

**Prototype Chain:** Ability to lookin up methods and properties in a prototype. And chain continues until the built-in methods/properties in JS. \
`catObj.hasOwnProperty() //chain is: catObj -> Cat -> Object(JS - Reference Type)`

> Always remember; JavaScript is a prototype-based language and this can lead to beautiful possibilites. Eventhough there are things that you should avoid;\
> `Array.prototype.unique = function () { return [...new Set(this)] }`\
> So `arr.unique()` will return unique values.\
> Not a good idea: First, next JS version can have some kind of method like this and that can break code; second, in big teams this can lead some bugs.

### **ES6 Classes**

_Syntactic sugar for constructor methods._

```
class Cat {
  constructor(name, age){this.name = name; this.age = age}
  meow(){console.log('meow')}
}
const simba = new Cat('Simba', 1)
```

All of the methods outside of constructor, will be on the prototype of the objects.\
`simba.__proto__ == Cat.prototype`, so this acts like constructor function with just a little bit nicer look. No need to mess with prototype property manually.

### **Object.create**

There are no prototype properties involved. So no constructor function and no 'new' operator.\
Manually set the prototype of an object to any other object. Link objects to other objects.\
Only the prototypes exits (eg. CatProto); there is nothing like 'Cat' function or class here.

```
const CatProto = {
  calcAge() {...}
  init(name, age) {this.name = name; this.age = age} // a common way to fake constructor
}
const smokeyCat = Object.create(CatProto) // set prototype of smokey cat explicityly
console.log(smokeyCat.__proto__) // gives CatProto
smokeyCat.init('Smokey', 3) // instance ready.
```

## OOP Inheritance

### **Constructor Functions**

Call inherited 'Animal' inside Cat constructor function while setting 'this'.\

```
const Cat = function(name, age, gender)
  {
    Animal.call(this, age, gender); // inherit Animal's properties.
    this.name = name;
  }
Cat.prototype = Object.create(Animal.prototype) // chain Animal's protoype
```

Do this assignment above before any other method creations, because it will return empty object and can overwrite already added methods.

```
catObj.__proto__          ->  Cat.prototype
Cat.prototype.__proto__   ->  Animal.prototype
```

### **ES6 Classes**

_Works exactly like above, just syntax changes_

```
class Cat extends Animal {
  constructor(name, age, gender)
  {
    super(age, gender)
    this.name = name;
  }
}
```

'Super' is basicly the constructor function of parent class, always first. Call to super function is responsible for creating 'this' keyword in the subclass. Without that, `this.name` is not available.

### **Object.create**

```
const AnimalProto = {...}
const CatProto = Object.create(AnimalProto) // Prototype chain is ready.
CatProto.init = function(name, age, gender){ // remember faking constructor function
  AnimalProto.init.call(this, age, gender)
  this.name = name;
}
const smokeyCat = Object.create(CatProto)
smokeyCat.init('Smokey', 3)
```

## Solid Principles

Principles intended to make software designs more understandable, flexible, and maintainable.

- **(S) Single-Responsibility Principle:** \
  A class should have only one job.
- **(O) Open-Closed Principle:** \
  Objects or entities should be open for extension but closed for modification. A class should be extendable without modifying the class itself.
- **(L) Liskov Substitution Principle:** \
  Every subclass or derived class should be substitutable for their base or parent class.
- **(I) Interface Segregation Principle:** \
  Distribute responsibilities across interfaces. Must prevent classes from relying on modules or functions that they dont need.
- **(D) Dependency Inversion Principle:** \
  Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions. _Abstractions must not depend on details. Details must depend on abstractions._

# Functional Programming

Writing software by combining many pure functions, while avoiding side effects and mutating data. Declarative rather than imperative code since this paradigm is a subclass.

**Side Effect:** A side effect is any application state change that is observable outside the called function other than its return value. Modification of any data outside of the function, logging to console, writing DOM, writing to a file etc..\
**Pure Function:** Func without side effects. Doesn't depend on external variables. Given the same inputs, always returns the same outputs - deterministic.\
**Immutability:** State (data) is never modified. Instead, copied and the copy is mutated and returned. Easier data flow tracking.

Abstract the control flow away by using the functional utilities.\
Create generic, reusable utilities that use higher order functions to act on many data types instead of methods that only operate on their colocated data.\
Expressions over statements

# Database Processing Approach

This programming methodology is based on data and its movement. Program statements are defined by data rather than hard-coding a series of steps. A database program provides file creation, data entry, update, query and reporting functions.

# Logic Programming

Program statements express facts and rules about problems within a system of formal logic. Rules are written as logical clauses with a head and a body; for instance, "H is true if B1, B2, and B3 are true." Facts are expressed similar to rules, but without a body; for instance, "H is true."

# Credits

I bring these informations together with the help of software community on internet. I do not claim that each sentence and analogy in this document belongs to me.

## I pursue giving what I get.
