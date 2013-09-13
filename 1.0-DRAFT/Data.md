# osm-json 1.0

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

## 3. File Format

osm-json formats use the JSON format as described in RFC 4627.

```javascript
{
    // REQUIRED. The OSM API version, currently 0.6
    "version": "0.6",

    // OPTIONAL. The software used to generate the OSM JSON. The behavior is
    // not defined if merging different OSM JSON files with different
    // generator values.
    "generator": "CGImap 0.2.0",

    // OPTIONAL. The copyright of the data. The behavior is not defined if
    // merging different OSM JSON files with different copyright values.
    "copyright": "OpenStreetMap and contributors",

    // OPTIONAL. The required attribution URL of the data. The behavior is not
    // defined if merging different OSM JSON files with different attribution
    // values.
    "attribution": "http://www.openstreetmap.org/copyright",

    // OPTIONAL. The URL of the license for the data. The behavior is not
    // defined if merging different OSM JSON files with different license
    // values, and may not be legal.
    "license": "http://opendatacommons.org/licenses/odbl/1-0/",

    // OPTIONAL. The bounds of the area
    // TODO: Allow multiple bounds?
    "bounds" : {
        // If the bounds are present, all four keys are REQUIRED and are
        // floating point numbers that form a bounding box.
        "minlat": -90.0,
        "minlon": -180.0,
        "maxlat": 90.0,
        "maxlon": 180.0
    }

    // TODO: changesets?

    // REQUIRED. A list containing the OSM nodes in the file, which may be
    // empty. Nodes in a file MUST have a unique (id, version) pair.
    "nodes": [
        // An example of a node.
        {
            // REQUIRED. True if the object is visible, or false if it has
            // been deleted.
            "visible": true,

            // REQUIRED. The positive integer ID of the node. Implementations
            // SHOULD use a representation that allows integers above 2^31.
            // The maximum node ID from the main OSM API exceeds 2^31. Some
            // software uses high IDs (>2^32) when making one OSM file from
            // multiple sources.
            "id": 1,

            // REQUIRED. The positive integer version number of the node.
            "version": 1,

            // REQUIRED. The latitude of the node. The main API uses 7
            // decimal places of accuracy, but other uses may require more.
            "lat": 49.25,

            // REQUIRED. The longitude of the node. The main API uses 7
            // decimal places of accuracy, but other uses may require more.
            "lon": -123.5,

            // REQUIRED. The positive integer ID of the changeset the node
            // was modified in.
            "changeset": 1234,

            // REQUIRED. When the node was last modified, in the same format
            // as with OSM XML (ISO 8601 combined date/time). For
            // portability, it is RECOMMENDED that this is in UTC.
            "timestamp": "2010-02-07T07:07:58Z",

            // REQUIRED. The user ID. For anonymous objects the uid MUST be
            // null. For non-anonymous objects a uid MUST be present.
            "uid": 1234,

            // REQUIRED. The user name. For anonymous objects the user name
            // MUST be null. For non-anonymous objects a user name MUST be
            // present.
            "user": "AnEditor",

            // REQUIRED. The tags of the object. Untagged objects have no tags
            "tags": {
                // A tag pair. Tag values may be up to 255 characters in
                // length and may contain newlines, which will need to be
                // encoded.
                // A tag value MUST NOT contain the characters 0x00 through
                // 0x1F except 0x09 ('\t'), 0x0A ('\n') and 0x0D ('\r') even
                // if they are escaped.
                "key": "value"
            }
        },

        // Repeat for additional nodes.
    ],

    // REQUIRED. A list containing the OSM ways in the file, which may be
    // empty. Ways in a file MUST have a unique (id, version) pair.
    "ways": [
        // An example of a way
        {
            // REQUIRED. The positive integer ID of the way. The main OSM API
            // has a maximum way ID well under 2^31 but software MAY want to
            // use a representation that supports IDs >2^31. Some software uses
            // high IDs (>2^32) when making one OSM file from multiple sources.
            "id": 1,

            // The next attributes are the same for nodes, ways, and relations
            "visible": true,
            "version": 1,
            "changeset": 1234,
            "timestamp": "2010-02-07T07:07:58Z",
            "uid": 1234,
            "user": "AnEditor",
            "tags": {},

            // REQUIRED. A list of integers for node references. The list MUST
            // have at least one reference. Node references do not need to be
            // present in the list of nodes in the file. Node references may
            // repeat. Implementations MUST support nd references to any node
            // ID they support (e.g. they cannot use 64-bit integers for node
            // IDs and 32-bit integers for nd references)
            "nds": [
                1,
                2,
                3
            ]
        },

        // Repeat for additional ways.
    ],

    // REQUIRED. A list containing the OSM relations in the file, which may be
    // empty. Relations in a file MUST have a unique (id, version) pair.
    "relations": [
        // An example of a relation
        {
            // REQUIRED. The positive relation ID of the way. The main OSM API
            // has a maximum relation ID well under 2^31 but software MAY want
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

            // REQUIRED. A list of relation members. A relation with no
            // members is allowed, but probably a mistake in the data.
            "members": [
                {
                    // REQUIRED. Either "node", "way", or "relation".
                    // Indicates the type of object
                    "type": "node",

                    // REQUIRED. The ID of the object in the relation
                    // Note that the object referenced may not be in this file.
                    // Implementations MUST support references to any
                    // node/way/relation ID that they support.
                    // One way to meet this is by using 64-bit integers for
                    // nodes/ways/relations.
                    "ref": 1,

                    // REQUIRED. The role of the relation member. Many relation
                    // members have blank roles. Rules for this regarding length
                    // and special characters are the same as for tag keys and
                    // values.
                    "role": "foo"
                },

                // Repeat for additional relation members
        },

        // Repeat for additional relations.
    ]
}
```

