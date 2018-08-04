# ezproxy

A web service API for managing EZproxy configurations.

## API

### ```GET /configs```

Gets the list of EZproxy configurations, returning a JSON list:
```json
[
  { id: "config-id", name: "config-name" }, ...
]
```

### ```GET /configs/{id}```

Gets the specified EZproxy configuration.

If the query parameter `format=text` or header `Accept: text/plain` is given, the configuration
is returned as an EZproxy config.txt file. Otherwise a JSON `config` object is returned. 

### ```POST /configs```

Creates a new EZproxy configuration.

If the content-type is `text/plain` the body is parsed as an EZproxy config.txt file.
Otherwise a JSON `config` object is expected.

### ```PUT/PATCH /configs/{id}```

Updates the specified EZproxy configuration.

If the content-type is `text/plain` the body is parsed as an EZproxy config.txt file.
Otheriwse a JSON `config` object is expected.

The `PUT` method entirely replaces configuration entries with those in the request body.
The `PATCH` method updates only the fields supplied in the request body.

### ```DELETE /configs/{id}```

Deletes the specified EZproxy configuration.

### ```GET /oclc```

Gets the list of entries in the OCLC EZproxy database.

If the query parameter `format=short` is given, a list of partial `stanza` objects (omitting
the body) is returned:

```json
[
  { last_updated: "YYYY-MM-DD", source: "oclc-url", title: "resource-title" }
]
```

If the query parameter `format=full` is given, a list of full `stanza` objects with body
content is returned:
```json
[
  { body: ["line1", ...], last_updated: "...", source: "...", title: "..." }, ...
]
```
### ```GET /oclc/{title}```

Gets the entry with the given title in the OCLC EZproxy database.

If the query parameter `format=text` or header `Accept: text/plain` is given, the entry is
returned as a resource in an EZproxy config.txt file. Otherwise a `stanza` object is
returned.

## Objects

### Config

A `config` object represents a complete EZproxy server configuration and consists of a
number of `section` objects which allow stanzas to be grouped using the EZproxy `Group`
mechanism.

`description` and `title` are optional and for documentation only.

```json
{
  description: "config description",
  id: "id",
  sections: [section, ...],
  title: "config title"
}
```

### Section

A `section` represents a group of `stanza` objects in an EZproxy server configuration.

The `groups` attribute, if specified, causes all stanzas to be collected under an EZproxy
`Group` directive. Multiple groups can be specified, corresponding to the EZproxy
directive `Group group1+group2+...`

`description` and `title` are optional and for documentation only.

```json
{
  description: "section description",
  groups: ["group", ...],
  stanzas: [stanza, ...],
  title: "section title"
}
```

### Stanza

A `stanza` represents a single resource or related group of directives in an EZproxy
server configuration.

`body` contains the lines of the stanza.

`last_updated` indicates the time of the latest update to the stanza content.

`source` is the URI of the stanza source. This may be the file URI of a config.txt file
or the http URI of an OCLC database entry. 

`description` and `title` are optional and for documentation only.

```json
{
  body: ["line 1", "line 2", ...],
  description: "stanza description"
  last_updated: "YYYY-MM-DD",
  source: "uri",
  resource: "resource title",
  title: "stanza title"
}
```