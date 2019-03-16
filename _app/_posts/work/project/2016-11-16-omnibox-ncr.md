---
layout: post
title: Omnibox NCR
category: work
tag: project
head: |
  <link rel="chrome-webstore-item" href="https://chrome.google.com/webstore/detail/kohddgnpofoogkkjejnmcgleamcfbhhc">
---

## Intro

This extension, will force using google.com (aka Google No Country Redirect) when you search in Google Chrome Omnibox.

If you travel a lot, or you just care about privacy and use proxy to hide your real IP from another country, imagine you're in China and using a Japanese proxy, Google always redirects you to google.co.jp when you search in Google Chrome Omnibox. This extension could definitely make your browser stick with google.com.

## Under the Hood

This extension simply replace the URL you request before `onBeforeRequest` to google.com, no additional internet request will be made since the replace process will be done in 307 Internal Redirect.

## Downloads

<div class="largetype">
  <div><a href="https://chrome.google.com/webstore/detail/kohddgnpofoogkkjejnmcgleamcfbhhc">Add to Chrome</a></div>
  <div><a href="https://github.com/sparanoid/omnibox-ncr">View source at GitHub</a></div>
</div>

## Love this?

Please consider [buying me a cup of coffee]({{ '/donate/' | relative_url }}).
