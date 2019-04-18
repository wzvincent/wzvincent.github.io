---
title: Ember.js LearnNote
date: 2019-03-27 14:50:43
categories: 
    - front end
tags: 
    - javascript
    - js learnNote
    - ember.js
---
# Overview
![](ember-core-concepts.png)
### Router
<!-- more -->
When *url* is changed, the first thing that happens is that the Ember **router maps the URL to a route handler**.
The route handler then typically does two things:

* It renders a template.
* It loads a **model** that is then available to the **template**.
### Model
Models represent persistent state.
### Hooks
In Ember, we use the term *hook* for methods that are **automatically called within the Ember application**. These are methods that can be expected to be called automatically, rather than having to call them specifically.

In the following example, the **didRender()** component lifecycle hook is used to log "I rendered!" to the console after each time the component is rendered.

```
import Component from '@ember/component';

export default Component.extend({
  didRender() {
    this._super(...arguments);
    console.log('I rendered!');
  }
});
```

### Ember Object
Arrays and objects defined directly on any Ember.Object are shared across all instances of that class.

```
import { A } from '@ember/array';
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  shoppingList: A(['eggs', 'cheese'])
});

Person.create({
  name: 'Stefan Penner',
  addItem() {
    this.shoppingList.pushObject('bacon');
  }
});

Person.create({
  name: 'Robert Jackson',
  addItem() {
    this.shoppingList.pushObject('sausage');
  }
});

// Stefan and Robert both trigger their addItem.
// They both end up with: ['eggs', 'cheese', 'bacon', 'sausage']
```
To avoid this behavior, it is encouraged to initialize those arrays and object properties during init(). Doing so ensures each instance will be unique.

```
import { A } from '@ember/array';
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  init() {
    this.set('shoppingList', A(['eggs', 'cheese']));
  }
});

Person.create({
  name: 'Stefan Penner',
  addItem() {
    this.shoppingList.pushObject('bacon');
  }
});

Person.create({
  name: 'Robert Jackson',
  addItem() {
    this.shoppingList.pushObject('sausage');
  }
});

// Stefan ['eggs', 'cheese', 'bacon']
// Robert ['eggs', 'cheese', 'sausage']
```
### get() & set()
When reading a property value of an object, you can in most cases use the common JavaScript dot notation, *e.g. myObject.myProperty.*

**Ember proxy objects** are the one big exception to this rule. If you're working with Ember proxy objects, including promise proxies for async relationships in Ember Data, you have to use Ember's **get()** accessor method to read values.

Let's look at the following blogPost Ember Data model:

```
import Model from 'ember-data/model';
import attr from 'ember-data/attr';
import { hasMany } from 'ember-data/relationships';

export default Model.extend({
  title: attr('string'),
  body: attr('string'),
  comments: hasMany('comment', { async: true }),
});
```
To access the blog post's title you can simply write blogPost.title, whereas only the syntax **blogPost.get('comments')** will return the post's comments.

Always use Ember's **set()** method to update property values. It will propagate the value change to computed properties, observers, templates, etc. If you "just" use JavaScript's dot notation to update a property value, computed properties won't recalculate, observers won't fire and templates won't update.

```
import EmberObject from '@ember/object';

const Person = EmberObject.extend({
  name: 'Robert Jackson'
});

let person = Person.create();

person.name; // 'Robert Jackson'
person.set('name', 'Tobias Fünke');
person.name; // 'Tobias Fünke'
```

### reopen() method

You don't need to define a class all at once. You can reopen a class and define new properties using the *reopen()* method.
```
Person.reopen({
  isPerson: true
});

Person.create().get('isPerson'); // true
```

When using *reopen()*, you can also override existing methods and call *this._super*
```
Person.reopen({
  // override `say` to add an ! at the end
  say(thing) {
    this._super(thing + '!');
  }
});
```

*reopen()* is used to **add instance methods and properties** that are **shared across all instances** of a class. It does not add methods and properties to a particular instance of a class as in vanilla JavaScript (without using prototype).

But when you need to add static methods or static properties to the class itself you can use *reopenClass()*.
```
// add static property to class
Person.reopenClass({
  isPerson: false
});
// override property of existing and future Person instances
Person.reopen({
  isPerson: true
});

Person.isPerson; // false - because it is static property created by `reopenClass`
Person.create().get('isPerson'); // true
```
### Computed properties
In a nutshell, computed properties let you **declare functions as properties**. You create one by defining a computed property as a function, which Ember will automatically call when you ask for the property. You can then use it the same way you would any normal, static property.
```
import EmberObject, { computed } from '@ember/object';

Person = EmberObject.extend({
  // these will be supplied by `create`
  firstName: null,
  lastName: null,

  fullName: computed('firstName', 'lastName', function() {
    return `${this.firstName} ${this.lastName}`;
  })
});

let ironMan = Person.create({
  firstName: 'Tony',
  lastName:  'Stark'
});

ironMan.fullName; // "Tony Stark"
```
#### Computed properties only recompute when they are consumed
A computed property will only recompute its value when it is consumed. Properties are consumed in two ways:

