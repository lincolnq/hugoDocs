---
title: Build options
description: Build options help define how Hugo must treat a given page when building the site.
categories: [content management,fundamentals]
keywords: [build,content,front matter, page resources]
menu:
  docs:
    parent: content-management
    weight: 70
weight: 70
toc: true
aliases: [/content/build-options/]
---

They are stored in a reserved front matter object named `_build` with the following defaults:

{{< code-toggle >}}
_build:
  render: always
  list: always
  publishResources: true
{{< /code-toggle >}}

#### render

If `always`, the page will be treated as a published page, holding its dedicated output files (`index.html`, etc...) and permalink.

Valid values are:

  - `never`
    : The page will not be included in any page collection.
  - `always (default)`
    : The page will be rendered to disk and get a `RelPermalink` etc.
  - `link`
    : The page will be not be rendered to disk, but will get a `RelPermalink`.

#### list

Valid values are:
  
  - `never`
    : The page will not be included in any page collection.
  - `always (default)`
    : The page will be included in all page collections, e.g. `site.RegularPages`, `.Pages`.
  - `local`
    : The page will be included in any _local_ page collection, e.g. `.RegularPages`, `.Pages`. One use case for this would be to create fully navigable, but headless content sections.

#### publishResources

If set to `true` (default) the [Bundle's Resources](/content-management/page-bundles) will be published.
Setting this to `false` will still publish Resources on demand (when a resource's `.Permalink` or `.RelPermalink` is invoked from the templates) but will skip the others.

{{% note %}}
Any page, regardless of their build options, will always be available using the [`.GetPage`](/methods/page/getpage) methods.
{{% /note %}}

### Illustrative use cases

#### Not publishing a page

Project needs a "Who We Are" content file for front matter and body to be used by the homepage but nowhere else.

{{< code-toggle file="content/who-we-are.md" fm=true >}}
title: Who we are
_build:
 list: false
 render: false
{{< /code-toggle >}}

{{< code file="layouts/index.html" >}}
<section id="who-we-are">
  {{ with site.GetPage "who-we-are" }}
    {{ .Content }}
  {{ end }}
</section>
{{< /code >}}

#### Listing pages without publishing them

Website needs to showcase a few of the hundred "testimonials" available as content files without publishing any of them.

To avoid setting the build options on every testimonials, one can use [`cascade`](/content-management/front-matter#front-matter-cascade) on the testimonial section's content file.

{{< code-toggle >}}
title: Testimonials
_build:
  render: true
cascade:
  _build:
    render: false
    list: true # default
{{< /code-toggle >}}

{{< code file="layouts/_defaults/testimonials.html" >}}
<section id="testimonials">
  {{ range first 5 .Pages }}
    <blockquote cite="{{ .Params.cite }}">
      {{ .Content }}
    </blockquote>
  {{ end }}
</section>
{{< /code >}}

#### Not rendering an index file for a hidden section

Website wants to hide a section from sitemap, menu and other navigation, but still wants to have all children rendered.

See the forum question: [Disable generating empty index.html pages created for sections](https://discourse.gohugo.io/t/disable-generating-empty-index-html-pages-created-for-sections/11036/1)

This use case leverages [`cascade`](/content-management/front-matter#front-matter-cascade) as well.

{{< code-toggle >}}
title: Section without index file
url: /hidden-section/
_build:
  render: never
  list: never
  publishResources: false
cascade:
  _build:
    list: false
    render: true
{{< /code-toggle >}}
