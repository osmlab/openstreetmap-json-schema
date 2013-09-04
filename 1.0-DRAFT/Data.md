# osm-json 1.0

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to
be interpreted as described in RFC 2119.

## 1. Purpose

This specification attempts to create a standard for representing
OpenStreetMap data in a JSON format. It applies to data from an OSM API or
similar source but *this* specification does not cover modified data stored
locally, data for uploading

## 2. File Format

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
    // values.
    "license": "http://opendatacommons.org/licenses/odbl/1-0/",

    // OPTIONAL. The bounds of the area
    // To-do: Allow multiple bounds?
    "bounds" : {
        // If the bounds are present, all four keys are REQUIRED and are
        // floating point numbers that form a bounding box.
        "minlat": -90.0,
        "minlon": -180.0,
        "maxlat": 90.0,
        "maxlon": 180.0
    }

    // REQUIRED. A list containing the OSM nodes, ways, and relations in the file.
    // In most cases, the array SHOULD be sorted first by type (node, way, relation)
    // then by ID.
    "objects": [
        // An example of a node.
        {
            // REQUIRED. The type of the object. In this case, a 'node'.
            "type": "node",
 
            // REQUIRED. If the object is visible, or if it has been deleted.
            "visible": true,

            // REQUIRED. The positive integer ID of the node. Implementations
            // SHOULD NOT use a 32-bit integer for this
            "id": 1,

            // REQUIRED. The positive integer version number of the node.
            "version": 1,

            // REQUIRED. The latitude of the node. The main API uses 9
            // decimal places of accuracy, but other uses may require more.
            "lat": 49.25,

            // REQUIRED. The longitude of the node. The main API uses 9
            // decimal places of accuracy, but other uses may require more.
            "lat": -123.5,

            // REQUIRED. The positive integer of thechangeset the node was
            // last modified in.
            "changeset": 1234,

            // REQUIRED. When the node was last modified, in the same format
            // as with OSM XML
            "timestamp": "2010-02-07T07:07:58",

            // OPTIONAL. The user ID. For anonymous objects a uid MUST NOT
            // be present. For non-anonymous objects a uid MUST be present
            "uid": 1234,

            // OPTIONAL. The user name. For anonymous objects a user name
            // MUST NOT be present. For non-anonymous objects a user name
            // MUST be present
            "user": "AnEditor",

            // OPTIONAL. The tags of the object. Untagged objects have no tags
            "tags": {
                // A tag pair. Tag values may be up to 255 characters in
                // length and may contain newlines, which will need to be
                // encoded.
                "key": "value"
            }
        },

        // Repeat for additional nodes. Any two nodes MUST NOT have the same id

        // An example of a way
        {
            // REQUIRED. The type of the object. In this case, a 'way'.
            "type": "way",
 
            // REQUIRED. The positive integer ID of the way.
            "id": 1,

            // The next attributes are the same for nodes, ways, and relations
            "visible": true,
            "version": 1,
            "changeset": 1234,
            "timestamp": "2010-02-07T07:07:58",
            "uid": 1234,
            "user": "AnEditor",
            "tags": { 
                // ...
            },

            // REQUIRED. A list of integers for node references. The list MUST
            // have at least one reference. Node references do not need to be
            // present in the list of nodes in the file.
            "nds": [
                1,
                2,
                3
            ]
        },

        // Repeat for additional ways. Any two ways MUST NOT have the same id

        // An example of a relation
        {
            // REQUIRED. The type of the object. In this case, a 'relation'.
            "type": "relation",
 
            // REQUIRED. The positive integer ID of the relation.
            "id": 1,

            // The next attributes are the same for nodes, ways, and relations
            "visible": true,
            "version": 1,
            "changeset": 1234,
            "timestamp": "2010-02-07T07:07:58",
            "uid": 1234,
            "user": "AnEditor",
            "tags": {
                // ...
            },

            // OPTIONAL. A list of relation members. A relation with no
            // members is allowed, but probably a mistake in the data.
            "members": [
                {
                    // REQUIRED. Either "node", "way", or "relation".
                    // Indicates the type of object
                    "type": "node",

                    // REQUIRED. The ID of the object in the relation
                    // Note that the object referenced may not be in this file
                    "ref": 1,

                    // REQUIRED. The role of the relation member. Many relation
                    // members have blank roles.
                    "role": "foo"
                },

                // Repeat for additional relation members
        },

        // Repeat for additional relations. Any two relations MUST NOT have the same id
    ]
}
```
