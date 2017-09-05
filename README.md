# Avalon+JSON

## Description

Avalon+JSON is a pragmatic hypermedia specification designed to enable task-based user interfaces whilst preserving traditional web API entity response models.

**Author:** [Ben Hinman](http://ben.hinman.io).

## Media Type

The media type for Avalon+JSON is application/vnd.avalon+json (pending).

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

## Specification

### Response

#### Discussion

A response consists of a collection, entity, acknowledgement or error with optional collections of links and forms.

#### Properties

`collection`

A [Collection](#collection). MUST NOT be specified if an entity, acknowledgement or error has been specified.

`entity`

An [Entity](#entity). MUST NOT be specified if a collection, acknowledgement or error has been specified.

`acknowledgement`

An [Acknowledgement](#acknowledgement). MUST NOT be specified if a collection, entity or error has been specified.

`error`

An [Error](#error). MUST NOT be specified if a collection, entity or acknowledgement has been specified.

`links`

An array of [Link](#link)s. OPTIONAL.

`forms`

An array of [Form](#form)s. OPTIONAL.

### Collection

#### Example

```json
{
    "collection": {
        "items": [
            {
                "entity": {
                    "name": "TicketIndexResponse",
                    "data": {
                        "id": 1,
                        "number": 1,
                        "summary": "Could not connect to server."
                    }
                },
                "links": [
                    {
                        "name": "self",
                        "displayName": "TKT-1",
                        "href": "https://example.org/api/tickets/1"
                    }
                ]
            }
        ],
        "totalItemCount": 1
    },
    "links": [
        {
            "name": "self",
            "displayName": "Current",
            "href": "https://example.org/api/tickets?skip=0&take=1"
        },
        {
            "name": "first",
            "displayName": "First",
            "href": "https://example.org/api/tickets?skip=0&take=1"
        },
        {
            "name": "last",
            "displayName": "Last",
            "href": "https://example.org/api/tickets?skip=0&take=1"
        }
    ],
    "forms": [
        {
            "name": "create",
            "displayName": "Create Ticket",
            "method": "POST",
            "contentType": "application/json",
            "href": "https://example.org/api/tickets",
            "fieldsets": [
                {
                    "fields": [
                        {
                            "name": "summary",
                            "displayName": "Summary",
                            "type": "text"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### Discussion

A collection represents an array of items with a total item count. The total item count is useful when only a subset of items are included as part of the response (i.e. when paging). Processing agents typically return collection responses when the requested resource represents more than one item.

#### Properties

`items`

An array of [Item](#item)s. REQUIRED.

`totalItemCount`

The total number of items (i.e. without paging). REQUIRED.

### Entity

#### Example

```json
{
    "entity": {
        "name": "TicketReadResponse",
        "data": {
            "id": 1,
            "number": 1,
            "summary": "Could not connect to server."
        }
    },
    "links": [
        {
            "name": "self",
            "displayName": "TKT-1",
            "href": "https://example.org/api/tickets/1"
        },
        {
            "name": "notes",
            "displayName": "Notes",
            "href": "https://example.org/api/tickets/1/notes",
            "fieldsets": [
                {
                    "fields": [
                        {
                            "name": "isPrivate",
                            "displayName": "Is Private?",
                            "type": "checkbox"
                        }
                    ]
                }
            ]
        }
    ],
    "forms": [
        {
            "name": "addNote",
            "displayName": "Add Note",
            "method": "POST",
            "contentType": "application/json",
            "href": "https://example.org/api/tickets/1/notes",
            "fieldsets": [
                {
                    "fields": [
                        {
                            "name": "content",
                            "displayName": "Content",
                            "type": "text"
                        },
                        {
                            "name": "isPrivate",
                            "displayName": "Is Private?",
                            "type": "checkbox"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### Discussion

An entity represents arbitrary data with an associated name that describes its structure. Processing agents typically return entity responses when the requested resource represents a single item.

#### Properties

`name`

The name of the entity. REQUIRED. Implementers SHOULD document the different data structures and names.

`data`

The data of the entity. REQUIRED.

### Acknowledgement

#### Example

```json
{
    "acknowledgement": {
        "messages": [
            {
                "type": "Information",
                "title": "Ticket Created",
                "content": "TKT-1 was created."
            }
        ]
    },
    "links": [
        {
            "name": "created",
            "displayName": "TKT-1",
            "href": "https://example.org/api/tickets/1"
        }
    ]
}
```

#### Discussion

An acknowledgement represents an array of messages. Processing agents typically return an acknowledgement when a user or user agent performs an action against a resource.

#### Properties

`messages`

An array of [Message](#message)s. OPTIONAL.

### Error

#### Example

```json
{
    "error": {
        "message": "Validation failed: \r\n -- 'Summary' is required."
    }
}
```

#### Discussion

An error represents a message. Processing agents typically return an error when a request to a resource fails.

#### Properties

`message`

The message of the error. REQUIRED.

### Item

#### Discussion

An item represents an entity with optional collections of links and forms.

#### Properties

`entity`

An [Entity](#entity). REQUIRED.

`links`

An array of [Link](#link)s. OPTIONAL.

`forms`

An array of [Form](#form)s. OPTIONAL.

### Link

#### Discussion

Links enable users or user agents to navigate between resources. A link name describes the relationship between the current resource and the resource being linked to. The field names and values should be appended to the request URI specified in the `href` property (separated by a question-mark) and sent according to the underlying protocol.

#### Properties

`name`

The name of the link. REQUIRED.

`displayName`

The display (i.e. human-readable) name of the link. REQUIRED.

`href`

The URI of the request. REQUIRED.

`fieldsets`

An array of [Fieldset](#fieldset)s. OPTIONAL.

### Form

#### Discussion

Forms enable users or user agents to perform actions against resources. The resources being actioned against will typically be the current resource or a sub-resource of the current resource. The field names and values should be serialised according to the `contentType` property and sent according to the `method` property and underlying protocol.

#### Properties

`name`

The name of the form. REQUIRED. MUST be unique.

`displayName`

The display (i.e. human-readable) name of the form. REQUIRED.

`method`

The protocol-specific method of the request. REQUIRED.

`contentType`

The content type of the request. REQUIRED when the form contains one or more [Field](#field)s.

`href`

The URI of the request. REQUIRED.

`fieldsets`

An array of [Fieldset](#fieldset)s. OPTIONAL.

### Message

#### Discussion

Messages enable user agents to display (if applicable) additional information to users.

#### Properties

`type`

The type of the message. Permitted values are `Information`, `Warning` and `Error`. OPTIONAL. SHOULD be `Information` when omitted.

`title`

The title of the error. OPTIONAL.

`content`

The content of the error. REQUIRED.

### Fieldset

#### Discussion

Fieldsets enable user agents to display (if applicable) groups of related fields to users. The `displayName` property can be used to distinguish groups from one another.

#### Properties

`displayName`

The display (i.e. human-readable) name of the fieldset. OPTIONAL.

`fields`

An array of [Field](#field)s. REQUIRED.

### Field

#### Discussion

Fields enable users or user agents to send information as part of the request. They also enable user agents to display (if applicable) controls to users. Fields typically represent simple values that can be serialised easily. However, they can be [extended](#extensions) to enable more complex types, values and controls if necessary.

#### Properties

`name`

The name of the field. REQUIRED. MUST be unique.

`displayName`

The display (i.e. human-readable) name of the field. OPTIONAL.

`type`

The implementation-specific type of the field. Implementers SHOULD document the available field types and default value. OPTIONAL. SHOULD be the default value when omitted.

`value`

The initial value of the field. OPTIONAL.

### Extensions

The Avalon+JSON specification can be extended arbitrarily by introducing new properties prefixed with `x-`.

## Acknowledgements

The author gratefully acknowledges the following specifications that influenced the design of this media type:

* [Collection+JSON](http://amundsen.com/media-types/collection/format/)
* [Siren](https://github.com/kevinswiber/siren)

The author gratefully acknowledges the following individuals who generously contributed comments on the design of this media type:

* [Andrew Bush](http://www.bushdev.online)
* Sam Melville-Coman