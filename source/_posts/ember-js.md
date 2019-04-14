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
To access the blog post's title you can simply write blogPost.title, whereas only the syntax ```blogPost.get('comments')``` will return the post's comments.

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