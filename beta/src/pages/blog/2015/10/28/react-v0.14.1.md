---
title: 'React v0.14.1'
author: [zpao]
---

After a couple weeks of having more people use v0.14, we're ready to ship a patch release addressing a few issues. Thanks to everybody who has reported issues and written patches!

The release is now available for download:

- **React**  
  Dev build with warnings: <https://fb.me/react-0.14.1.js>  
  Minified build for production: <https://fb.me/react-0.14.1.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-0.14.1.js>  
  Minified build for production: <https://fb.me/react-with-addons-0.14.1.min.js>
- **React DOM** (include React in the page before React DOM)  
  Dev build with warnings: <https://fb.me/react-dom-0.14.1.js>  
  Minified build for production: <https://fb.me/react-dom-0.14.1.min.js>

We've also published version `0.14.1` of the `react`, `react-dom`, and addons packages on npm and the `react` package on bower.

---

## Changelog {#changelog}

### React DOM {#react-dom}

- Fixed bug where events wouldn't fire in old browsers when using React in development mode
- Fixed bug preventing use of `dangerouslySetInnerHTML` with Closure Compiler Advanced mode
- Added support for `srcLang`, `default`, and `kind` attributes for `<track>` elements
- Added support for `color` attribute
- Ensured legacy `.props` access on DOM nodes is updated on re-renders

### React TestUtils Add-on {#react-testutils-add-on}

- Fixed `scryRenderedDOMComponentsWithClass` so it works with SVG

### React CSSTransitionGroup Add-on {#react-csstransitiongroup-add-on}

- Fix bug preventing `0` to be used as a timeout value

### React on Bower {#react-on-bower}

- Added `react-dom.js` to `main` to improve compatibility with tooling
