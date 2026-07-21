# Editing via IFRAME

## Overview

The iframe containing the G3W-SUITE application and the parent container application exchange messages using the following structure.

---

## Message Format

### Application → Iframe (Request)

```json
{
  "id": "<UNIQUE_STRING_IDENTIFYING_THE_REQUEST>",
  "action": "editing:json",
  "data": {
    "qgs_layer_id": "<LAYER_ID>",
    "method": "<METHOD_NAME>",
    "geojson": "<GEOJSON_OBJECT_OF_THE_TARGET_FEATURE>"
  }
}
```

| Field | Description |
| --- | --- |
| `id` | A unique string identifying the request |
| `action` | Always `"editing:json"` — identifies this as an editing message |
| `data.qgs_layer_id` | The QGIS layer ID |
| `data.method` | The method/action to execute (see below) |
| `data.geojson` | GeoJSON object of the feature to act upon |

**Available methods:**

| Method | Description |
| --- | --- |
| `add` | Add a new feature |
| `delete` | Delete an existing feature from the database |
| `update` | Update an existing feature |
| `draw` | Draw a new feature or modify the geometry of an existing one |

---

### Iframe → Application (Response)

```json
{
  "action": "editing:json",
  "response": {
    "result": "<BOOLEAN: true on success, false on failure>",
    "data": {
      "method": "<EXECUTED_METHOD_NAME>",
      "geojson": "<GEOJSON_OBJECT_OF_THE_AFFECTED_FEATURE>"
    }
  }
}
```

---

## Examples

### Draw / Modify a Feature (Activate Map Drawing)

Activates the drawing tool on the map for a geometric layer.

**Request:**

```json
{
  "id": "1784642571309",
  "action": "editing:json",
  "data": {
    "qgs_layer_id": "buildings_2f43dc1d_6725_42d2_a09b_dd446220104a",
    "method": "draw",
    "geojson": "<OPTIONAL_GEOJSON_OF_THE_FEATURE_TO_MODIFY>"
  }
}
```

Once the feature has been drawn, the iframe sends a message to the parent application containing the GeoJSON of the feature — without attributes, which will be added later by the application — and then calls the `add` method to save it to the database.

**Response example (Polygon):**

```json
{
  "action": "editing:json",
  "response": {
    "result": true,
    "data": {
      "method": "draw",
      "geojson": {
        "type": "Feature",
        "geometry": {
          "type": "MultiPolygon",
          "coordinates": [
            [
              [
                [1252200.7608101263, 5433648.920573661],
                [1252036.0663704963, 5433472.356264507],
                [1252331.329374878,  5433291.340754284],
                [1252460.4142059393, 5433509.449606767],
                [1252200.7608101263, 5433648.920573661]
              ]
            ]
          ]
        },
        "properties": null,
        "id": "_new_1784646052597"
      }
    }
  }
}
```

> **Note:** Pay attention to the `"id": "_new_1784646052597"` value — in particular the `_new_` prefix, which is required to generate a new ID when the feature IDs stored in the database are auto-incremented.

---

### Add a Feature to a Layer

**Request:**

```json
{
  "id": "1784642571309",
  "action": "editing:json",
  "data": {
    "qgs_layer_id": "buildings_2f43dc1d_6725_42d2_a09b_dd446220104a",
    "method": "add",
    "geojson": {
      "type": "Feature",
      "geometry": {
        "type": "MultiPolygon",
        "coordinates": [
          [
            [
              [1252200.7608101263, 5433648.920573661],
              [1252036.0663704963, 5433472.356264507],
              [1252331.329374878,  5433291.340754284],
              [1252460.4142059393, 5433509.449606767],
              [1252200.7608101263, 5433648.920573661]
            ]
          ]
        ]
      },
      "properties": null,
      "id": "_new_1784646052598"
    }
  }
}
```

**Success response:**

```json
{
  "id": "1784647113405",
  "action": "editing:json",
  "response": {
    "result": true,
    "data": {
      "method": "add",
      "fid": "70",
      "geojson": "<GEOJSON_OF_THE_NEWLY_CREATED_AND_SAVED_FEATURE>"
    }
  }
}
```

**Error response:**

```json
{
  "id": "1784647182919",
  "action": "editing:json",
  "response": {
    "result": false,
    "data": {
      "buildings": {
        "add": {
          "id": "_new_1784647180034",
          "fields": {
            "year": ["Field value must be NOT NULL"],
            "high":  ["Field value must be NOT NULL"],
            "type":  ["Field value must be NOT NULL"]
          }
        }
      }
    }
  }
}
```

---

### Update a Feature

**Request:**

```json
{
  "id": "1784642571309",
  "action": "editing:json",
  "data": {
    "qgs_layer_id": "buildings_2f43dc1d_6725_42d2_a09b_dd446220104a",
    "method": "update",
    "geojson": "<GEOJSON_OF_THE_FEATURE_TO_UPDATE (attributes and/or geometry)>"
  }
}
```

---

### Delete a Feature

**Request:**

```json
{
  "id": "1784642571309",
  "action": "editing:json",
  "data": {
    "qgs_layer_id": "buildings_2f43dc1d_6725_42d2_a09b_dd446220104a",
    "method": "delete",
    "geojson": {
      "id": "<ID_OF_THE_FEATURE_TO_DELETE>"
    }
  }
}
```
