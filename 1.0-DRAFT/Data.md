# osm-json data 1.0

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to
be interpreted as described in RFC 2119.

## 1. Purpose

This specification attempts to create a standard for representing
OpenStreetMap data in a JSON format. It is intended to encode OSM data for
interchange between an API and OSM editors. It does not cover modified data
stored locally or diffs.

## 2. Definitions

* History

  A file is said to contain history if and only if it contains multiple
  versions of the same ID object or if it contains deleted data (objects
  where `visible=false`). An example of a file not containing history is
  planet.osm.

## 3. Format

The osm-json data format uses the JSON format as described in RFC 4627.

The format is formally described in data.json which is a
[JSON Schema](http://json-schema.org/) as defined in the IETF draft
[draft-fge-json-schema-validation-00](http://tools.ietf.org/html/draft-fge-json-schema-validation-00).

An example document is supplied but this document does not impose mandatory
requirements.

### Additional constraints

It is not possible to express all constraints of the format in JSON Schema.
These additional constraints MUST be followed.

* There MUST NOT be two objects of the same id and version within either the
  nodes, ways or relations lists. This constraint is within each type of object
  not across all objects (e.g. a node and way can have the same id and version
  as each other.)

* `timestamp` attributes MUST be in ISO 8601 combined date/time in UTC.

* For way nodes and relation members implementations must support references
  in these to any node/way/relation ID they support (e.g. they cannot use
  64-bit integers for node IDs and 32-bit integers for way nodes references)

  One way of meeting this requirement is to use 64-bit integers for all IDs.

* For anonymous objects the uid and username MUST be null. For non-anonymous
  objects the uid and username MUST NOT be null.

* 7 decimal places of accuracy MUST be supported for latitude and longitude.
  Implementations MAY support more.

* `minlat`, `maxlat`, `minlon` and `maxlon` MUST form a bounding box, i.e.
  `maxlat` > `minlat` and `maxlon` > `minlon`.

* All objects with the same uid MUST have the same username.

* All objects with the same changeset MUST have the same uid (and username).

## 4. Example document

This example document is informative and does not set out mandatory
requirements.

Comments are not allowed in JSON but are present in the example for clarity.

```javascript
{
    // Required. The OSM API version, currently 0.6
    "version": "0.6",

    // Optional. The software used to generate the OSM JSON.
    "generator": "CGImap 0.2.0",

    // Optional. The copyright of the data.
    "copyright": "OpenStreetMap and contributors",

    // Optional. The required attribution URL of the data.
    "attribution": "http://www.openstreetmap.org/copyright",

    // Optional. The URL of the license for the data.
    "license": "http://opendatacommons.org/licenses/odbl/1-0/",

    // Optional. The bounds of the area
    // TODO: Allow multiple bounds?
    "bounds" : {
        // If the bounds are present, all four keys are Required and are
        // floating point numbers that form a bounding box.
        "minlat": -90.0,
        "minlon": -180.0,
        "maxlat": 90.0,
        "maxlon": 180.0
    }

    // TODO: changesets?

    // Required. A list containing the OSM nodes in the file, which may be
    // empty. Nodes in a file MUST have a unique (id, version) pair.
    "nodes": [
        // An example of a visible node.
        {
            // Required. True if the object is visible, or false if it has
            // been deleted.
            "visible": true,

            // Required. The positive integer ID of the node. Implementations
            // should use a representation that allows integers above 2^31.
            // The maximum node ID from the main OSM API exceeds 2^31. Some
            // software uses high IDs (>2^32) when making one OSM file from
            // multiple sources.
            "id": 1,

            // Required. The positive integer version number of the node.
            "version": 1,

            // Required. The latitude of the node.
            "lat": 49.25,

            // Required. The longitude of the node.
            "lon": -123.5,

            // Required. The positive integer ID of the changeset the node
            // was modified in.
            "changeset": 12345,

            // Required. When the node was last modified.
            "timestamp": "2010-02-07T07:07:58Z",

            // Required. The user ID. For anonymous objects the uid is null.
            "uid": 1234,

            // Required. The username. For anonymous objects the username
            // is null.
            "user": "AnEditor",

            // Required. The tags of the object. Untagged objects have no tags
            "tags": {
                // A tag pair. Tags may contain newlines, which will need to be
                // encoded.
                // The main API places additional constraints on tag values
                // such as a maximum length of 255 characters.
                "key": "value"
            }
        },
        // An example of a deleted node
        {
            "visible": false,

            // As with a visible node, but note that there are no lat, lon or
            // tags attributes.
            "id": 1,
            "version": 2,
            "changeset": 12345,
            "timestamp": "2010-02-07T07:07:59Z",
            "uid": 1234,
            "user": "AnEditor"
        },
        // Repeat for additional nodes.
    ],

    // Required. A list containing the OSM ways in the file, which may be
    // empty. Ways in a file MUST have a unique (id, version) pair.
    "ways": [
        // An example of a visible way
        {
            // Required. The positive integer ID of the way. The main OSM API
            // has a maximum way ID well under 2^31 but software may want to
            // use a representation that supports IDs >2^31. Some software uses
            // high IDs (>2^32) when making one OSM file from multiple sources.
            "id": 1,

            // The next attributes are the same for nodes, ways, and relations
            "visible": true,
            "version": 1,
            "changeset": 12345,
            "timestamp": "2010-02-07T07:07:58Z",
            "uid": 1234,
            "user": "AnEditor",
            "tags": {},

            // Required. A list of integers for node references. The list must
            // have at least one reference. Node references do not need to be
            // present in the list of nodes in the file.
            "nodes": [
                1,
                2,
                3
            ]
        },
        // An example of a deleted way
        {
            "visible": false,

            // As with a visible way, but no tags or nodes attributes
            "id": 1,
            "version": 2,
            "changeset": 12345,
            "timestamp": "2010-02-02T07:07:59Z",
            "uid": 1234,
            "user": "AnEditor"
        },

        // Repeat for additional ways.
    ],

    // Required. A list containing the OSM relations in the file, which may be
    // empty. Relations in a file MUST have a unique (id, version) pair.
    "relations": [
        // An example of a visible relation
        {
            // Required. The positive relation ID of the way. The main OSM API
            // has a maximum relation ID well under 2^31 but software may want
            // to use a representation that supports IDs >2^31. Some software
            // uses high IDs (>2^32) when making one OSM file from multiple
            // sources.
            "id": 1,

            // The next attributes are the same for nodes, ways, and relations
            "visible": true,
            "version": 1,
            "changeset": 1234,
            "timestamp": "2010-02-07T07:07:58Z",
            "uid": 1234,
            "user": "AnEditor",
            "tags": {},

            // Required. A list of relation members. A relation with no
            // members is allowed, but probably a mistake in the data.
            "members": [
                {
                    // Required. Either "node", "way", or "relation".
                    // Indicates the type of object
                    "type": "node",

                    // Required. The ID of the object in the relation
                    "ref": 1,

                    // Required. The role of the relation member. Many relation
                    // members have blank roles. Rules for this regarding length
                    // and special characters are the same as for tag keys and
                    // values.
                    "role": "foo"
                },
                // Repeat for additional relation members
            ]
        },
        {
            "visible": false,

            // The next attributes are the same for nodes, ways, and relations
            "id": 2,
            "version": 2,
            "changeset": 12345,
            "timestamp": "2010-02-07T07:07:59Z",
            "uid": 1234,
            "user": "AnEditor",
        },

        // Repeat for additional relations.
    ]
}
```

