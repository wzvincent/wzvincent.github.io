---
title: JavaScript Object Inheritance
date: 2018-06-27 17:20:35
categories: 
    - front end
tags: 
    - javascript
    - js learnNote
    - object
    - inheritance
---
>### Prototype Chain

ECMAScript describes *prototype chaining* as the **primary method** of inheritance in ECMAScript.
Recall the relationship between **constructors**, **prototypes**, and **instances**: each constructor has a prototype object that points back to the constructor, and instances have an internal pointer to the prototype. What if the prototype were actually an instance of another type? That would mean the *prototype itself would have a pointer to a different prototype* that, in turn, would have a *pointer to another constructor*. **If that prototype were also an instance of another type, then the pattern would continue, forming a chain between instances and prototypes.** This is the basic idea behind prototype chaining.
<!-- more -->
Example:
```
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

//inherit from SuperType
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function (){
    return this.subproperty;
};

var instance = new SubType();
alert(instance.getSuperValue()); //true
```
![](prototype.jpeg)
A call to **instance.getSuperValue()** results in a three-step search: **1) the instance**, **2) SubType.prototype**, and **3) SuperType.prototype**, where the method is found. 

All reference types inherit from **Object** by default, which is accomplished through prototype chaining. The default prototype for any function is an instance of Object, meaning that its internal prototype pointer points to Object.prototype. This is how custom types inherit all of the default methods such as **toString()** and **valueOf()**.

&nbsp;
&nbsp;
> ### Constructor Stealing

Compare these two methods:
```
function SuperType(){
    this.colors = [“red”, “blue”, “green”];
}

function SubType(){
}

//inherit from SuperType
SubType.prototype = new SuperType();

var instance1 = new SubType();
instance1.colors.push(“black”);
alert(instance1.colors); //”red,blue,green,black”

var instance2 = new SubType();
alert(instance2.colors); //”red,blue,green,black”
```

```
function SuperType(){
this.colors = [“red”, “blue”, “green”];
}

function SubType(){
    //inherit from SuperType
    SuperType.call(this);
}

var instance1 = new SubType();
instance1.colors.push(“black”);
alert(instance1.colors); //”red,blue,green,black”

var instance2 = new SubType();
alert(instance2.colors); //”red,blue,green”
```
By using the call() method (or alternately, apply()), the **SuperType constructor** *is called in the context of the newly created instance of SubType*. Doing this effectively runs all of the object initialization code in the SuperType() function on the new SubType object. The result is that each instance has its own copy of the colors property.

You can pass some arguments using the second method
```
function SuperType(name){
    this.name = name;
}

function SubType(){
    //inherit from SuperType passing in an argument
    SuperType.call(this, “Nicholas”);

    //instance property
    this.age = 29;
}

var instance = new SubType();
alert(instance.name); //”Nicholas”;
alert(instance.age); //29
```
*Problems with Constructor Stealing*
The downside to using constructor stealing exclusively is that it introduces the same problems as the constructor pattern for custom types: methods must be defined inside the constructor, so there’s **no function reuse**. Furthermore, methods defined on the supertype’s prototype are not accessible on the subtype, so all types can use only the constructor pattern. Because of these issues, constructor stealing is rarely used on its own.
&nbsp;
&nbsp;
> ### Combinaton Inheritance

The basic idea is to use **prototype chaining to inherit properties and methods on the prototype** and to use **constructor stealing to inherit instance properties**. This allows function reuse by defining methods on the prototype and allows each instance to have its own properties.
```
function SuperType(name){
    this.name = name;
    this.colors = [“red”, “blue”, “green”];
}

SuperType.prototype.sayName = function(){
    alert(this.name);
};

function SubType(name, age){
    //inherit properties
    SuperType.call(this, name);
    this.age = age;
}

//inherit methods
SubType.prototype = new SuperType();
SubType.prototype.sayAge = function(){
    alert(this.age);
};

var instance1 = new SubType(“Nicholas”, 29);
instance1.colors.push(“black”);
alert(instance1.colors); //”red,blue,green,black”
instance1.sayName(); //”Nicholas”;
instance1.sayAge(); //29

var instance2 = new SubType(“Greg”, 27);
alert(instance2.colors); //”red,blue,green”
instance2.sayName(); //”Greg”;
instance2.sayAge(); //27
```
&nbsp;
&nbsp;
> ### Prototypal Inheritance

