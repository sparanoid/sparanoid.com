---
layout: post
title: Lightense Images
category: work
tag: project
scheme-text: "#3b1599"
scheme-link: "#ff3f00"
scheme-hover: "#ff6300"
scheme-code: "#d6642a"
scheme-bg: "#fff"
scheme-list-color: "text"
plugin: lightense
---

A dependency-free pure JavaScript image zooming library less than 2 KB (gzipped). Inspired by [fat/zoom.js](https://github.com/fat/zoom.js) and [tholman/intense-images](https://github.com/tholman/intense-images).

This library is mainly used by [Almace Scaffolding](/lab/amsf/).

## Features

- High performance
- One script, no additional dependencies, no bloated styles
- Safari `backdrop-filter` support
- 2 KB gzipped

## Install

```shell
$ npm install lightense-images --save
```

- Or download library and save it to your project
- Or host it on a CDN and reference it via `<script></script>` tag

The Lightense library is wrapped in Universal Module Syntax (UMD), this means that out of the box, you can include it into your web application via `<script></script>` tag, `import`, or `require('lightense-images')`.

## Usage

Configuration object:

```js
Lightense(elements, {
  time: 300,
  padding: 40,
  offset: 40,
  keyboard: true,
  cubicBezier: 'cubic-bezier(.2, 0, .1, 1)',
  background: 'rgba(255, 255, 255, .98)',
  zIndex: 2147483647
});
```

Inline data configurations:

```html
<img src="image.png"
  data-lightense-padding="40"
  data-lightense-cubic-bezier="cubic-bezier(.2, 0, .1, 1)"
  data-lightense-background="rgba(255, 255, 255, .98)"
  data-lightense-z-index="2147483647">
```

## Setup

```html
<img src="photo.jpg">
<script>
  window.addEventListener('load', function () {
    Lightense('img');
  }, false);
</script>
```

<p><img src="{{ site.file }}/girls_dead_monster_logo.png"></p>

## Custom Background Color

```html
<img src="screenshot.png" data-lightense-background="rgba(0, 0, 0, .96)">
```

<p><img src="{{ site.file }}/railgun-logo.png" data-background="rgba(23, 29, 54, .8)"></p>

## Custom Padding

```html
<img src="screenshot.png" data-lightense-padding="0">
```

<p><img src="{{ site.file }}/delicious.com-logo.png" data-padding="0"></p>

## Disable Lightense for Specific Elements

```html
<img src="photo.jpg" class="no-lightense">
<script>
  window.addEventListener('load', function () {
    Lightense('img:not(.no-lightense)');
  }, false);
</script>
```

<p><img src="{{ site.file }}/imouto-logo-large.png" class="no-lightense"></p>

## Custom Event Hooks

```html
<img
  src="{{ site.file }}/girls_dead_monster_logo.png"
  before-show-alert="Showing!"
  after-show-alert="Showed!"
  before-hide-alert="Hiding!"
  after-hide-alert="Hidden!"
/>
<script>
  window.addEventListener("load", function() {
    Lightense("img:not(.no-lightense),.lightense", {
      beforeShow(config) {
        var beforeShowAttr = config.target.getAttribute("before-show-alert");
        beforeShowAttr && alert(beforeShowAttr);
      },
      afterShow(config) {
        var afterShowAttr = config.target.getAttribute("after-show-alert");
        afterShowAttr && alert(afterShowAttr);
      },
      beforeHide(config) {
        var beforeHideMessage = config.target.getAttribute("before-hide-alert");
        beforeHideMessage && alert(beforeHideMessage);
      },
      afterHide(config) {
        var afterHideMessage = config.target.getAttribute("after-hide-alert");
        afterHideMessage && alert(afterHideMessage);
      }
    });
  }, false);
</script>
```

## Download

<div class="largetype">
  <div><a href="https://github.com/sparanoid/lightense-images">Github</a></div>
</div>

## Browser Support

All modern browsers, it "should work" in Internet Explorer 10 and up as well.

## Love this?

Please consider [buying me a cup of coffee]({{ '/donate/' | relative_url }}).
