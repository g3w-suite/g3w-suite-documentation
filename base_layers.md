# G3W-SUITE base layers

Below are the instructions for adding, modifying, or deleting configurations for `Base layers`, the base layers of webgis services that are independent of the project and configured at the Map Group level.

These operations are exclusively allowed to users with the `Admin level 1` role. Base layers can be configured from within the Django admin interface.

![](images/manual/django_admin_access.png)

![](images/manual/django_admin_base_layers.png)

![](images/manual/django_admin_base_layers_form.png)

I campi del form:
 - `Name`: un identificativo interno del base layer.
 - `Title`: il nome del base layer.
 - `icon`: unna immagine da utilizzare nella lista dei base layers disponibili nel tab relativo nel webgis.
 - `Description`: un campo descrittivo del base layer.
 - `Property`: la configurazione del base layer sotto forma di *python dict*.


Nel campo `Property` è possibile configurare servizi `TMS`, `WMS` e `WMTS` come base layers, di seguito alcuni esempi:

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

**IMPORTANTE** le configurazioni sopra indicate non sono `JSON` ma sono `Python dict`

Di seguito un breve descrizione dei parametri:

 - `crs`*: dichiarazione per esteso del sistema di riferimento dei dati a cui accediamo.
 - `url`*: url del servizio da raggiungere compreso dei _placeholder_ se necessari (ad esempio *{x}{y}{z}* per i servizi TMS).
 - `servicetype`*: uno tra i seguenti valori, `TMS`, `WMTS`, `WMS`.
 - `layer`: il nome del layer del servizio da richiamare per `WMTS` e `WMS`.
 - `extent`: una lista cre rappresenta l'estensione del dato.
 - `attribution`: una stringa in cui indicare le attribution che verranno mostrate sul servizio webgis del il base layer è attivo.
 - `grid`: il nome della griglia da usare nei servizzi `WMTS`
 - `grid_extent`: estensione della griglia dichiarata nel parametro `grid`

**\*** parametri obbligatori