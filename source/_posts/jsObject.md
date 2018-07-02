---
title: JavaScript Object Creation
date: 2018-06-08 11:24:52
categories: 
    - front end
tags: 
    - javascript
    - js learnNote
    - object
---
## Understanding Objects
>### Type of Properties
#### Data Properties
Data properties `contain a single location for a data value`. Data properties have four attributes describing their behavior:
<!-- more -->
* **\[[Configurable]]**: `default: true` Indicates if the property may be redefined by removing the property via delete, changing the property’s attributes, or changing the property into an accessor property.
* **\[[Enumerable]]**: `default: true` Indicates if the property will be returned in a for-in loop.
* **\[[Writable]]**: `default: true`  Indicates if the property’s value can be changed. 
* **\[[Value]]**: `default: undefin` Contains the actual data value for the property. This is the location from which the property’s value is read and the location to which new values are saved.

To change any of the default property attributes, you must use the ECMAScript 5 `Object.defineProperty()` method. This method accepts three arguments: the object on which the property should be added or modified, the name of the property, and a descriptor object. The properties on the descriptor object match the attribute names: *configurable*, *enumerable*, *writable*, and *value*.

```
    var person = {};
    Object.defineProperty(person, “name”, {
        writable: false,
        value: “Nicholas”
    });
    alert(person.name); //”Nicholas”
    person.name = “Greg”;
    alert(person.name); //”Nicholas”
```
```
    var person = {};
    Object.defineProperty(person, "name", {
        configurable: false,
        value: "Nicholas"
    });
    alert(person.name); //”Nicholas”
    delete person.name;
    alert(person.name); //”Nicholas”
```

#### Accessor Properties (访问器属性)
Accessor properties **do not** `contain a data value`. Instead, they contain a combination of a getter function and a setter function (though both are not necessary). When an accessor property is **read from**, the `getter` function is called, and it’s the function’s responsibility to return a valid value; when Understanding Objects an accessor property is **written to**, a `setter` function is called with the new value, and that function must decide how to react to the data. Accessor properties have four attributes:
* **\[[Configurable]]** `default: true`
* **\[[Enumerable]]** `default: true`
* **\[[Get]]** `default: undefined`
* **\[[Set]]** `default: undefined`

It is not possible to define an accessor property explicitly; you must use `Object.defineProperty()`
Here’s a simple example:
```
var book = {
    _year: 2004,
    edition: 1
};
Object.defineProperty(book, “year”, {
    get: function(){
        
    },
    set: function(newValue){
        if (newValue > 2004) {
            this._year = newValue;
            this.edition += newValue - 2004;
        }
    }
});
book.year = 2005;
alert(book.edition); //2
```
&nbsp;
&nbsp;
&nbsp;
## Object Creation
>### The Factory Pattern
```
    function createPerson(name, age, job){
        var o = new Object();
        o.name = name;
        o.age = age;
        o.job = job;
        o.sayName = function(){
            alert(this.name);
        };
        return o;
    }
    var person1 = createPerson(“Nicholas”, 29, “Software Engineer”);
    var person2 = createPerson(“Greg”, 27, “Doctor”);   
```
&nbsp;
&nbsp;
>### The Constructor Pattern
```
    function Person(name, age, job){
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = function(){
            alert(this.name);
        };
    }
    var person1 = new Person(“Nicholas”, 29, “Software Engineer”);
    var person2 = new Person(“Greg”, 27, “Doctor”);
```
#### features
* There is no object being created explicitly.
* The properties and method are assigned directly onto the *this* object.
* There is no return statement.

Calling a constructor in this manner essentially causes the following four steps to be taken:
1. Create a new object.
2. Assign the `this` value of the constructor to the new object (so this points to the new object).
3. Execute the code inside the constructor (adds properties to the new object).
4. Return the new object.

Each of the objects in this example is considered to be both an instance of Object and an instance of Person, as indicated by using the instanceof operator like this:
```
    alert(person1 instanceof Object); //true
    alert(person1 instanceof Person); //true
    alert(person2 instanceof Object); //true
    alert(person2 instanceof Person); //true
```
#### Constructors as Functions
The only *difference* between constructor functions and other functions is `the way in which they are called`.
Any function that is called with the `new operator` acts as a constructor.
```
    //use as a constructor
    var person = new Person(“Nicholas”, 29, “Software Engineer”);
    person.sayName(); //”Nicholas”

    //call as a function
    Person(“Greg”, 27, “Doctor”); //adds to window
    window.sayName(); //”Greg”

    //call in the scope of another object
    var o = new Object();
    Person.call(o, “Kristen”, 25, “Nurse”);
    o.sayName(); //”Kristen”
```
#### Two ways of define function in constructor
```
function Person(name, age, job){ 
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = new Function(“alert(this.name)”);  //logical equivalent
}

alert(person1.sayName == person2.sayName); //false
```
```
function Person(name, age, job){ this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}
function sayName(){
    alert(this.name);
}
var person1 = new Person(“Nicholas”, 29, “Software Engineer”); 
var person2 = new Person(“Greg”, 27, “Doctor”);
```

