h1. Element 'inview' Event Plugin

Event that is fired as soon as an element appears in the user's viewport.

*Author:* "Christopher Blum":http://twitter.com/ChristopherBlum
*Original idea and concept by:* "Remy Sharp":http://remysharp.com/2009/01/26/element-in-view-event-plugin/
*Forked from:* "https://github.com/zuk/jquery.inview/":https://github.com/zuk/jquery.inview/
*Demo* (loading lolcats when they scroll into view): "http://tifftiff.de/jquery.inview/example/live_event.html":http://tifftiff.de/jquery.inview/example/live_event.html
*Requires:* jQuery 1.4+

h2. Usage

The script makes use of the new $.contains method - so it will only work with jQuery 1.4 upwards. If you need to use it with older versions of jQuery, drop a comment, and I'll post an alternative.

The event will only fire when the element comes in to view of the viewport, and out of view. It won't keep firing if the user scrolls and the element remains in view.

The variable after the event argument indicates the visible state in the viewport.
The third variable visiblePartX detects which horizontal part of the element is visible to the user (possible values: left, right, both)
The fourth variable visiblePartY detects which vertical part of the element is visible to the user (possible values: top, bottom, both)

bc.. $('div').bind('inview', function(event, isInView, visiblePartX, visiblePartY) {
  if (isInView) {
    // element is now visible in the viewport
    if (visiblePartY == 'top') {
      // top part of element is visible
    } else if (visiblePartY == 'bottom') {
      // bottom part of element is visible
    } else {
      // whole part of element is visible
    }
  } else {
    // element has gone out of viewport
  }
});

p. To stop listening for the event - simply unbind:

bc.. $('div').unbind('inview');

p. Remember you can also bind once:

bc.. $('div').one('inview', fn);

h2. Live events

Yep, inview events can also be used with .live/.delegate methods.
_Please note that this could slow down your app when the selector is too complex and/or matches a huge set of elements._
The following code snippet only loads images when they appear in the browser's viewport.

bc..  // Assuming that all images have set the 'data-src' attribute instead of the 'src'attribute
  $("img[data-src]").live("inview", function() {
    var $this = $(this);
    $this.attr("src", $this.attr("data-src"));
    // Remove it from the set of matching elements in order to avoid that the handler gets re-executed
    $this.removeAttr("data-src");
  });

h2. More complex example

This way we can attach inview to some DIV in our code to, for example, detect when it FULLY readed by user (user sees it's bottom and top) and only after 1 seconds of view, so after we call some out stats function or whatever

bc.. $(someMyOneDiv).bind('inview', function(e, isInView, visiblePartX, visiblePartY) {
  var elem = $(this);

  if (elem.data('inviewtimer')) {
    clearTimeout(elem.data('inviewtimer'));
    elem.removeData('inviewtimer');
  }

  if (isInView) {
    elem.data('inviewtimer', setTimeout(function() {
      if (visiblePartY == 'top') {
        elem.data('seenTop', true);
      } else if (visiblePartY == 'bottom') {
        elem.data('seenBottom', true);
      } else {
        elem.data('seenTop', true);
        elem.data('seenBottom', true);
      }

      if (elem.data('seenTop') && elem.data('seenBottom')) {
        elem.unbind('inview');
        // here we will do WHAT WHE NEED (for ex. Call Ajax stats collector)
        ...
      }
    }, 1000));
  }
});

h2. Using offset

If you use this code for lazy loading images, sometimes you are interested to preload the image when it's getting close to the view port. So you can add a offset to the target element.

Add offset from 300px to all sites from the view port. Example: Your view port is 600x800 px, so the new one will be 1200x1400. All images in this area will be preloaded.

```html
<img data-offset="300">
```

Add offset from 300px to top and bottom of view port. Example: Your view port is 600x800 px, so the new one will be 600x1400.

```html
<img data-offset-top="300">
```

Add offset from 300px to left and right of the view port. Example: Your view port is 600x800 px, the new will be 1200x1400px.

```html
<img data-offset-left="300">
```

h2. How it works

An interval in the background checks the position of the elements against the viewport dimensions and the scroll position.

However, I wanted to create a utility that would only check the elements that were registered under the 'inview' event, i.e. I didn't want to keep checking the element list if we unbind from the event.

This is achieved by dipping in to the $.cache store within jQuery, and looping through, looking for the elements tied to the 'inview' event.

This way the user can treat it like a native event on the page.

h2. Use cases

* Reduce http requests and traffic on server by loading assets (images, javascript, html, ...) only when they are visible to the user
* Endless scrolling (twitter-like)
* Tracking (eg. to see whether a user has read an entire article)
* ...

h2. Browser Compatibility

h4. The Test Suite succeeds in the following browsers that were tested:

* Firefox 3+
* Safari 3+
* Chrome 7+
* Opera 10+
* IE 6+
* Mobile Safari on iPad 4.2.2
* Fennec 4b on Android 2.2
* Opera Mobile 10.1b on Android 2.2

h4. The Test Suite doesn't completely succeed but the demos in this repository work without problems in the following browsers:

* Mobile WebKit on Android 2.2
* Mobile WebKit on Palm Pre 1