- By being accessed, for example ironMan.fullName
- By being referenced in a handlebars template that is currently being rendered, for example {{ironMan.fullName}}
```
import Ember from 'ember';

…
  fullName: computed('firstName', 'lastName', function() {
    console.log('compute fullName'); // track when the property recomputes
    return `${this.firstName} ${this.lastName}`;
  })
…
```
```
let ironMan = Person.create({
  firstName: 'Tony',
  lastName:  'Stark'
});

ironMan.fullName; // 'compute fullName'
ironMan.set('firstName', 'Bruce') // no console output

ironMan.fullName; // 'compute fullName'
ironMan.fullName; // no console output since dependencies have not changed

```
#### Multiple dependents on the same object
####!!! important
In the previous example, the *fullName* computed property depends on two other properties of the same object.
However, you may find that you have to observe the properties of a different object.

For example, look at this computed property:
```
import EmberObject, { computed } from '@ember/object';

const Home = EmberObject.extend({
  location: {
    streetName: 'Evergreen Terrace',
    streetNumber: 742
  },

  address: computed('location.streetName', 'location.streetNumber', function() {
    return `${this.location.streetNumber} ${this.location.streetName}`;
  })
});

let home = Home.create()

home.address // 742 Evergreen Terrace
home.set('location.streetNumber', 744)
home.address // 744 Evergreen Terrace
```
It is important to observe an object's properties, not the object itself that has properties nested inside. If the object reference *location* is used as a dependent key, the computed property will not recalculate when the *streetName* or *streetNumber* properties change.
```
import EmberObject, { computed } from '@ember/object';

const Home = EmberObject.extend({
  location: {
    streetName: 'Evergreen Terrace',
    streetNumber: 742
  },

  address: computed('location', function() { // here is the difference
    return `${this.location.streetNumber} ${this.location.streetName}`;
  })
});

let home = Home.create()

home.address // 742 Evergreen Terrace
home.set('location.streetNumber', 744)
home.address // 742 Evergreen Terrace
home.set('location', {
  streetName: 'Evergreen Terrace',
  streetNumber: 744
})
home.address // 744 Evergreen Terrace
```

Since both *streetName* and *streetNumber* are properties on the location object, we can use a short-hand syntax called brace expansion to declare the dependents keys. You surround the dependent properties with braces (**{}**), and separate with commas, like so:
```
import EmberObject, { computed } from '@ember/object';

const Home = EmberObject.extend({
  location: {
    streetName: 'Evergreen Terrace',
    streetNumber: 742
  },

  address: computed('location.{streetName,streetNumber}', function() {
    return `${this.location.streetNumber} ${this.location.streetName}`;
  })
});
```
#### Description For Computed Properties
```
import EmberObject, { computed } from '@ember/object';

Person = EmberObject.extend({
  firstName: null,
  lastName: null,
  age: null,
  country: null,

  fullName: computed('firstName', 'lastName', function() {
    return `${this.firstName} ${this.lastName}`;
  }),

  // description
  description: computed('fullName', 'age', 'country', function() {
    return `${this.fullName}; Age: ${this.age}; Country: ${this.country}`;
  })
});

let captainAmerica = Person.create({
  firstName: 'Steve',
  lastName: 'Rogers',
  age: 80,
  country: 'USA'
});

captainAmerica.get('description'); // "Steve Rogers; Age: 80; Country: USA"

```
#### Dymamic Updating
Computed properties, by default, observe any changes made to the properties they depend on and are dynamically updated when they're called. Let's use computed properties to dynamically update.
```
captainAmerica.set('firstName', 'William');

captainAmerica.description; // "William Rogers; Age: 80; Country: USA"
```
So this change to *firstName* was observed by *fullName* computed property, which was itself observed by the *description* property.

Setting any dependent property will **propagate changes through any computed properties that depend on them**, all the way down the chain of computed properties you've created.

#### Setting Computed Properties
You can also define what Ember should do when setting a computed property. If you try to set a computed property, it will be invoked with the key (property name), and the value you want to set it to. You must return the new intended value of the computed property from the setter function.
```
import EmberObject, { computed } from '@ember/object';

Person = EmberObject.extend({
  firstName: null,
  lastName: null,

  fullName: computed('firstName', 'lastName', {
    get(key) {
      return `${this.firstName} ${this.lastName}`;
    },
    set(key, value) {
      let [firstName, lastName] = value.split(/\s+/);
      this.set('firstName', firstName);
      this.set('lastName',  lastName);
      return value;
    }
  })
});


let captainAmerica = Person.create();
captainAmerica.set('fullName', 'William Burnside');
captainAmerica.firstName; // William
captainAmerica.lastName; // Burnside
```
#### Computed Properties Macros
Some types of computed properties are very common. Ember provides a number of computed property macros, which are shorter ways of expressing certain types of computed property.

In this example, the two computed properties are equivalent:
```
import EmberObject, { computed } from '@ember/object';
import { equal } from '@ember/object/computed';

Person = EmberObject.extend({
  fullName: 'Tony Stark',

  isIronManLongWay: computed('fullName', function() {
    return this.fullName === 'Tony Stark';
  }),

  isIronManShortWay: equal('fullName', 'Tony Stark')
});
```
