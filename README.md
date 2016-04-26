# Mobile Viewport Control

On mobile browsers, Apple set a non-standardized precedent of controlling the
viewport with one or more meta tags:

```html
<meta name="viewport" content="name=value,name=value,...">
```

> See Quirksmode for more context: [1], [2]

[1]:http://www.quirksmode.org/mobile/viewports2.html
[2]:http://www.quirksmode.org/mobile/metaviewport/

The goal of this project is to see if we can use these tags to dynamically
modify the viewport at runtime. Specifically, we want to freeze the viewport at
some zoom level, then restore the viewport later.

Generally, a user should be able to pinch-zoom a page.  But for things like
popup modals, the user experience is better if the viewport is preset like a
native app.

## Usage

```
npm install mobileViewportControl
```

```js
const viewport = require('mobileViewportControl');

// Freeze the viewport at a desired scale.
viewport.freeze(1.0, () => console.log("notified when frozen!"));

// Restore the viewport to what it was before freezing.
viewport.thaw(() => console.log("notified when thawed!"));
```

## Compatibility

Compatibility is measured with a combination of automatic/manual testing:

1. __[Measure Test]__ - verify that we can measure the current viewport scale.
1. __[Freeze Test]__ - verify that we can set and freeze the viewport scale.
  1. Manual step - pinch-zoom before starting the test
  1. Manual step - verify that you cannot pinch-zoom after the test
1. __[Thaw Test]__ - verify that we can restore the viewport scale and bounds.
  1. Manual step - pinch-zoom before starting the test
  1. Manual step - verify that you can still pinch-zoom after the test

| Mobile Browser             | [Measure Test]\* | [Freeze Test]                             | [Thaw Test]                                      |
|----------------------------|------------------|-------------------------------------------|--------------------------------------------------|
| iOS Safari                 | Y                | Y                                         | Fails if user does not manually zoom beforehand. |
| iOS UIWebView              | Y                | Fails if user manually zoomed beforehand. | Fails if user does not manually zoom beforehand. |
| iOS WKWebView              | Y                | Y                                         | Fails if user does not manually zoom beforehand. |
| iOS SFSafariViewController | Y                | Y                                         | Fails if user does not manually zoom beforehand. |
| iOS Chrome                 | Y                | Y                                         | Fails if user does not manually zoom beforehand. |
| iOS Firefox                | Y                | Y                                         | Fails if user does not manually zoom beforehand. |
| iOS Opera Mini             | Y                | Fails if user manually zoomed beforehand. | Fails.                                           |
| Android Browser (Stock)    |                  |                                           |                                                  |
| Android Chrome             |                  |                                           |                                                  |
| Android WebView            |                  |                                           |                                                  |
| Android Chrome Custom Tabs |                  |                                           |                                                  |
| Android Firefox            |                  |                                           |                                                  |
| Android Opera Mini         |                  |                                           |                                                  |

_\* This test fails in the iOS Simulator because initial-scale is ignored there for wide pages for some reason._

[Measure Test]:http://shaunstripe.github.io/mobileViewportControl/test/01-measure.html
[Freeze Test]:http://shaunstripe.github.io/mobileViewportControl/test/02-freeze.html
[Thaw Test]:http://shaunstripe.github.io/mobileViewportControl/test/03-thaw.html

### Variables

We currently do not test all variables, but the test outcomes depend on the following:

- browser/platform
- page width (modify in `test.css`)
- screen orientation (portrait or landscape)
- refresh delay (for viewport changes to take effect)
- initial zoom, influenced by either of:
  - default zoom specified in initial-scale
  - manual zoom remembered before page refresh
  - manual zoom after page load and before test run
- initial zoom bounds (controlled by page's original viewport meta tags)

