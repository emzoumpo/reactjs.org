---
title: 'React v0.12 RC'
author: [sebmarkbage]
---

We are finally ready to share the work we've been doing over the past couple months. A lot has gone into this and we want to make sure we iron out any potential issues before we make this final. So, we're shipping a Release Candidate for React v0.12 today. If you get a chance, please give it a try and report any issues you find! A full changelog will accompany the final release but we've highlighted the interesting and breaking changes below.

The release candidate is available for download:

- **React**  
  Dev build with warnings: <https://fb.me/react-0.12.0-rc1.js>  
  Minified build for production: <https://fb.me/react-0.12.0-rc1.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-0.12.0-rc1.js>  
  Minified build for production: <https://fb.me/react-with-addons-0.12.0-rc1.min.js>
- **In-Browser JSX transformer**  
  <https://fb.me/JSXTransformer-0.12.0-rc1.js>

We've also published version `0.12.0-rc1` of the `react` and `react-tools` packages on npm and the `react` package on bower.

## React Elements {#react-elements}

The biggest conceptual change we made in v0.12 is the move to React Elements. [We talked about this topic in depth earlier this week](/blog/2014/10/14/introducing-react-elements.html). If you haven't already, you should read up on the exciting changes in there!

## JSX Changes {#jsx-changes}

Earlier this year we decided to write [a specification for JSX](https://facebook.github.io/jsx/). This has allowed us to make some changes focused on the React specific JSX and still allow others to innovate in the same space.

### The `@jsx` Pragma is Gone! {#the-jsx-pragma-is-gone}

We have wanted to do this since before we even open sourced React. No more `/** @jsx React.DOM */`!. The React specific JSX transform assumes you have `React` in scope (which had to be true before anyway).

`JSXTransformer` and `react-tools` have both been updated to account for this.

### JSX for Function Calls is No Longer Supported {#jsx-for-function-calls-is-no-longer-supported}

The React specific JSX transform no longer transforms to function calls. Instead we use `React.createElement` and pass it arguments. This allows us to make optimizations and better support React as a compile target for things like Om. Read more in the [React Elements introduction](/blog/2014/10/14/introducting-react-elements.html).

The result of this change is that we will no longer support arbitrary function calls. We understand that the ability to do was a convenient shortcut for many people but we believe the gains will be worth it.

### JSX Lower-case Convention {#jsx-lower-case-convention}

We used to have a whitelist of HTML tags that got special treatment in JSX. However as new HTML tags got added to the spec, or we added support for more SVG tags, we had to go update our whitelist. Additionally, there was ambiguity about the behavior. There was always the chance that something new added to the tag list would result in breaking your code. For example:

```javascript
return <component />;
```

Is `component` an existing HTML tag? What if it becomes one?

To address this, we decided on a convention: **All JSX tags that start with a lower-case letter or contain a dash are treated as HTML.**

This means that you no longer have to wait for us to upgrade JSX to use new tags. This also introduces the possibility to consume custom elements (Web Components) - although custom attributes are not yet fully supported.

Currently we still use the whitelist as a sanity check. The transform will fail when it encounters an unknown tag. This allows you to update your code without hitting errors in production.

In addition, the HTML tags are converted to strings instead of using `React.DOM` directly. `<div/>` becomes `React.createElement('div')` instead of `React.DOM.div()`.

### JSX Spread Attributes {#jsx-spread-attributes}

Previously there wasn't a way to for you to pass a dynamic or unknown set of properties through JSX. This is now possible using the spread `...` operator.

```javascript
var myProps = {a: 1, b: 2};
return <MyComponent {...myProps} />;
```

This merges the properties of the object onto the props of `MyComponent`.

[Read More About Spread Attributes](https://gist.github.com/sebmarkbage/07bbe37bc42b6d4aef81)

If you used to use plain function calls to pass arbitrary props objects...

```javascript
return MyComponent(myProps);
```

You can now switch to using Spread Attributes instead:

```javascript
return <MyComponent {...myProps} />;
```

## Breaking Change: `key` and `ref` Removed From `this.props` {#breaking-change-key-and-ref-removed-from-thisprops}

The props `key` and `ref` were already reserved property names. This turned out to be difficult to explicitly statically type since any object can accept these extra props. It also screws up JIT optimizations of React internals in modern VMs.

These are concepts that React manages from outside the Component before it even gets created so it shouldn't be part of the props.

We made this distinction clearer by moving them off the props object and onto the `ReactElement` itself. This means that you need to rename:

`someElement.props.key` -> `someElement.key`

You can no longer access `this.props.ref` and `this.props.key` from inside the Component instance itself. So you need to use a different name for those props.

You do NOT need to change the way to define `key` and `ref`, only if you need to read it. E.g. `<div key="my-key" />` and `div({ key: 'my-key' })` still works.

## Breaking Change: Default Props Resolution {#breaking-change-default-props-resolution}

This is a subtle difference but `defaultProps` are now resolved at `ReactElement` creation time instead of when it's mounted. This is means that we can avoid allocating an extra object for the resolved props.

You will primarily see this breaking if you're also using `transferPropsTo`.

## Deprecated: transferPropsTo {#deprecated-transferpropsto}

`transferPropsTo` is deprecated in v0.12 and will be removed in v0.13. This helper function was a bit magical. It auto-merged a certain whitelist of properties and excluded others. It was also transferring too many properties. This meant that we have to keep a whitelist of valid HTML attributes in the React runtime. It also means that we can't catch typos on props.

Our suggested solution is to use the Spread Attributes.

```javascript
return <div {...this.props} />;
```

Or, just if you're not using JSX:

```javascript
return div(this.props);
```

Although to avoid passing too many props down, you'll probably want to use something like ES7 rest properties. [Read more about upgrading from transferPropsTo](https://gist.github.com/sebmarkbage/a6e220b7097eb3c79ab7).

## Deprecated: Returning `false` in Event Handlers {#deprecated-returning-false-in-event-handlers}

It used to be possible to return `false` from event handlers to preventDefault. We did this because this works in most browsers. This is a confusing API and we might want to use the return value for something else. Therefore, this is deprecated. Use `event.preventDefault()` instead.

## Renamed APIs {#renamed-apis}

As part of the [new React terminology](https://gist.github.com/sebmarkbage/fcb1b6ab493b0c77d589) we aliased some existing APIs to use the new naming convention:

- `React.renderComponent` -> `React.render`
- `React.renderComponentToString` -> `React.renderToString`
- `React.renderComponentToStaticMarkup` -> `React.renderToStaticMarkup`
- `React.isValidComponent` -> `React.isValidElement`
- `React.PropTypes.component` -> `React.PropTypes.element`
- `React.PropTypes.renderable` -> `React.PropTypes.node`

The older APIs will log a warning but work the same. They will be removed in v0.13.
