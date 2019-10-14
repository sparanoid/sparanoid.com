---
layout: post
title: "CSS Variables Guide: Color Manipulation and Dark Mode"
category: note
css: |
  @media (prefers-color-scheme: dark) {
    :root {
      --text-color-l: 85%;
      --link-color-l: 68%;
      --bg-color-h: 230;
      --bg-color-l: 12%;
      --bg-color-s: 12%;
      --code-color-l: calc(var(--link-color-l) * 1);
    }
  }
---

Follow this guide, you will:

- Learn why use HSLa over HEX/RGB for better color manipulation
- Create dark mode for your app/website with CSS variables (custom properties) in a pleasant way
- How to migrate from spreading colors all over your stylesheets to a unified, reactive color system
- Easily extend your theme to "darker" mode, "pinky" mode, or any color tint you want

## Let's embrace HSLa color model

First, before I get started to create dark mode for my site, I deprecated all HEX and RGBa (ARGB) models with [HSLa](https://en.wikipedia.org/wiki/HSL_and_HSV). Why? The real appeal of the HSLa model is it's more intuitive, straightforward for humans to know what the color will be like when you change the H (hue), S (saturation), and L (lightness) values. Remember HEX or RGB colors? Will you tell `#6495ed` is cornflower blue? `rgb(75, 0, 130)` represents indigo? Are they light or dark colors? Their saturation? No, but HSLa will.


## Introducing Root Variables

Based on the HSLa model, I created a minimal, future-proof [CSS variables](https://caniuse.com/#feat=css-variables) framework based on HSLa. [Root Variables](https://sparanoid.com/lab/root-variables/); it is:

- Minimal variables defined: colors, typography, and layout helpers.
- HSL colors: all colors are HSL-based for more straightforward manipulation (before we have CSS Color Module Level 4 in hands).
- Flexible to extend: all variables can be overridden later in `:root` or in specific scopes.
- Dark mode ready: extend your existing app with `prefers-color-scheme: dark`, tweak colors with HSL, and you're ready to go.

This framework starts with 4 main color templates cover most of the color using on a page:

- `--text-color` for the main text. A generally dark color on a white page when the color scheme is light
- `--link-color` for hyperlinks. A vivid color compared to general text
- `--bg-color` for the page background. A white color by default
- `--code-color` for monospace code text. Just an alternative color with slightly adjusted hue compared to link color

If you look at the source code you will know how it works. Take the link color as an example:

I first define the color using HSL color space in native CSS custom properties (aka. CSS variables). This can help me change the value of it or manipulate it using CSS `calc()` function later:

```css
:root {
  --link-color-h: 211;
  --link-color-s: 100%;
  --link-color-l: 50%;
}
```

Since I have all the values HSL needs I can combine them into one variable:

```css
:root {
  --link-color-hsl: var(--link-color-h), var(--link-color-s), var(--link-color-l);
}
```

Then I can create more variables based on this HSL variable:

```css
:root {
  --link-color: hsl(var(--link-color-hsl));
  --link-color-5: hsla(var(--link-color-hsl), .05);
  --link-color-10: hsla(var(--link-color-hsl), .1);
  --link-color-20: hsla(var(--link-color-hsl), .2);
  --link-color-50: hsla(var(--link-color-hsl), .5);
  --link-color-90: hsla(var(--link-color-hsl), .9);
}
```

You get it? this creates "variable chains" --- when `--link-color-h` updates; it will also updates `--link-color-hsl`. All colors based on these variables will also be updated automatically. It works more like traditional CSS preprocessors like Less and Sass, but it's executed in the browser without compiling thanks to CSS variables.

With this technique you can create more complex color variables with your imagination:

```css
:root {
  --link-color-light: hsl(var(--link-color-h), var(--link-color-s), calc(var(--link-color-l) / .9));
  --link-color-dark: hsl(var(--link-color-h), var(--link-color-s), calc(var(--link-color-l) * .9));
  /* just inverted from link-color's hue */
  --code-color-h: calc(var(--link-color-h) + 180);
}
```

## Create your own themes

The Root Variables framework is just too easy to use. You can just import it in your app and play around with the colors in your browser inspector to see how it works. If you like you can also create your own root variables based on this technique.

To make things simple, I'll continue with the Root Variables conventions for the dark mode guide.

After importing the Root Variables you will get several CSS variables in your `:root`. You can override them later in any CSS files, and the best practice is defining them in `:root` or `body`:

```css
/* app.css */
/* Override default color scheme */
:root {
  --text-color-l: 20%;
  --link-color-h: 240;
  --bg-color-s: 10%;
  --bg-color-l: 80%;
}
```

With the benefits of HSL color mode I can easily tweak the colors without using any visual color picker. I can open the browser inspector and change them to see color updates automatically.

That's all, I just created a new theme with lighter text, more bluish links, and a blush background.

## Welcome to the variables world!

Now preparing for the dark mode. You need to migrate all colors in your CSS with varibles you defined:

```diff
a {
- color: #6495ed;
+ color: var(--link-color);
}

a:hover {
- color: #6495ed;
+ color: var(--link-color-dark);
}

hr {
- border-top-color: #e3e1ea;
+ border-top-color: var(--text-color-10);
}
```

You can also create variables based on usage for more semantic:

```css
:root {
  --border: var(--text-color-10);
  --btn-hover: var(--link-color-dark);
}
```

This can be hard at the beginning, but when you've done this step you're really close to the dark mode.

## Let the magic happen

This is the final step for dark mode. Now you're smart enough to realize that dark mode is just another theme for specific devices that support it. You already know how to create a theme based on Root Variables, then you know how to create another theme for dark mode:

```css
/* Dark mode, that's all we need. */
:root {
  --text-color-s: 10%;
  --text-color-l: 70%;
  --link-color-l: 70%;
  --bg-color-l: 7%;
  --bg-color-s: 10%;
}
```

Let me explain:

- Set text color's saturation lower to 10% that will be pleasant for your eyes.
- I'm creating a "white text with black background" style so make text color lighter to 70% lightness.
- The lightness of links could also match the text color. Set it to 70%, too.
- Turn off the light for the background color, so make the lightness of it to 7%.
- The background shouldn't be too vivid, so set the saturation to 10%.

You can copy and paste the above snippet to Root Variables demo to see how it works.

Now, let's make magic happen: wrap them in `prefers-color-scheme: dark` media query, followed by your original variable overrides:

```css
/* app.css */
/* Override default color scheme */
:root {
  --text-color-l: 20%;
  --link-color-h: 240;
  --bg-color-s: 10%;
  --bg-color-l: 80%;
}

@media (prefers-color-scheme: dark) {
  :root {
    --text-color-s: 10%;
    --text-color-l: 70%;
    --link-color-l: 70%;
    --bg-color-l: 7%;
    --bg-color-s: 10%;
  }
}
```

That's all you need for dark mode! All your buttons, text, links, borders, input, modals, dropdowns, and etc. that you migrated from normal colors to variables will now update automatically when `prefers-color-scheme: dark` is true for supported devices.

## Further reading

- [Root Variables](https://sparanoid.com/lab/root-variables/) - The initial project
- [Almace Scaffolding](https://sparanoid.com/lab/amsf/) - Dark mode demo
- [OpenType Features in CSS](https://sparanoid.com/lab/opentype-features/) - Custom AMSF dark mode demo
- [Hyper.AI](https://hyper.ai/) - 3 color schemes all with dark mode support. Related stylesheets for dark mode less than 50 lines.
