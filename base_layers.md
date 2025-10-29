# G3W-SUITE base layers

Below are the instructions for adding, modifying, or deleting configurations for `Base layers`, the base layers of webgis services that are independent of the project and configured at the Map Group level.

These operations are exclusively allowed to users with the `Admin level 1` role. Base layers can be configured from within the Django admin interface.

![](images/manual/django_admin_access.png)

![](images/manual/django_admin_base_layers.png)

![](images/manual/django_admin_base_layers_form.png)

Form fields:
 - `Name`: an internal identifier for the base layer.
 - `Title`: the name of the base layer.
 - `icon`: an image to use in the list of available base layers in the related tab in the webgis.
 - `Description`: a descriptive field for the base layer.
 - `Property`: the base layer configuration in the form of a *python dict*.


In the `Property` field, you can configure `TMS`, `WMS`, and `WMTS` services as base layers. Below are some examples:

### TMS
```python
{
 "crs":{
  "epsg": 32632,
   "proj4": "+proj=utm +zone=32 +datum=WGS84 +units=m +no_defs",
   "geographic":  'false',
   "axisinverted": 'false',
  "extent" : [166021.44,0.0,833978.56,9329005.18]
},
 "url": "https://sit.parco.gran-paradiso.g3wsuite.it/caching/api/qdjango30/{z}/{x}/{y}.png",
 "servertype": "TMS",
 "attributions": "Ortofoto Piemonte AGEA 2015"
}
```

### WMTS
```python
{
 "crs": {
    "epsg": 27700,
    "proj4": "+proj=tmerc +lat_0=49 +lon_0=-2 +k=0.9996012717 +x_0=400000 +y_0=-100000 +ellps=airy +units=m +no_defs",
    "geographic": False,
    "axisinverted": False,
    "extent": [-103976.29764287075, 6853.1136085508915, 616829.1229036889, 1243036.259830151]
 },
 "url": "http://localhost:8080/mapproxy_conf_2378/service/?",
 "servertype": "WMTS",
 "attributions": "",
 "layer": "MVDC_DistrictBoundary",
 "extent": [508630.2485007373, 134752.46570880772, 528222.6959272355, 161343.02015124384],
 "grid": "localgrid_wmts",
 "grid_extent": [508630.2485007373, 134752.46570880772, 528222.6959272355, 161343.02015124384]
}
```

**IMPORTANT** the configurations shown above are not `JSON` but are `Python dict`

Below is a brief description of the parameters:

 - `crs`*: full declaration of the reference system of the data we are accessing.
 - `url`*: url of the service to reach including _placeholders_ if necessary (e.g., *{x}{y}{z}* for TMS services).
 - `servicetype`*: one of the following values: `TMS`, `WMTS`, `WMS`.
 - `layer`: the name of the service layer to call for `WMTS` and `WMS`.
 - `extent`: a list that represents the data extent.
 - `attribution`: a string indicating the attributions that will be shown on the webgis service when the base layer is active.
 - `grid`: the name of the grid to use in `WMTS` services
 - `grid_extent`: extent of the grid declared in the `grid` parameter

**\*** mandatory parameters