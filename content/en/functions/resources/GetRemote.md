---
title: resources.GetRemote
description: Returns a remote resource from the given URL, or nil if none found.
categories: []
keywords: []
action:
  aliases: []
  related:
    - functions/data/GetCSV
    - functions/data/GetJSON
    - functions/resources/ByType
    - functions/resources/Get
    - functions/resources/GetMatch
    - functions/resources/Match
    - methods/page/Resources
  returnType: resource.Resource
  signatures: ['resources.GetRemote URL [OPTIONS]']
toc: true
---

```go-html-template
{{ $url := "https://example.org/images/a.jpg" }}
{{ with resources.GetRemote $url }}
  {{ with .Err }}
    {{ errorf "%s" . }}
  {{ else }}
    <img src="{{ .RelPermalink }}" width="{{ .Width }}" height="{{ .Height }}" alt="">
  {{ end }}
{{ else }}
  {{ errorf "Unable to get remote resource %q" $url }}
{{ end }}
```

## Options

The `resources.GetRemote` function takes an optional map of options.

```go-html-template
{{ $url := "https://example.org/api" }}
{{ $opts := dict
  "headers" (dict "Authorization" "Bearer abcd")
}}
{{ $resource := resources.GetRemote $url $opts }}
```

If you need multiple values for the same header key, use a slice:

```go-html-template
{{ $url := "https://example.org/api" }}
{{ $opts := dict
  "headers" (dict "X-List" (slice "a" "b" "c"))
}}
{{ $resource := resources.GetRemote $url $opts }}
```

You can also change the request method and set the request body:

```go-html-template
{{ $url := "https://example.org/api" }}
{{ $opts := dict
  "method" "post"
  "body" `{"complete": true}` 
  "headers" (dict  "Content-Type" "application/json")
}}
{{ $resource := resources.GetRemote $url $opts }}
```

## Remote data

When retrieving remote data, use the [`transform.Unmarshal`] function to unmarshal the response to a map.

[`transform.Unmarshal`]: /functions/transform/unmarshal

```go-html-template
{{ $data := "" }}
{{ $url := "https://example.org/books.json" }}
{{ with resources.GetRemote $url }}
  {{ with .Err }}
    {{ errorf "%s" . }}
  {{ else }}
    {{ $data = . | transform.Unmarshal }}
  {{ end }}
{{ else }}
  {{ errorf "Unable to get remote resource %q" $url }}
{{ end }}
```

## Error handling

The [`Err`] method on a resource returned by the `resources.GetRemote` function returns an error message if the HTTP request fails, else nil. If you do not handle the error yourself, Hugo will fail the build.

[`Err`]: /methods/resource/err

```go-html-template
{{ $url := "https://broken-example.org/images/a.jpg" }}
{{ with resources.GetRemote $url }}
  {{ with .Err }}
    {{ errorf "%s" . }}
  {{ else }}
    <img src="{{ .RelPermalink }}" width="{{ .Width }}" height="{{ .Height }}" alt="">
  {{ end }}
{{ else }}
  {{ errorf "Unable to get remote resource %q" $url }}
{{ end }}
```

To log an error as a warning instead of an error:

```go-html-template
{{ $url := "https://broken-example.org/images/a.jpg" }}
{{ with resources.GetRemote $url }}
  {{ with .Err }}
    {{ warnf "%s" . }}
  {{ else }}
    <img src="{{ .RelPermalink }}" width="{{ .Width }}" height="{{ .Height }}" alt="">
  {{ end }}
{{ else }}
  {{ errorf "Unable to get remote resource %q" $url }}
{{ end }}
```

## HTTP response

The [`Data`] method on a resource returned by the `resources.GetRemote` function returns information from the HTTP response.

[`Data`]: /methods/resource/data

```go-html-template
{{ $url := "https://example.org/images/a.jpg" }}
{{ with resources.GetRemote $url }}
  {{ with .Err }}
    {{ errorf "%s" . }}
  {{ else }}
    {{ with .Data }}
      {{ .ContentLength }} → 42764
      {{ .ContentType }} → image/jpeg
      {{ .Status }} → 200 OK
      {{ .StatusCode }} → 200
      {{ .TransferEncoding }} → []
    {{ end }}
  {{ end }}
{{ else }}
  {{ errorf "Unable to get remote resource %q" $url }}
{{ end }}
```

ContentLength
: (`int`) The content length in bytes.

ContentType
: (`string`) The content type.

Status
: (`string`) The HTTP status text.

StatusCode
: (`int`) The HTTP status code.

TransferEncoding
: (`string`) The transfer encoding.

## Caching

Resources returned from `resources.GetRemote` are cached to disk. See [configure file caches] for details.

By default, Hugo derives the cache key from the arguments passed to the function, the URL and the options map, if any.

Override the cache key by setting a `key` in the options map. Use this approach to have more control over how often Hugo fetches a remote resource.

```go-html-template
{{ $url := "https://example.org/images/a.jpg" }}
{{ $cacheKey := print $url (now.Format "2006-01-02") }}
{{ $resource := resource.GetRemote $url (dict "key" $cacheKey) }}
```

[configure file caches]: /getting-started/configuration/#configure-file-caches
