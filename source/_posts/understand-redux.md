---
title: Understand Redux
date: 2018-06-22 16:16:09
categories: 
    - front end
tags: 
    - redux
---
## In a word
> Redux keeps the state of your app in a single store. Then, you can **extract parts of that state and plug it into your components as props**. This lets you **keep data in one global place** (the store) and feed it directly to any component in the app, without the gymnastics of passing props down multiple levels.

## Relation Chart
<!-- more -->
![](redux工作原理.png)
&nbsp;
&nbsp;
## About Action:
Actions are *payloads of information that send data from your application to your store*. They are the only source of information for the store. You send them to the store using store.dispatch().
```
const ADD_TODO = 'ADD_TODO'
{
    type: ADD_TODO,
    payload: 'Learn Redux'
}
```
payload: data for request

Action creators are exactly that—**functions** that create actions.
```
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```
The dispatch() function can be accessed directly from the store as **store.dispatch()**, 
```
store.dispatch(addTodo('Learn Redux'));
```
but more likely you'll access it using a helper like react-redux's **connect()**.

&nbsp;
&nbsp;
## About Reducer
Reducers specify *how the application's state changes* in response to actions sent to the store. Remember that actions only describe what happened, but don't describe how the application's state changes.

**The reducer is a pure function that takes the previous state and an action, and returns the next state.**
```
(previousState, action) => newState
```
It's called a reducer because it's the type of function you would pass to **Array.prototype.reduce(reducer, ?initialValue)**
```
function todos(state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case TOGGLE_TODO:
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: !todo.completed
          })
        }
        return todo
      })
    default:
      return state
  }
}
​
function visibilityFilter(state = SHOW_ALL, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return action.filter
    default:
      return state
  }
}

```
When the app is larger, we can split the reducers into separate files and keep them completely independent and managing different data domains. Redux provides a utility called **combineReducers()** that does the same boilerplate logic that the todoApp above currently does.
```
import { combineReducers } from 'redux'
​
const todoApp = combineReducers({
  visibilityFilter,
  todos
})
​
export default todoApp
```
Note that this is equivalent to:
```
export default function todoApp(state = {}, action) {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
```
&nbsp;
&nbsp;

## About Store
In the previous sections, we defined the *actions* that represent the facts about “what happened” and the *reducers* that update the state according to those actions.

The **Store** is the object that brings them together. The store has the following responsibilities:
* Holds application state;
* Allows access to state via *getState()*;
* Allows state to be updated via *dispatch(action)*;
* Registers listeners via *subscribe(listener)*;
* Handles unregistering of listeners via the function returned by *subscribe(listener)*.

It's easy to create a store if you have a reducer. In the previous section, we used combineReducers() to combine several reducers into one. We will now import it, and pass it to **createStore()**.
```
import { createStore } from 'redux'
import todoApp from './reducers'
const store = createStore(todoApp)
```
现在我们有了store，那么我们怎么才能在我们的组件中对它们进行操作呢？connect就为提供了这个功能。它接受mapStateToProps, mapDispatchToProps等作为参数。比如在我的TodoList这个组件中需要用到todos这部分数据，那么我完善 **mapStateToProps** 这个函数，它接受store中的state作为参数，返回一个对象，属性就是state中我们需要的数据
```
const mapStateToProps = state => {
  return {
    todos: state.todos
  }
}
```
同样的，我们可能需要在组件中对state进行处理。**mapDispatchToProps** 就是帮助我们在组件中通过props调用dispatch来触发action的：
```
const mapDispatchToProps = dispatch => {
  return {
    onTodoClick: id => {
      dispatch(toggleTodo(id))
    }
  }
}
```
最后我们调用 **connect** 这个方法，将mapStateToProps， mapDispatchToProps生成的props注入到需要使用它的组件中:
```
const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```
这样，我们在TodoList这个组件中，就能直接通过props.todos获取到todos中的数据， 通过props.onTodoClick对todos进行处理。

上面我们调用connect时，在mapStateToProps 和 mapDispatchToProps我们分别用到了store的state和dispatch。但是在组件中的store是哪里凭空冒出来的呢？

**Provider** 就是来解决这个事的。**Provider** 使它的子孙在调用connect方法时，都能获取到store。
```
const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

const App = () => (
  <div>
    <AddTodo />
    <VisibleTodoList />
    <Footer />
  </div>
)

<Provider store={store}>
  <App />
</Provider>
```
&nbsp;
&nbsp;
*To be continued...*