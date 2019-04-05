---
layout: post
title: Space Lover
category: work
tag: wordpress
excerpt: This WordPress plugin magically adds an extra space between Chinese characters and English letters / numbers / common punctuation marks
thumbnail: thumb/space-lover.png
heading-img: svg/space-lover.svg
heading-img-local: true
heading-img-width: 300
link: https://wordpress.org/plugins/corner-bracket-lover/
scheme-text: "#7d4f46"
scheme-link: "#ff4525"
scheme-hover: "#ff9d79"
scheme-code: "#ffb800"
scheme-bg-light: true
---

This plugin magically adds an extra space between Chinese characters and English letters / numbers / common punctuation marks. A must-have plugin if you're running a Chinese blog. This plugin also works fine with multisite enabled WordPress (aka. WordPress Mu).

No options, no additional database inserts, no stupid banners and shitty ads, install and go.

你客戶／合作夥伴不光文案寫的爛，而且還不會排版？Space Lover 可以幫到你。它會神奇的在中文、日文和英文、數字、符號之間自動加入空白，讓客戶網站的文案、文章看上去規範整潔。

無選項、無 MySQL 插入，無廣告，安裝即用。

覺得這招治標不治本？也可以試試 [Chinese Copywriting Guidelines](/note/chinese-copywriting-guidelines/)，讓你的客戶／合作夥伴學會如何正確的排版。

```bash
# Before
今天出去買菜花了5000元

# After
今天出去買菜花了 5000 元
```

<details>
  <summary>Compatible with multisite?</summary>
  <p>Yes.</p>
</details>

<details>
  <summary>Can I activate Space Lover and Space Lover Lite at the same time?</summary>
  <p>Yes.</p>
</details>

<details>
  <summary>Differences between Space Lover and Space Lover Lite?</summary>
  <ul>
    <li>The new Space Lover (since v1.0.5) uses PHP regex to replace output contents.</li>
    <li>Space Lover Lite just inserts one script to dynamic add spaces.</li>
    <li>Space Lover changes your post output in your themes, RSS output</li>
    <li>Space Lover Lite only changes what you see, the post output is untouched, so you’ll still get original post contents in your RSS feeds, however this method is slightly safer than Space Lover.</li>
  </ul>
</details>

<details>
  <summary>Space Lover 與 Space Lover Lite 的區別？</summary>
  <ul>
    <li>新版 Space Lover（v1.0.5）使用 PHP 的正則替換文章的輸出內容</li>
    <li>Space Lover Lite 只插入 JavaScript 腳本，動態添加空格</li>
    <li>Space Lover 會修改所有文章的輸出形式，例如用戶所看到的頁面，RSS 輸出等</li>
    <li>Space Lover Lite 只會改變您在網站上看到的形式，而文章本身的內容是沒有被修改過的，所以使用此擴展時，RSS 的輸出並不會產生變化，唯一的優點是，這種方法與 Space Lover 比較相對安全，不會有內容被錯誤替換的可能。</li>
  </ul>
</details>

If you love this plugin, please consider [buying me a cup of coffee]({{ '/donate/' | relative_url }}).

[Download](https://wordpress.org/plugins/space-lover/){: .download} it at WordPress.org
