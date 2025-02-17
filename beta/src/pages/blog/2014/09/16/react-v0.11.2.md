---
title: React v0.11.2
author: [zpao]
---

Today we're releasing React v0.11.2 to add a few small features.

We're adding support for two more DOM elements, `<dialog>` and `<picture>`, as well as the associated attributes needed to use these elements: `open`, `media`, and `sizes`. While not all browsers support these natively, some of our cutting edge users want to make use of them, so we're making them available to everybody.

We're also doing some work to prepare for v0.12 and improve compatibility between the versions. To do this we are replacing `React.createDescriptor` with `React.createElement`. `createDescriptor` will continue to work with a warning and will be gone in v0.12. Chances are that this won't affect anybody.

And lastly, on the heels of announcing Flow at [@Scale](http://atscaleconference.com/) yesterday, we're adding the ability to strip TypeScript-like type annotations as part of the `jsx` transform. To use, simply use the `--strip-types` flag on the command line, or set `stripTypes` in the `options` object when calling the API. We'll be talking about Flow more in the coming months. But for now, it's helpful to know that it is a flow-sensitive JavaScript type checker we will be open sourcing soon.

The release is available for download from the CDN:

- **React**  
  Dev build with warnings: <https://fb.me/react-0.11.2.js>  
  Minified build for production: <https://fb.me/react-0.11.2.min.js>
- **React with Add-Ons**  
  Dev build with warnings: <https://fb.me/react-with-addons-0.11.2.js>  
  Minified build for production: <https://fb.me/react-with-addons-0.11.2.min.js>
- **In-Browser JSX transformer**  
  <https://fb.me/JSXTransformer-0.11.2.js>

We've also published version `0.11.2` of the `react` and `react-tools` packages on npm and the `react` package on bower.

Please try these builds out and [file an issue on GitHub](https://github.com/facebook/react/issues/new) if you see anything awry.

### React Core {#react-core}

#### New Features {#new-features}

- Added support for `<dialog>` element and associated `open` attribute
- Added support for `<picture>` element and associated `media` and `sizes` attributes
- Added `React.createElement` API in preparation for React v0.12
  - `React.createDescriptor` has been deprecated as a result

### JSX {#jsx}

- `<picture>` is now parsed into `React.DOM.picture`

### React Tools {#react-tools}

- Update `esprima` and `jstransform` for correctness fixes
- The `jsx` executable now exposes a `--strip-types` flag which can be used to remove TypeScript-like type annotations
  - This option is also exposed to `require('react-tools').transform` as `stripTypes`
