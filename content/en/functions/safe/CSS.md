---
title: safe.CSS
description: Declares the given string as safe CSS string.
categories: []
keywords: []
action:
  aliases: [safeCSS]
  related:
    - functions/safe/HTML
    - functions/safe/HTMLAttr
    - functions/safe/JS
    - functions/safe/JSStr
    - functions/safe/URL
  returnType: template.CSS
  signatures: [safe.CSS INPUT]
aliases: [/functions/safecss]
---

In this context, *safe* means CSS content that matches any of the following:

1. The CSS3 stylesheet production, such as `p { color: purple }`.
2. The CSS3 rule production, such as `a[href=~"https:"].foo#bar`.
3. CSS3 declaration productions, such as `color: red; margin: 2px`.
4. The CSS3 value production, such as `rgba(0, 0, 255, 127)`.

Example: Given `style = "color: red;"` defined in the front matter of your `.md` file:

* <span class="good">`<p style="{{ .Params.style | safeCSS }}">…</p>` &rarr; `<p style="color: red;">…</p>`</span>
* <span class="bad">`<p style="{{ .Params.style }}">…</p>` &rarr; `<p style="ZgotmplZ">…</p>`</span>

{{% note %}}
"ZgotmplZ" is a special value that indicates that unsafe content reached a CSS or URL context.
{{% /note %}}
