# Hugo Base Module

Basic and useful partials and functions for Hugo theme developers.

## Functions

## Partials

### Title Partials

The `base/title` partial generates a universal title for the page, that include separator, paginator (i18n) and sections' titles.

| Parameter | Type | Default | Description
|---|---|---|---
| `title_separator` | String | `-` | Title separator.

| Page | Kind | Title
|---|---|---
| `/` | Home | My Site.
| `/page/2` | List | My Site - Page 2 of N.
| `/blog` | Section | Blog - My Site.
| `/blog/hello` | Single Page | Hello world - Blog - My Site.
| `/blog/foo` | Section | Foo - My Site.
| `/blog/foo/bar` | Single Page | Bar - Foo - My Site.

You'll need to wrap the `base/title` partial as the fallback via `block` syntax, and initialize the paginator within the `title` block.

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