&nbsp;
&nbsp;
>### The Prototype Pattern
The benefit of using the prototype is that all of its properties and methods are shared among object instances. Instead of assigning object information in the constructor, they can be assigned directly to the prototype.
```
function Person(){ 
}

Person.prototype.name = “Nicholas”; 
Person.prototype.age = 29; 
Person.prototype.job = “Software Engineer”; 
Person.prototype.sayName = function(){
    alert(this.name); 
};

var person1 = new Person(); 
person1.sayName(); //”Nicholas”
var person2 = new Person();
person2.sayName(); //”Nicholas” 
alert(person1.sayName == person2.sayName); //true
```

the **isPrototypeOf()** method can be used to determine if this relationship exists between objects. Essentially, isPrototypeOf() returns true if \[[Prototype]] points to the prototype on which the method is being called, as shown here:
```
alert(Person.prototype.isPrototypeOf(person1)); //true
alert(Person.prototype.isPrototypeOf(person2)); //true
```
ECMAScript 5 adds a new method called **Object.getPrototypeOf()**, which returns the value of \[[Prototype]] in all supporting implementations. For example:
```
alert(Object.getPrototypeOf(person1) == Person.prototype); //true
alert(Object.getPrototypeOf(person1).name); //”Nicholas”
```
Whenever a property is accessed for reading on an object, a *search* is started to find a property with that name. The search begins on the **object instance itself**. If a property with the given name is found on the instance, then that value is returned; if the property is not found, then the search continues up the **pointer to the prototype**, and the prototype is searched for a property with the same name. If the property is found on the prototype, then that value is returned. 

Although it’s possible to read values on the prototype from object instances, it is **not possible** to overwrite them. If you add a property to an instance that has the same name as a property on the prototype, you create the property on the instance, which then **masks** the property on the prototype.
Here’s an example:
```
function Person(){
}

Person.prototype.name = “Nicholas”;
Person.prototype.age = 29;
Person.prototype.job = “Software Engineer”;
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
var person2 = new Person();
person1.name = “Greg”;
alert(person1.name); //”Greg” - from instance
alert(person2.name); //”Nicholas” - from prototype
```
Once a property is added to the object instance, it **shadows** any properties of the same name on the prototype, which means that **it blocks access to the property on the prototype without altering it**. Even setting the property to null only sets the property on the instance and doesn’t restore the link to the prototype. The delete operator, however, completely removes the instance property and allows the prototype property to be accessed again as follows:
```
delete person1.name;
alert(person1.name); //”Nicholas” - from the prototype
```
The **hasOwnProperty()** method determines if a property exists on the instance or on the prototype. This method, which is inherited from Object, returns true only if a property of the given name exists on the object instance, as in this example:
```
function Person(){
}

Person.prototype.name = “Nicholas”;
Person.prototype.age = 29;
Person.prototype.job = “Software Engineer”;
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

alert(person1.hasOwnProperty(“name”)); //false
person1.name = “Greg”;
alert(person1.name); //”Greg” - from instance
alert(person1.hasOwnProperty(“name”)); //true

delete person1.name;
alert(person1.name); //”Nicholas” - from the prototype
alert(person1.hasOwnProperty(“name”)); //false
```

#### Difference with 'in' Operator:
>Since the **in operator** always returns true *so long as the property is accessible by the object*, and **hasOwnProperty()** returns true *only if the property exists on the instance*, a prototype property can be determined if the in operator returns true but hasOwnProperty() returns false.

