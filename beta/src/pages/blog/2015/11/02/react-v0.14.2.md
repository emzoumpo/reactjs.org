---
title: 'React v0.14.2'
author: [zpao]
---

We have a quick update following the release of 0.14.1 last week. It turns out we broke a couple things in the development build of React when using Internet Explorer. Luckily it was only the development build, so your production applications were unaffected. This release is mostly to address those issues. There is one notable change if consuming React from npm. For the `react-dom` package, we moved `react` from a regular dependency to a peer dependency. This will impact very few people as these two are typically installed together at the top level, but it will fix some issues with dependencies of installed components also using `react` as a peer dependency.

The release is now available for download:

- **React**  
  Dev build with warnings: <https://fb.me/react-0.14.2.js>  
  Minified build for production: <https://fb.me/react-0.14.2.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-0.14.2.js>  
  Minified build for production: <https://fb.me/react-with-addons-0.14.2.min.js>
- **React DOM** (include React in the page before React DOM)  
  Dev build with warnings: <https://fb.me/react-dom-0.14.2.js>  
  Minified build for production: <https://fb.me/react-dom-0.14.2.min.js>

We've also published version `0.14.2` of the `react`, `react-dom`, and addons packages on npm and the `react` package on bower.

---

## Changelog {#changelog}

### React DOM {#react-dom}

- Fixed bug with development build preventing events from firing in some versions of Internet Explorer & Edge
- Fixed bug with development build when using es5-sham in older versions of Internet Explorer
- Added support for `integrity` attribute
- Fixed bug resulting in `children` prop being coerced to a string for custom elements, which was not the desired behavior.
- Moved `react` from `dependencies` to `peerDependencies` to match expectations and align with `react-addons-*` packages
