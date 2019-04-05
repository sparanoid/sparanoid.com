---
layout: post
title: Media Auto Hash Rename
category: work
tag: wordpress
excerpt: A WordPress plugin that renames media filename during upload with unique hash.
thumbnail: thumb/kai-series-thumb.png
heading-img: svg/media-auto-hash-rename.svg
heading-img-local: true
heading-img-width: 400
svg-headline-height: 86
css: |
  :root {
    --text-color-h: 262;
    --text-color-s: 100%;
    --link-color-h: 262;
    --bg-color-h: 262;
    --bg-color-s: 0%;
  }
---

Media Auto Hash Rename will rename any file (specific files can be ignored by file extension) during upload, with unique, low collision hashes. Hash characters and length can be configured for even lower collision rate.

This plugin provides no configuration GUI to make it more easier to maintain with the future WordPress updates.

Currently there're 3 constants you can configure in your `wp-config.php`, I recommend WP-CLI for maintaining these constants.

## `MAHR_LENGTH`

length of the random hashes, (default to `8`), longer can help reduce collision. Hashes at the length of 8 can be collision-free at the scale of 50,000 images).

## `MAHR_CHARS`

Characters used in hashes, default to `0123456789abcdefghijklmnopqrstuvwxyz_`, You can add more characters like uppercased alphabelts to dramatically reduce the collision without increasing the length of your filenames. But please note that this option can be dangerous if you're not familer with general URI encoding. So if you don't know what characters are allowed in a filename, just keep it untouched and use the default option.

## `MAHR_IGNORE`

File extensions to be ignored, default to `pdf, zip`, you can define a comma delimited list of file extensions to bypass renaming process of this plugin. All files that has the file extension you defined in the list will be ignored. Please note that:
  - File extensions must be defined without the leading peroid, for example: `'pdf, 7z, bmp'` works, `'.pdf, .7z, .bmp'` does not.
  - With (`'pdf, zip'`) or without (`'pdf,zip'`) space both work.
  - If you define your own ignore list, default ignore list will be discarded. For example, if you define `'7z'`, then `'pdf, zip'` will be processed during upload. You need to reapply these extensions with your own: `'7z, pdf, zip'`.
  - If you don't need to ignore any files by its extension. define an empty array `[]` (without quotes) to this option: `define( 'MAR_IGNORE', [] );` to force process PDF and ZIP files.

## FAQ

<details>
  <summary>What if I deactivate this plugin?</summary>
  <p>This pluigin, doesn't not write any extra data into your database. The files renamed by this plugin will still work after you deactivate this plugin.</p>
</details>

## Downloads

[Download](https://wordpress.org/plugins/media-auto-hash-rename/){: .download} it at WordPress.org
