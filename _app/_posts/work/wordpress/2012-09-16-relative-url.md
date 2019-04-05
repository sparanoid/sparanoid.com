---
layout: post
title: Relative URL
category: work
tag: wordpress
excerpt: A WordPress plugin to apply wp_make_link_relative function to links to convert them to relative URLs.
thumbnail: thumb/relative-url.png
heading-img: svg/relative-url.svg
heading-img-local: true
heading-img-width: 400
link: https://wordpress.org/plugins/relative-url/
scheme-text: "#00fff0"
scheme-link: "#ff3f00"
scheme-hover: "#fffd4a"
scheme-code: "#39beb6"
scheme-bg: "#001311"
scheme-bg-light: true
---

**Relative URL** is a WordPress plugin to apply `wp_make_link_relative` function to links (posts, categories, pages and etc.) to convert them to relative URLs. Useful for developers when debugging local WordPress instance on a mobile device (iPad. iPhone, etc.).

This plugin should be used for local development only. I haven’t tested on a production environment; it **may** work with some issues, like unwanted URLs in RSS feed or sharing URLs are replaced with relative URLs, etc.


```bash
# Example:
http://localhost:8080/wp/

# Will be converted to:
/wp/

# And...
http://localhost:8080/wp/2012/09/01/hello-world/

# Will be converted to:
/wp/2012/09/01/hello-world/

# And...
http://localhost:8080/wp/wp-content/themes/twentyeleven/style.css

# Will be converted to:
/wp/wp-content/themes/twentyeleven/style.css
```

Then after activating this plugin, you can simply access your local instance using `http://10.0.1.5:8888/wp/` on your iPad or other mobile devices without having styles and navigation issue.

<details>
  <summary>What if I deactivate this plugin?</summary>
  <p>The URLs will be changed back to absolute URLs again, there’s no database writes with this plugin.</p>
</details>

<details>
  <summary>Why this plugin is not recommend for production environment?</summary>
  <p>URLs in RSS feed are also replaced to relative URLs with this plugin, this could causes some issues for RSS readers that they will be confused for URLs without host. Shared URLs (ie Jetpack Sharing module) are also replaced to related URLs, Twitter, Facebook or other social sites won’t treat them as valid URLs.</p>
</details>

If you love this plugin, please consider [buying me a cup of coffee]({{ '/donate/' | relative_url }}).

[Download](https://wordpress.org/extend/plugins/relative-url/){: .download} it at WordPress.org