#### Point to which constructor?
the Person.prototype property is set equal to a new object created
with an object literal. The end result is the same, with one **exception**: *the constructor property no longer points to Person*. When a function is created, its prototype object is created and the constructor is automatically assigned. Essentially, this syntax overwrites the default prototype object completely, meaning that *the constructor property is equal to that of a completely new object (the Object constructor)* instead of the function itself. Although the instanceof operator still works reliably, you cannot rely on the constructor to indicate the type of object, as this example shows:
```
function Person(){
}

var friend = new Person();
alert(friend.constructor == Person); //true

Person.prototype = {
    name : “Nicholas”,
    age : 29,
    job : “Software Engineer”,
    sayName : function () {
        alert(this.name);
    }
};

var friend1 = new Person();
alert(friend1 instanceof Object); //true
alert(friend1 instanceof Person); //true
alert(friend1.constructor == Person); //false
alert(friend1.constructor == Object); //true
```
Here, instanceof still returns true for both Object and Person, but the constructor property is now equal to Object instead of Person. *If the constructor’s value is important, you can set it specifically back to the appropriate value*, as shown here:
```
function Person(){
}
Person.prototype = {
    constructor: Person,
    name : “Nicholas”,
    age : 29,
    job : “Software Engineer”,
    sayName : function () {
        alert(this.name);
    }
};
```
Keep in mind that restoring the constructor in this manner creates a property with *\[[Enumerable]] set to true*. Native constructor properties are not enumerable by default, so if you’re using an ECMAScript 5–compliant JavaScript engine, you may wish to use *Object.defineProperty()* instead:
```
function Person(){
}

Person.prototype = {
    name : “Nicholas”,
    age : 29,
    job : “Software Engineer”,
    sayName : function () {
        alert(this.name);
    }
};

//ECMAScript 5 only – restore the constructor
Object.defineProperty(Person.prototype, “constructor”, {
    enumerable: false,
    value: Person
});
```
#### Dynamic Nature of Prototypes
```
var friend= new Person();
Person.prototype.sayHi = function(){
    alert(“hi”);
};
friend.sayHi(); //”hi” - works!
```
In this code, an instance of Person is created and stored in friend. The next statement adds a method called sayHi() to Person.prototype. *Even though the friend instance was created prior to this change, it still has access to the new method.* This happens because of the loose link between the instance and the prototype. When friend.sayHi() is called, the instance is first searched for a property named sayHi; when it’s not found, the search continues to the prototype. Since the link between the instance and the prototype is simply a pointer, not a copy, the search finds the new sayHi property on the prototype and returns the function stored there.

The \[[Prototype]] pointer is assigned when the constructor is called, so *changing the prototype to a different object* severs the tie between the constructor and the original prototype. Remember: **the instance has a pointer to only the prototype, not to the constructor.** Consider the following:
```
function Person(){
}

var friend = new Person();

Person.prototype = {
    constructor: Person,
    name : “Nicholas”,
    age : 29,
    job : “Software Engineer”,
    sayName : function () {
        alert(this.name);
    }
};

friend.sayName(); //error
```

#### Problems with Prototypes
```
function Person(){
}

Person.prototype = {
    constructor: Person,
    name : “Nicholas”,
    age : 29,
    job : “Software Engineer”,
    friends : [“Shelby”, “Court”],
    sayName : function () {
        alert(this.name);
    }
};

var person1 = new Person();
var person2 = new Person();
person1.friends.push(“Van”);

alert(person1.friends); //”Shelby,Court,Van”
alert(person2.friends); //”Shelby,Court,Van”
alert(person1.friends === person2.friends); //true
```
Here, the Person.prototype object has a property called friends that contains an array of strings. Two instances of Person are then created. The person1.friends array is altered by adding another string. Because the friends array **exists on Person.prototype, not on person1**, the changes made are also reflected on person2.friends (which points to the same array).

&nbsp;
&nbsp;
>### Combination Constructor/Prototype Pattern
```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = [“Shelby”, “Court”];
}

Person.prototype = {
    constructor: Person,
    sayName : function () {
        alert(this.name);
    }
};

var person1 = new Person(“Nicholas”, 29, “Software Engineer”);
var person2 = new Person(“Greg”, 27, “Doctor”);
person1.friends.push(“Van”);

alert(person1.friends); //”Shelby,Court,Van”
alert(person2.friends); //”Shelby,Court”
alert(person1.friends === person2.friends); //false
alert(person1.sayName === person2.sayName); //true
```

&nbsp;
&nbsp;
>### Dynamic Prototype Pattern
```
function Person(name, age, job){
    //properties
    this.name = name;
    this.age = age;
    this.job = job;
    //methods
    if (typeof this.sayName != “function”){
        Person.prototype.sayName = function(){
            alert(this.name);
        };
    }
}

var friend = new Person(“Nicholas”, 29, “Software Engineer”);
friend.sayName();
```

&nbsp;
&nbsp;
>### Parasitic Constructor Pattern
```
function SpecialArray(){

    //create the array
    var values = new Array();

    //add the values
    values.push.apply(values, arguments);

    //assign the method
    values.toPipedString = function(){
    return this.join(“|”);
    };

    //return it
    return values;
}

var colors = new SpecialArray(“red”, “blue”, “green”);
alert(colors.toPipedString()); //”red|blue|green”
```

&nbsp;
&nbsp;
>### Durable Constructor Pattern
```
function Person(name, age, job){
    //create the object to return
    var o = new Object();

    //optional: define private variables/functions here
    //attach methods
    o.sayName = function(){
    alert(name);
    };

    //return the object
    return o;
}

var friend = Person(“Nicholas”, 29, “Software Engineer”);
friend.sayName(); //”Nicholas”
```

The person variable is a durable object, and there is *no way to access any of its data members without calling a method.* Even if some other code adds methods or data members to the object, there is **no way to access the original data that was passed into the constructor.** Such security makes the durable constructor pattern useful when dealing with secure execution environments.
