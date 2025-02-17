---
title: React v0.14 Beta 1
author: [sophiebits]
---

This week, many people in the React community are at [ReactEurope](https://www.react-europe.org/) in the beautiful (and very warm) city of Paris, the second React conference that's been held to date. At our last conference, we released the first beta of React 0.13, and we figured we'd do the same today with our first beta of React 0.14, giving you something to play with if you're not at the conference or you're looking for something to do on the way home.

With React 0.14, we're continuing to let React mature and to make minor changes as the APIs continue to settle down. I'll talk only about the two largest changes in this blog post; when we publish the final release we'll be sure to update all of our documentation and include a full changelog.

You can install the new beta with `npm install react@0.14.0-beta1` and `npm install react-dom@0.14.0-beta1`. As mentioned in [Deprecating react-tools](/blog/2015/06/12/deprecating-jstransform-and-react-tools.html), we're no longer updating the react-tools package so this release doesn't include a new version of it. Please try the new version out and let us know what you think, and please do file issues on our GitHub repo if you run into any problems.

## Two Packages {#two-packages}

As we look at packages like [react-native](https://github.com/facebook/react-native), [react-art](https://github.com/reactjs/react-art), [react-canvas](https://github.com/Flipboard/react-canvas), and [react-three](https://github.com/Izzimach/react-three), it's become clear that the beauty and essence of React has nothing to do with browsers or the DOM.

We think the true foundations of React are simply ideas of components and elements: being able to describe what you want to render in a declarative way. These are the pieces shared by all of these different packages. The parts of React specific to certain rendering targets aren't usually what we think of when we think of React. As one example, DOM diffing currently enables us to build React for the browser and make it fast enough to be useful, but if the DOM didn't have a stateful, imperative API, we might not need diffing at all.

To make this more clear and to make it easier to build more environments that React can render to, we're splitting the main `react` package into two: `react` and `react-dom`.

The `react` package contains `React.createElement`, `React.createClass` and `React.Component`, `React.PropTypes`, `React.Children`, and the other helpers related to elements and component classes. We think of these as the [_isomorphic_](http://nerds.airbnb.com/isomorphic-javascript-future-web-apps/) or [_universal_](https://medium.com/@mjackson/universal-javascript-4761051b7ae9) helpers that you need to build components.

The `react-dom` package contains `ReactDOM.render`, `ReactDOM.unmountComponentAtNode`, and `ReactDOM.findDOMNode`, and in `react-dom/server` we have server-side rendering support with `ReactDOMServer.renderToString` and `ReactDOMServer.renderToStaticMarkup`.

```js
var React = require('react');
var ReactDOM = require('react-dom');

var MyComponent = React.createClass({
  render: function () {
    return <div>Hello World</div>;
  },
});

ReactDOM.render(<MyComponent />, node);
```

We anticipate that most components will need to depend only on the `react` package, which is lightweight and doesn't include any of the actual rendering logic. To start, we expect people to render DOM-based components with our `react-dom` package, but there's nothing stopping someone from diving deep on performance and writing a `awesome-faster-react-dom` package which can render _the exact same DOM-based components_. By decoupling the component definitions from the rendering, this becomes possible.

More importantly, this paves the way to writing components that can be shared between the web version of React and React Native. This isn't yet easily possible, but we intend to make this easy in a future version so you can share React code between your website and native apps.

The addons have moved to separate packages as well: `react-addons-clone-with-props`, `react-addons-create-fragment`, `react-addons-css-transition-group`, `react-addons-linked-state-mixin`, `react-addons-pure-render-mixin`, `react-addons-shallow-compare`, `react-addons-transition-group`, and `react-addons-update`, plus `ReactDOM.unstable_batchedUpdates` in `react-dom`.

For now, please use the same version of `react` and `react-dom` in your apps to avoid versioning problems -- but we plan to remove this requirement later. (This release includes the old methods in the `react` package with a deprecation warning, but they'll be removed completely in 0.15.)

## DOM node refs {#dom-node-refs}

The other big change we're making in this release is exposing refs to DOM components as the DOM node itself. That means: we looked at what you can do with a `ref` to a DOM component and realized that the only useful thing you can do with it is call `this.refs.giraffe.getDOMNode()` to get the underlying DOM node. In this release, `this.refs.giraffe` _is_ the actual DOM node.

Refs to custom component classes work exactly as before.

```js
var Zoo = React.createClass({
  render: function () {
    return (
      <div>
        Giraffe's name: <input ref="giraffe" />
      </div>
    );
  },

  showName: function () {
    // Previously:
    // var input = this.refs.giraffe.getDOMNode();
    var input = this.refs.giraffe;

    alert(input.value);
  },
});
```

This change also applies to the return result of `ReactDOM.render` when passing a DOM node as the top component. As with refs, this change does not affect custom components (eg. `<MyFancyMenu>` or `<MyContextProvider>`), which remain unaffected by this change.

Along with this change, we're also replacing `component.getDOMNode()` with `ReactDOM.findDOMNode(component)`. The `findDOMNode` method drills down to find which DOM node was rendered by a component, but it returns its argument when passed a DOM node so it's safe to call on a DOM component too. We introduced this function quietly in the last release, but now we're deprecating `.getDOMNode()` completely: it should be easy to change all existing calls in your code to be `ReactDOM.findDOMNode`. We also have an [automated codemod script](https://www.npmjs.com/package/react-codemod) to help you with this transition. Note that the `findDOMNode` calls are unnecessary when you already have a DOM component ref (as in the example above), so you can (and should) skip them in most cases going forward.

We hope you're as excited about this release as we are! Let us know what you think of it.
