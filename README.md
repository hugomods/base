# Hugo Base Module

[![Used By](https://img.shields.io/badge/dynamic/json?color=success&label=used+by&query=repositories_humanize&logo=hugo&style=flat-square&url=https://api.razonyang.com/v1/github/dependents/hugomods/base)](https://github.com/hugomods/base/network/dependents)
![Hugo Requirements](https://img.shields.io/badge/dynamic/json?color=important&label=requirements&query=requirements&logo=hugo&style=flat-square&url=https://api.razonyang.com/v1/hugo/modules/github.com/hugomods/base)
[![License](https://img.shields.io/github/license/hugomods/base?style=flat-square)](https://github.com/hugomods/base/blob/main/LICENSE)
[![Version](https://img.shields.io/github/v/tag/hugomods/base?label=version&style=flat-square)](https://github.com/hugomods/base/tags)

Basic and useful partials and functions for Hugo theme developers.

## Functions

### Has ToC Function

The `base/functions/has-toc` accepts a Page and returns a boolean value to indicate whether the page's table of contents is not empty.

```go
{{ if partial "base/functions/has-toc" . }}
  {{ .TableOfContents }}
{{ end }}
```

### Is URL External

Indicate whether the URL is external.

```go
{{ if partial "base/functions/is-url-external" "https://example.com" }}
OPEN IN NEW TAB
{{ end }}
```

### Is Menu External

Indicate whether the menu is external.

```go
{{ if partial "base/functions/is-menu-external" $menu }}
OPEN IN NEW TAB
{{ end }}
```

## Partials

### Title Partials

The `base/title` partial generates a universal title for the page, that include separator, paginator (i18n) and sections' titles.

| Parameter | Type | Default | Description
|---|:-:|:-:|---
| `title_separator` | String | `-` | Title separator.
| `title_sections` | Boolean | `true` | Show/Hide the sections titles.

| Page | Kind | Title
|---|:-:|---
| `/` | Home | My Site.
| `/page/2` | List | My Site - Page 2 of N.
| `/blog` | Section | Blog - My Site.
| `/blog/hello` | Page | Hello world - Blog - My Site.
| `/blog/foo` | Section | Foo - My Site.
| `/blog/foo/bar` | Page | Bar - Foo - My Site.

You'll need to wrap the `base/title` partial as the fallback via `block` syntax, and initialize the paginator within the `title` block if necessary.

> Since the `.Paginator` was cached once initialized, so we MUST initialized it before calling the `base/title` partial.

```go
{{/* baseof.html */}}
<html>
  <head>
    <title>
      {{ block "title" . }}{{ partial "base/title" . }}{{ end }}
    </title>
  </head>
  <body>
    {{ block "main" . }}{{ end }}
  </body>
</html>
```

```go
{{/* list.html */}}
{{ define "title" }}
  {{/* We MUST initialize the paginator first. */}}
  {{ $mainSections := slice "blog" }}
  {{ $pages := where .Site.RegularPages "Type" "in" $mainSections }}
  {{ $paginator := .Paginate $pages 1 }}
  {{/* Then include the title partial. */}}
  {{ partial "base/title" . }}
{{ end }}

{{ define "main" }}
  {{/* Use the initialized paginator above. */}}
  {{ range .Paginator.Pages }}
  {{ end }}
  {{ template "_internal/pagination.html" . }}
{{ end }}
```

### Partial If Exists

Include the target partial if exists.

| Parameter | Type | Description
|---|:-:|---
| `Name` | String | The partial name, extension is required, such as `foo.html`.
| `Context` | Any | The context passed to target partial.

```go
{{ partial "base/partial-if-exists" (dict "Name" "foo.html" "Context" .) }}

{{ partial "base/partial-if-exists" (dict "Name" "foo.json" "Context" .) }}

{{ partial "base/partial-if-exists" (dict "Name" "invalid.html" "Context" .) }}
```
