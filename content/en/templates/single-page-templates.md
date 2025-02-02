---
title: Single page templates
description: The primary view of content in Hugo is the single view. Hugo will render every Markdown file provided with a corresponding single template.
categories: [templates]
keywords: [page, templates]
menu:
  docs:
    parent: templates
    weight: 50
weight: 50
toc: true
aliases: [/layout/content/]
---

## Single page template lookup order

See [Template Lookup](/templates/lookup-order/).

## Example single page templates

Content pages are of the type `page` and will therefore have all the [page variables][pagevars] and [site variables] available to use in their templates.

### `posts/single.html`

This single page template makes use of Hugo [base templates], the [`.Format` function] for dates, the [`.WordCount` page variable][pagevars], and ranges through the single content's specific [taxonomies][pagetaxonomy]. [`with`] is also used to check whether the taxonomies are set in the front matter.

{{< code file="layouts/posts/single.html" >}}
{{ define "main" }}
  <section id="main">
    <h1 id="title">{{ .Title }}</h1>
    <div>
      <article id="content">
        {{ .Content }}
      </article>
    </div>
  </section>
  <aside id="meta">
    <div>
      <section>
        <h4 id="date"> {{ .Date.Format "Mon Jan 2, 2006" }} </h4>
        <h5 id="wordcount"> {{ .WordCount }} Words </h5>
      </section>
      {{ with .GetTerms "topics" }}
        <ul id="topics">
          {{ range . }}
            <li><a href="{{ .RelPermalink }}">{{ .Title }}</a></li>
          {{ end }}
        </ul>
      {{ end }}
      {{ with .GetTerms "tags" }}
        <ul id="tags">
          {{ range . }}
            <li><a href="{{ .RelPermalink }}">{{ .Title }}</a></li>
          {{ end }}
        </ul>
      {{ end }}
    </div>
    <div>
      {{ with .PrevInSection }}
        <a class="previous" href="{{ .Permalink }}"> {{ .Title }}</a>
      {{ end }}
      {{ with .NextInSection }}
        <a class="next" href="{{ .Permalink }}"> {{ .Title }}</a>
      {{ end }}
    </div>
  </aside>
{{ end }}
{{< /code >}}

To easily generate new instances of a content type (e.g., new `.md` files in a section like `project/`) with preconfigured front matter, use [content archetypes][archetypes].

[archetypes]: /content-management/archetypes/
[base templates]: /templates/base/
[content type]: /content-management/types/
[directory structure]: /getting-started/directory-structure/
[dry]: https://en.wikipedia.org/wiki/Don%27t_repeat_yourself
[`.format` function]: /methods/time/format/
[front matter]: /content-management/front-matter/
[pagetaxonomy]: /templates/taxonomy-templates/#list-terms-assigned-to-a-page
[pagevars]: /variables/page/
[partials]: /templates/partials/
[section]: /content-management/sections/
[site variables]: /variables/site/
[spf13]: https://spf13.com/
[`with`]: /functions/go-template/with/