a method of inheritance that didn’t involve the use of strictly defined constructors.
```
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}

var person = {
    name: “Nicholas”,
    friends: [“Shelby”, “Court”, “Van”]
};

var anotherPerson = object(person);
anotherPerson.name = “Greg”;
anotherPerson.friends.push(“Rob”);

var yetAnotherPerson = object(person);
yetAnotherPerson.name = “Linda”;
yetAnotherPerson.friends.push(“Barbie”);

alert(person.friends); //”Shelby,Court,Van,Rob,Barbie”
```
In this example, the person object contains information that should be available on another object, so it is passed into the object() function, which returns a new object. The new object has person as its prototype, meaning that it has both a primitive value property and a reference value property on its prototype. This also means that person.friends is shared not only by person but also with anotherPerson and yetAnotherPerson. Effectively, this code has created two clones of person.

ECMAScript 5 formalized the concept of prototypal inheritance by adding the **Object.create()** method. This method accepts *two arguments*, an object to use as the prototype for a new object and Available for an optional object defining additional properties to apply to the new object. When used with one argument, Object.create() behaves the same as the object() method:
```
var person = {
    name: “Nicholas”,
    friends: [“Shelby”, “Court”, “Van”]
};

var anotherPerson = Object.create(person);
anotherPerson.name = “Greg”;
anotherPerson.friends.push(“Rob”);

var yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = “Linda”;
yetAnotherPerson.friends.push(“Barbie”);

alert(person.friends); //”Shelby,Court,Van,Rob,Barbie”
```

The **second argument for Object.create()** is in the same format as the **second argument for Object.defineProperties()**: each additional property to define is specified along with its descriptor. Any properties specified in this manner will shadow properties of the same name on the prototype object. For example:
```
var person = {
    name: “Nicholas”,
    friends: [“Shelby”, “Court”, “Van”]
};

var anotherPerson = Object.create(person, {
    name: {
    value: “Greg”
    }
});

alert(anotherPerson.name); //”Greg”
```
Prototypal inheritance is useful when *there is no need for the overhead of creating separate constructors, but you still need an object to behave similarly to another*. 

*When used with one argument, **Object.create()** behaves the same as the **object()** method*
&nbsp;
&nbsp;
> ### Parasitic Inheritance

```
function createAnother(original){
    var clone = object(original); //create a new object by calling a function
    clone.sayHi = function(){ //augment the object in some way
        alert(“hi”);
    };
    return clone; //return the object
}

var person = {
    name: “Nicholas”,
    friends: [“Shelby”, “Court”, “Van”]
};
var anotherPerson = createAnother(person);
anotherPerson.sayHi(); //”hi”                                    
```

&nbsp;
&nbsp;
> ### Parasitic Combination Inheritance

Combination inheritance is the most often-used pattern for inheritance in JavaScript, though it is not without its inefficiencies. The most inefficient part of the pattern is that the **supertype constructor is always called twice**: once to create the subtype’s prototype, and once inside the subtype constructor.
```
function SuperType(name){
    this.name = name;
    this.colors = [“red”, “blue”, “green”];
}
SuperType.prototype.sayName = function(){
    alert(this.name);
};

function SubType(name, age){
    SuperType.call(this, name); //second call to SuperType()
    this.age = age;
}
SubType.prototype = new SuperType(); //first call to SuperType()
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    alert(this.age);
};
```
Parasitic combination inheritance uses constructor stealing to inherit properties but uses a hybrid form of prototype chaining to inherit methods. The basic idea is this: instead of calling the supertype constructor to assign the subtype’s prototype, all you need is a copy of the supertype’s prototype. Essentially, use parasitic inheritance to inherit from the supertype’s prototype and then assign the result to the subtype’s prototype. The basic pattern for parasitic combination inheritance is as follows:
```
function inheritPrototype(subType, superType){
    var prototype = object(superType.prototype); //create object
    prototype.constructor = subType; //augment object
    subType.prototype = prototype; //assign object
}

function SuperType(name){
    this.name = name;
    this.colors = [“red”, “blue”, “green”];
}

SuperType.prototype.sayName = function(){
    alert(this.name);
};

function SubType(name, age){
    SuperType.call(this, name);
    this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function(){
    alert(this.age);
};
```
Parasitic combination inheritance is considered the most optimal inheritance paradigm for reference types.