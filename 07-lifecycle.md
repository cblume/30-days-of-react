---
page_id: 30-days-of-react/day-7
series: 30-days-of-react
permalink: day-7
title: Lifecycle Hooks
description: Today, we'll look through a few of the most common lifecycle hooks we can use with React components and we'll discuss why they are useful and when we should use them.
dayDir: '07'
hero_image: /assets/images/series/30-days-of-react/headings/7.jpg
imageUrl:  /assets/images/series/30-days-of-react/headings/7.jpg
introBannerUrl: '/assets/images/series/30-days-of-react/headings/7_wide.jpg'
date: Wed Oct 10 2016 21:29:42 GMT-0700 (PDT)
---

Congrats! We've made it to the end of the first week on React and we've already covered so much ground. We just finished working with stateful components to keep track of a component's internal state. Today, we're going to pause on implementation and talk a bit about how a component _lives_ in an application. That is, we'll talk about the component's lifecycle. 

As React mounts our application, it gives us some hooks where we can insert our own functionality at different times in the component's lifecycle. In order to _hook into_ the lifecycle, we'll need to define functions on our component which React calls at the appropriate time for each hook. Let's dive into the first lifecycle hook:

### `componentWillMount()` / `componentDidMount()`

When a component is defined on a page in our application, we can't depend upon it being available in the DOM immediately as we're defining virtual nodes. Instead, we have to wait until the component itself has actually been _mounted_ in the browser. For functionality that we need to run when it has been mounted, we get two different _hooks_ (or functions) we can define. One that is called just before the component is due to be mounted on the page and one that is called just after the component has been mounted.

> ### What does `mounting` mean?
>
> Since we're defining _virtual representations_ of nodes in our DOM tree with React, we're not actually defining DOM nodes. Instead, we're building up an in-memory view that React maintains and manages for us. When we talk about _mounting_, we're talking about the process of converting the virtual components into actual DOM elements that are placed in the DOM by React.

This is useful for things such as fetching data to populate the component. For instance, let's say that we want to use our activity tracker to display GitHub events. We will want to load these events only when the data itself is going to be rendered. 

Recall we defined our `Content` component in our activity list:

{lang=javascript}
<<[](content1.js)

Let's update the `Content` component to make a request to the [github.com events API](https://developer.github.com/v3/activity/events/) and use the response to display the activities. As such, we'll need to update the `state` of the object.

<div id="demo1"></div>

As we did yesterday, let's update our component to be stateful by setting `this.state` to an object in the constructor:

```javascript
class Content extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      activities: []
    }
  }

  // ...
}
```

Now, we'll want to make an HTTP request when the component itself is getting ready to be mounted (or just after it mounts). By defining the function `componentWillMount()` (or `componentDidMount()`) in our component, React runs the method just before it mounts in the DOM. This is a perfect spot for us to add a `GET` request.

Let's update the `Content` component with the request to the GitHub API. Since we'll only want to display a small list, let's take the latest four events. 

> We've stored a static JSON file of GitHub data that we'll load directly from source here using promises (we'll get back to making AJAX requests in a few days). For now, let's focus on how we'll implement updating our component with new data:

```javascript
class Content extends React.Component {
  // ...
  componentWillMount() {
    this.setState({activities: data});
  }
  // ...
}
```

Notice that we didn't change anything else from our `Content` component and it just works. 

### `componentWillUpdate()` / `componentDidUpdate()`

Sometimes we'll want to update some data of our component before or after we change the actual rendering. For instance, let's say we want to call a function to set up the rendering or call a function set when a component's props are changed. The `componentWillUpdate()` method is a reasonable hook to handle preparing our component for a change (as long as we don't call `this.setState()` to handle it as it will cause an infinite loop). 

Since we won't really need to handle this in-depth, we won't worry about setting up an example here, but it's good to know it exists. A more common lifecycle hook we'll use is the `componentWillReceiveProps()` hook.

### `componentWillReceiveProps()`

React will call a method when the component is about to receive new `props`. This is the first method that will be called when a component is going to receive a new set of props. As defining this method allows us to look for updates to specific `props`, it gives us an opportunity to calculate changes and update our component's internal state.

This is the time when we can update our state based on new props. 

> One thing to keep in mind here is that even though the `componentWillReceiveProps()` method gets called, the value of the `props` may not have changed. It's _always_ a good idea to check for changes in the prop values.

For instance, let's add a _refresh_ button to our activity list, so our users can request a rerequest of the GitHub events API. 

<div id="demo2"></div>

We'll use the `componentWillReceiveProps()` hook to ask the component to reload its data. As our component is stateful, we'll want to refresh this state with new data, so we can't simply update the `props` in a component. We can use the `componentWillReceiveProps()` method to _tell_ the component we want a refresh. 

For instance, let's add a button on our containing element that passes a `requestRefresh` boolean prop to tell the `Content` component to refresh.

{lang=javascript}
<<[](container1.js)

Using this new `prop` (the `requestRefresh` prop), we can update the `activities` from our `state` object when it changes value. 

{lang=javascript}
<<[](content2.js)

## `componentWillUnmount()`

Before the component is unmounted, React will call out to the `componentWillUnmount()` callback. This is the time to handle any clean-up events we might need, such as clearing timeouts, clearing data, disconnecting websockets, etc. 

For instance, with our clock component we worked on last time, we set a timeout to be called every second. When the component is ready to unmount, we want to make sure we clear this timeout so our JavaScript doesn't continue running a timeout for components that don't actually exist.

<div id="clock"></div>

Recall that our `timer` component we built looks like this:

{lang=javascript}
<<[](timer1.js)

When our clock is going to be unmounted, we'll want to clear the timeout we create in the `setTimer()` function on the component. Adding the `componentWillUnmount()` function takes care of this necessary cleanup.

```javascript
class Clock extends React.Component {
  // ...
  componentWillUnmount() {
    if (this.timeout) {
      clearTimeout(this.timeout);
    }
  }
  // ...
}
```

These are a few of the lifecycle hooks we can interact with in the React framework. We'll be using these a lot as we build our React apps, so it's a good idea to be familiar with them, that they exist, and how to hook into the life of a component.

We did introduce one new concept in this post, which we glossed over: we added a callback on a component to be called from the child to its parent component. In the next section, we're going to look at how to define and document the `prop` API of a component for usage when sharing a component across teams and an application in general. 
