---
title: "React v15.4.0"
author: [gaearon]
---

Today we are releasing React 15.4.0.

We didn't announce the [previous](https://github.com/facebook/react/blob/master/CHANGELOG.md#1510-may-20-2016) [minor](https://github.com/facebook/react/blob/master/CHANGELOG.md#1520-july-1-2016) [releases](https://github.com/facebook/react/blob/master/CHANGELOG.md#1530-july-29-2016) on the blog because most of the changes were bug fixes. However, 15.4.0 is a special release, and we would like to highlight a few notable changes in it.

### Separating React and React DOM {#separating-react-and-react-dom}

[More than a year ago](/blog/2015/09/10/react-v0.14-rc1.html#two-packages-react-and-react-dom), we started separating React and React DOM into separate packages. We deprecated `React.render()` in favor of `ReactDOM.render()` in React 0.14, and removed DOM-specific APIs from `React` completely in React 15. However, the React DOM implementation still [secretly lived inside the React package](https://www.reddit.com/r/javascript/comments/3m6wyu/found_this_line_in_the_react_codebase_made_me/cvcyo4a/).

In React 15.4.0, we are finally moving React DOM implementation to the React DOM package. The React package will now contain only the renderer-agnostic code such as `React.Component` and `React.createElement()`.

This solves a few long-standing issues, such as [errors](https://github.com/facebook/react/issues/7386) when you import React DOM in the same file as the [snapshot testing](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html) renderer.

**If you only use the official and documented React APIs you won't need to change anything in your application.**

However, there is a possibility that you imported private APIs from `react/lib/*`, or that a package you rely on might use them. We would like to remind you that this was never supported, and that your apps should not rely on internal APIs. The React internals will keep changing as we work to make React better.

Another thing to watch out for is that React DOM Server is now about the same size as React DOM since it contains its own copy of the React reconciler. We don't recommend using React DOM Server on the client in most cases.

### Profiling Components with Chrome Timeline {#profiling-components-with-chrome-timeline}

You can now visualize React components in the Chrome Timeline. This lets you see which components exactly get mounted, updated, and unmounted, how much time they take relative to each other.

<img src="../images/blog/react-perf-chrome-timeline.png" width="651" height="228" alt="React components in Chrome timeline" />

To use it:

1. Load your app with `?react_perf` in the query string (for example, `http://localhost:3000/?react_perf`).

2. Open the Chrome DevTools **Timeline** tab and press **Record**.

3. Perform the actions you want to profile. Don't record more than 20 seconds or Chrome might hang.

4. Stop recording.

5. React events will be grouped under the **User Timing** label.

Note that the numbers are relative so components will render faster in production. Still, this should help you realize when unrelated UI gets updated by mistake, and how deep and how often your UI updates occur.

Currently Chrome, Edge, and IE are the only browsers supporting this feature, but we use the standard [User Timing API](https://developer.mozilla.org/en-US/docs/Web/API/User_Timing_API) so we expect more browsers to add support for it.

### Mocking Refs for Snapshot Testing {#mocking-refs-for-snapshot-testing}

If you're using Jest [snapshot testing](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html), you might have had [issues](https://github.com/facebook/react/issues/7371) with components that rely on refs. With React 15.4.0, we introduce a way to provide mock refs to the test renderer. For example, consider this component using a ref in `componentDidMount`:

```js {5,11}
import React from 'react';

export default class MyInput extends React.Component {
  componentDidMount() {
    this.input.focus();
  }

  render() {
    return (
      <input
        ref={node => this.input = node}
      />
    );
  }
}
```

With snapshot renderer, `this.input` will be `null` because there is no DOM. React 15.4.0 lets us avoid such crashes by passing a custom `createNodeMock` function to the snapshot renderer in an `options` argument:

```js {5-12,16}
import React from 'react';
import MyInput from './MyInput';
import renderer from 'react-test-renderer';

function createNodeMock(element) {
  if (element.type === 'input') {
    return {
      focus() {},
    };
  }
  return null;
}

it('renders correctly', () => {
  const options = {createNodeMock};
  const tree = renderer.create(<MyInput />, options);
  expect(tree).toMatchSnapshot();
});
```

We would like to thank [Brandon Dail](https://github.com/Aweary) for implementing this feature.

You can learn more about snapshot testing in [this Jest blog post](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html).

---

## Installation {#installation}

We recommend using [Yarn](https://yarnpkg.com/) or [npm](https://www.npmjs.com/) for managing front-end dependencies. If you're new to package managers, the [Yarn documentation](https://yarnpkg.com/en/docs/getting-started) is a good place to get started.

To install React with Yarn, run:

```bash
yarn add react@15.4.0 react-dom@15.4.0
```

To install React with npm, run:

```bash
npm install --save react@15.4.0 react-dom@15.4.0
```

We recommend using a bundler like [webpack](https://webpack.js.org/) or [Browserify](http://browserify.org/) so you can write modular code and bundle it together into small packages to optimize load time.

Remember that by default, React runs extra checks and provides helpful warnings in development mode. When deploying your app, make sure to [compile it in production mode](/docs/installation.html#development-and-production-versions).

In case you don't use a bundler, we also provide pre-built bundles in the npm packages which you can [include as script tags](/docs/installation.html#using-a-cdn) on your page:

* **React**  
  Dev build with warnings: [react/dist/react.js](https://unpkg.com/react@15.4.0/dist/react.js)  
  Minified build for production: [react/dist/react.min.js](https://unpkg.com/react@15.4.0/dist/react.min.js)  
* **React with Add-Ons**  
  Dev build with warnings: [react/dist/react-with-addons.js](https://unpkg.com/react@15.4.0/dist/react-with-addons.js)  
  Minified build for production: [react/dist/react-with-addons.min.js](https://unpkg.com/react@15.4.0/dist/react-with-addons.min.js)  
* **React DOM** (include React in the page before React DOM)  
  Dev build with warnings: [react-dom/dist/react-dom.js](https://unpkg.com/react-dom@15.4.0/dist/react-dom.js)  
  Minified build for production: [react-dom/dist/react-dom.min.js](https://unpkg.com/react-dom@15.4.0/dist/react-dom.min.js)  
* **React DOM Server** (include React in the page before React DOM Server)  
  Dev build with warnings: [react-dom/dist/react-dom-server.js](https://unpkg.com/react-dom@15.4.0/dist/react-dom-server.js)  
  Minified build for production: [react-dom/dist/react-dom-server.min.js](https://unpkg.com/react-dom@15.4.0/dist/react-dom-server.min.js)

We've also published version `15.4.0` of the `react`, `react-dom`, and addons packages on npm and the `react` package on bower.

- - -

## Changelog {#changelog}

### React {#react}
* React package and browser build no longer "secretly" includes React DOM.  
  <small>([@sebmarkbage](https://github.com/sebmarkbage) in [#7164](https://github.com/facebook/react/pull/7164) and [#7168](https://github.com/facebook/react/pull/7168))</small>
* Required PropTypes now fail with specific messages for null and undefined.  
  <small>([@chenglou](https://github.com/chenglou) in [#7291](https://github.com/facebook/react/pull/7291))</small>
* Improved development performance by freezing children instead of copying.  
  <small>([@keyanzhang](https://github.com/keyanzhang) in [#7455](https://github.com/facebook/react/pull/7455))</small>

### React DOM {#react-dom}
* Fixed occasional test failures when React DOM is used together with shallow renderer.  
  <small>([@goatslacker](https://github.com/goatslacker) in [#8097](https://github.com/facebook/react/pull/8097))</small>
* Added a warning for invalid `aria-` attributes.  
  <small>([@jessebeach](https://github.com/jessebeach) in [#7744](https://github.com/facebook/react/pull/7744))</small>
* Added a warning for using `autofocus` rather than `autoFocus`.  
  <small>([@hkal](https://github.com/hkal) in [#7694](https://github.com/facebook/react/pull/7694))</small>
* Removed an unnecessary warning about polyfilling `String.prototype.split`.  
  <small>([@nhunzaker](https://github.com/nhunzaker) in [#7629](https://github.com/facebook/react/pull/7629))</small>
* Clarified the warning about not calling PropTypes manually.  
  <small>([@jedwards1211](https://github.com/jedwards1211) in [#7777](https://github.com/facebook/react/pull/7777))</small>
* The unstable `batchedUpdates` API now passes the wrapped function's return value through.  
  <small>([@bgnorlov](https://github.com/bgnorlov) in [#7444](https://github.com/facebook/react/pull/7444))</small>
* Fixed a bug with updating text in IE 8.  
  <small>([@mnpenner](https://github.com/mnpenner) in [#7832](https://github.com/facebook/react/pull/7832))</small>

### React Perf {#react-perf}
* When ReactPerf is started, you can now view the relative time spent in components as a chart in Chrome Timeline.  
  <small>([@gaearon](https://github.com/gaearon) in [#7549](https://github.com/facebook/react/pull/7549))</small>

### React Test Utils {#react-test-utils}
* If you call `Simulate.click()` on a `<input disabled onClick={foo} />` then `foo` will get called whereas it didn't before.  
  <small>([@nhunzaker](https://github.com/nhunzaker) in [#7642](https://github.com/facebook/react/pull/7642))</small>

### React Test Renderer {#react-test-renderer}
* Due to packaging changes, it no longer crashes when imported together with React DOM in the same file.  
  <small>([@sebmarkbage](https://github.com/sebmarkbage) in [#7164](https://github.com/facebook/react/pull/7164) and [#7168](https://github.com/facebook/react/pull/7168))</small>
* `ReactTestRenderer.create()` now accepts `{createNodeMock: element => mock}` as an optional argument so you can mock refs with snapshot testing.  
  <small>([@Aweary](https://github.com/Aweary) in [#7649](https://github.com/facebook/react/pull/7649) and [#8261](https://github.com/facebook/react/pull/8261))</small>
