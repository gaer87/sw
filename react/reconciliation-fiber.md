# [React](https://overreacted.io/react-as-a-ui-runtime)

Декларативное описание UI. Нет необходимости императивно описывать, что нужно сделать,
вместо этого описывается новое состояние UI, а React благодаря Reconciliation сделает всё сам)

## Термины
### Host Tree
React programs usually output a tree that may change over time.
It might be a DOM tree, aniOS hierarchy, a tree of PDF primitives, or even of JSON objects.
However, usually, we want to represent some UI with it. We’ll call it a “host tree” because 
it is a part of the host environment outside of React — like DOM or iOS.
The host tree usually has its own imperative API. React is a layer on top of it.

### Host Instances
The host tree consists of nodes. We’ll call them “host instances”. 
In the DOM environment, host instances are regular DOM nodes — like the objects you get
when you call document.createElement('div'). 

### Renderers
A renderer teaches React to talk to a specific host environment and manage its host instances.
React DOM, React Native, and even Ink are React renderers.

### React Elements
In the host environment, a host instance (like a DOM node) is the smallest building block.
In React, the smallest building block is a React element.
A React element is a plain **JavaScript object**.

![img_8.png](img_8.png)

A React element is lightweight and has no host instance tied to it.

Remember that React elements don’t have their own persistent identity.
They’re meant to be re-created and thrown away all the time.

React elements are immutable. For example, you can’t change the children or a property of a React element.
If you want to render something different later, you will describe it with a new React element tree created from scratch.

I like to think of React elements as being like frames in a movie.
They capture what the UI should look like at a specific point in time. They don’t change.

### Entry Point
React DOM entry point is `ReactDOM.render`

![img_9.png](img_9.png)

When we say ReactDOM.render(reactElement, domContainer),
we mean: “Dear React, make the domContainer host tree match my reactElement.”

React will look at the reactElement.type (in our example, 'button') and
ask the React DOM renderer to create a host instance for it and set the properties:

![img_10.png](img_10.png)

![img_11.png](img_11.png)

If the React element has child elements in reactElement.props.children,
React will recursively create host instances for them too on the first render.

## Reconciliation (Согласование)
![img.png](img.png)

React’s job is to make the host tree match the provided React element tree.
The process of figuring out what to do to the host instance tree in response to new information
is sometimes called reconciliation.

When a component’s props or state change, React decides whether an actual DOM update is necessary
by comparing the newly returned element with the previously rendered one.
When they are not equal, React will update the DOM. This process is called “reconciliation”.

Простой путь

![img_1.png](img_1.png)

But in DOM, this is slow and loses important information like focus, selection, scroll state, and so on.
Instead, we want React to do something like this:

![img_2.png](img_2.png)

This raises a question of identity. The React element may be different every time,
but when does it refer to the same host instance conceptually?

If an element type in the same place in the tree “matches up” between the previous and the next renders,
React reuses the existing host instance.

![img_3.png](img_3.png)

The same heuristic is used for child trees. For example, when we update a <dialog> with two <button>s inside,
React first decides whether to re-use the <dialog>, and then repeats this decision procedure for each child.

### Conditions

![img_4.png](img_4.png)

![img_5.png](img_5.png)

This is not great because conceptually the `<input>` hasn’t been replaced with `<p>` — it just moved.
We don’t want to lose its selection, focus state, and content due to re-creating the DOM.

In practice, you would rarely call ReactDOM.render directly.
Instead, React apps tend to be broken down into functions like this:

![img_6.png](img_6.png)

![img_7.png](img_7.png)

Regardless of whether showMessage is true or false,
the `<input>` is the second child and doesn’t change its tree position between renders.

### Lists
With dynamic lists, we can’t be sure the order is ever the same:

![img_12.png](img_12.png)

If the list of our shopping items is ever re-ordered,
React will see that all p and input elements inside have the same type,
and won’t know to move them. (From React’s point of view, the items themselves changed, not their order.)

So instead of re-ordering them, React would effectively update each of them.
This can create performance issues and possible bugs.
For example, the content of the first input would stay reflected in first input after the sort
— even though conceptually they might refer to different products in your shopping list!

This is why React nags you to specify a special property called key every time you include an array of elements in your output.

![img_13.png](img_13.png)

A key tells React that it should consider an item to be conceptually the same
even if it has different positions inside its parent element between renders.

When React sees <p key="42"> inside a <form>, it will check if the previous render also contained <p key="42"> inside the same <form>.
This works even if <form> children changed their order. React will reuse the previous host instance with the same key if it exists,
and re-order the siblings accordingly.