# Editing on line
## Activation and configuration
### Introduction and main features
The **editing settings** are defined partly at the **QGIS project level (editing form structure, widgets associated with individual attributes, 1:n and, in part, N:M relations)** and partly at the **Administration level** (users with editing power, activation scale, alpha and geo constraints).

Thanks to the integration with the APIs of QGIS it is now possible to manage the main formats (geographically and not) supported by QGIS Server:

 * **reading and editing mode**
   * PostGreSQL/PostGis
   * SQLite/SpatiaLite
   * Oracle Spatial
   * GeoPackage
   * ShapeFile

 * **reading mode**
   * SQL Server
   * Virtual layer

**Pay attention!**
An important note in relation to the formats supported in editing: please remember that only data on PostGreSQL/PostGis and Oracle Satial support multi-user editing.

Multi-user editing on formats such as Shapefile, but also GeoPackage and SpatiaLite, can lead to data corruption.

### 1:N and N:M relational editing

The editing function manages both direct editing on geometric and alphanumeric layers, and editing on layers in a 1:N or N:M (only in paert) relations, also both geometric and alphanumeric.

**Attention:** the management of the editing of the N: M relations is limited to the management of the 1:N relationship between the parent layer and the intermediate table.

This means that currently it is not possible to create new records in table M but only:
 * modify the current relations present
 * create new relations between new elements of layer N and pre-existing records of layer M

The **editing settings** are defined partly at the **QGIS project level** (**editing form structure, widgets associated with individual attributes, 1:n  and N:M relations**) and partly at the **Administration level** (users with editing power, activation scale, alpha and geo constraints).

### Multi-user editing
G3W-SUITE manages **multi-user editing** through a **feature lock system**.

It’s strongly recommended to allow multi-user editing only in the case of layers on the GeoDatabase and not on physical files.

When an enabled user activates the editing function at the map client level, the **features visible on the map at that time will be blocked**, in relation to the editing aspect, **for all the other enabled users** who will still be able to edit features present outside this geographical extension. .

In case of features of a layer locked by other users, the user will receive a warning message when starting the editing.

This block will be deactivated when the user exits the editing mode.

### QGIS project settings

#### Definition of editing widgets associated with individual attributes

In the QGIS project, for each layer it is possible to **define the structure of the attribute display module**.

The same structure will be used in the cartographic client when editing attributes.

The definition of the form structure can be managed, on QGIS, from the **`Properties of the vector`, in the `Attributes Form section`**.

![](images/manual/editing_qgis_form_widget.png)

##### Conditionals forms based on QGIS expressions

Conditional forms based on QGIS expressions are supported and implemented for online editing/consulting.

It is therefore possible to define the visibility of a forms (form or group) and of the fields it contains, on the basis of the values defined on further fields when filling in the attributes.

##### Drill-down (cascading) forms

Drill-down (cascading) forms **based on QGIS expressions** are supported and implemented for online editing.

The functionality can be used to implement “drill-down” within editing forms, where the values available in one field depend on the values of other fields.

This function will be reflected in the consultation side.

#### Definition of editing widgets associated with individual attributes
At the QGIS project level (always from **`Vector Properties`**, **`Attribute Form`** section) it is possible to define an alias and an editing widget for each attribute.

The alias and editing widgets defined at the project level will be available during web editing with some limitations.

Below are the available widgets and any limitations:
 * **`Relation reference:`** with some option limitation
 * **`Checkbox`**
 * **`Date/time`:** with Display format supported
 * **`Attachment`**
 * **`Range`:** with default, minimum and maximum values supported
 * **`Text edit`:** multiline and html included
 * **`Unique values`:**: this widget will be equipped with a **pick layer** tool at the cartographic client level
 * **`Value map`**
 * **`Value relations`:**
   * Order value option supported
   * Allow multiple selections supported
   * QGIS filter expression supported
   * the widget will be equipped with a pick layer tool at the cartographic client level

 **The expression-based filter can also be dependent on the values of other fields on the form and useful to create cascading drill down.**
   
With regard to the **`Attachment widget`**, it is necessary to specify that the association of a multimedia file with a feature requires that this file is uploaded to a dedicated space (exposed on the web) on the server and that the association takes place via a URL that refers to that file.

This solution allows you to consult the associated attachments also from QGIS or from other GIS software.

**Additional settings at single layer level**

In the **`Attribute Form`** section of the **`Layer Properties`** it is also possible to define for every field:
 * **field editable or not**
 * **mandatory constraints**
 * **unique constraints**
 * **default values:** also based on QGIS expression and with **Apply default value** on update option supported
 * **conditional forms**
 
#### QGIS expressions and default values

**All the QGIS expressions can be used as default values.**

The default values defined for individual fields at the QGIS project level are inherited from the suite.

In this case, at the online edit level, the form relating to the field thus defined will be self-calculated and can be set not editable by the user.

**The result of the expression can also be dependent on the values of other fields on the form.**

Very useful in all cases where we want the values of a field to be calculated automatically through the potential of QGIS expressions.


The `Apply the default value also to the update` option is supported.

As in QGIS the default values are displayed in the form during editing and not only after saving.

#### Definition of 1:n relations
In the event that, at the QGIS project level, one or more 1: n type relationships have been associated with a layer (**`Project menu → Properties…`, `Relations` section**), it will be possible to carry out relational editing also on the webgis platform.

Also for the tables related in 1:n mode it will be possible to define the **attribute form structure, aliases and editing widgets** in the QGIS project.

These configurations and tools will automatically available  on the webgis platform.

#### Transaction Group

**Use transaction group to edit, save or rollback multiple layers changes at once**

When working with layers with the same source (es. layer from the same PostGreSQL database), activate the **`Automatically create transaction groups where possible`** option in **`Project → Properties… → Data Sources`** to sync their behavior (enter or exit the edit mode, save or rollback changes at the same time).

![](images/manual/datasources.png)

### Administration settings

#### Activation of on-line editing on a single layer
To activate the online editing functions, access the **`Layer list`** section of the project within the administration panel of G3W-ADMIN.

![](images/manual/g3wsuite_administration_project_layer_list.png)

Identify the layer on which you want to activate the editing function and click on the **`Editing layer` icon** ![](images/manual/icon_editing.png) located on the left 

**Attention:** check that the layer format is among those supported by QGIS for the editing function

Clicking on the icon will open a modal window that will allow you to:
 * **Active:** activate the editing function
 * **Visible:** check on uncheck to show layer inside the list of layers to edit (useful for child layers in a 1:N relationship in order to allow editing only starting from the parent)
 * **Scale:** define the editing activation scale (only for geometric tables)
 * **define the Viewer users** (individuals or groups) **enabled** for online editing

For each user (single or group) it is possible to discriminate the editing powers:
 * **Add:** add feature, copy feature, add part to multipart
 * **Update geometry:** update vertex feature, move feature, add part to multipart, delete part from multipart, split features, dissolve features
 * **Update attributes:** update feature attributes, update attributes of selected features
 * **Delete:** delete features

I should be noted that:
 * **Viewers users** (individuals or groups) **available** in the drop-down menu **will be limited to those who have allowed access in consultation to the WebGis project**
 * **Editor1 and Editor2 users**, **owners** of the project, are **enabled by default** to the online editing function

![](images/manual/editing_setting.png)

**NB: In case a user belongs to a user group, the permissions set will be added together.**

##### Ability to associate references to create/update G3W-SUITE users

Through these two optional settings it is possible to define four fields of the table of the layer being edited automatically filled in.

These fields will contain references to G3W-SUITE users and users group who creators or modifiers of the individual features edited.

All settings defined at the QGIS level for these fields (eg default values) will no longer be considered.

It is recommended that these fields be set as non-editable at the project level.

#### Activation of online editing on multiple layers at the same time
In the case of a project in which it is necessary to activate the online editing function on many layers, by defining the same options for each of them it is possible to use the Project layer action function.

![](images/manual/multi_layer_icon.png)

![](images/manual/multi_layer_form.png)

#### Copy editing permission from a user to the others

In the case of complex projects with many users enabled for editing, it is possible to use this tool to duplicate the editing permissions defined for a user and apply them to other users (both individuals and groups).

![](images/manual/editing_clone_icon.png)

![](images/manual/editing_clone_setting.png)

#### 1:N relational editing
To allow editing on the related table in mode 1: n , the **editing function must also be activated** (always in the same way) **also for the related table** present in the project layers list.

#### Constraints setting

**G3W-SUITE allows you to manage two types of constraints:**
 * **alphanumeric (SQL) / QGIS expressions constraints**
 * **geographic constraints**
 
Both work in terms of visualization and/or editing

##### Alphanumeric (SQL) / QGIS expressions Constraints
**Alphanumeric (SQL) / QGIS expression constraints allow you to define, for each published layer, the subset of features that can be viewed and/or edited by individual users and/or groups of users.**

This setting is also available for the AnonymousUser user

To activate this type of constraint, you must click, always at the level of the list of project layers, on the **`Alphanumeric constraints list` icon** ![](images/manual/icon_alpha_constraints.png).

Clicking on the icon will show the list of any existing alphanumeric constraints and the item **`+ New alphanumeric constraint`** to create a new constraints.

![](images/manual/editing_alpha_constrain_layer.png)

Clicking on the item **`+ New constraint`** will open a modal window which will allow you to define a name and a description for the new constraint.

In the form it is possible to specify whether this filter will act at the level:
 * display only
 * editing only
 * in both cases
 
![](images/manual/editing_alpha_constrain_layer_init.png)


After clicking the OK button, the constraint will appear in the list and can be parameterized using two ways of defining the rules:  

 * ![](images/manual/icon_alphaconstraints_setting.png) **Provider's language / SQL dialect**
 * ![](images/manual/icon_qgisconstraints_setting.png) **QGIS Expression**


###### Provider's language / SQL dialect Rules
Clicking on the icon ![](images/manual/icon_alphaconstraints_setting.png) will open a modal window which, by pressing the green button ![](images/manual/button_add.png), it will allow you to **define, for each user and/or group of users, the rules of the constraints**.

The individual rules must be defined via  the **`Provider's language`** or the **`SQL dialect Rules`** associated with the format of the geo-constraints layer (es. use standard SQL if your layer is a PostGis layer)

The single rules must refer to the layer’s attributes and values.

The **`Save icon`** ![](images/manual/icon_save.png) will allow you to validate the rules, in order to ensure proper functioning of the constraints itself.

![](images/manual/editing_alpha_constrain_setting.png)

Once all the constraints have been entered and validated, click on the **Close button** to confirm the rules.


###### QGIS Expressions Rules
Clicking on the icon **QGIS expression rules** ![](images/manual/icon_qgisconstraints_setting.png) will open a modal window which, by pressing the green button ![](images/manual/button_add.png), it will allow you to **define, for each user and/or group of users, the rules of the constraints**.

The individual rules must be defined via **`QGIS expression`** and this allows to have a great degree of freedom in the ways in which to set these rules.

See the paragraph dedicated to the functions available directly on the [QGIS manual](https://docs.qgis.org/3.16/en/docs/user_manual/working_with_vector/expression.html).

The **`Save icon`** ![](images/manual/icon_save.png) will allow you to validate the rules, in order to ensure proper functioning of the constraints itself.

![](images/manual/editing_qgisconstrain_setting.png)

Once all the constraints have been entered and validated, click on the **Close button** to confirm the rules.

In the dedicated panel it will be possible to check, modify and delete the defined rules.

![](images/manual/alpha_constrain_list.png)


##### Geo-constraints
**The online editing function also allows you to manage geo-constraints that allow the user to view and/or edit features only if they intersect or are contained within specific features of a second polygonal layer.**

This setting is also available for the AnonymousUser user

To activate a geographical constraint, you must click, always at the level of the list of project layers, on the **`Manage Geo-Constraints` icon** ![](images/manual/icon_constraints.png) which will appear once the online editing function is activated.

Clicking on the icon will show the list of any existing constraints and the item **`+ New geo-constraint`** to create a new geo-constraints.

![](images/manual/editing_constrain_layer.png)

Clicking on the item **`+ New geo-constraint`** will open a modal window which will allow you to **define the polygonal layer** (among those present in the project) **on which the constraint itself must be based**.

In the form it is possible to specify whether this filter will act at the level:
 * display only
 * editing only
 * in both cases

![](images/manual/geo_constrain_layer_init.png)


Once the layer has been defined, the constraint will appear in the list and can be parameterized using the **Rules icon** ![](images/manual/icon_constraints_setting.png)

Clicking on this icon will open a modal window which, by pressing the green button ![](images/manual/button_add.png), it will allow you to **define, for each user and/or group of users, the rules of the constraints**.

The individual rules must be defined via  the **`Provider's language`** or the **`SQL dialect Rules`** associated with the format of the geo-constraints layer (es. use standard SQL if your geo-constraint layer is a PostGis layer)

The single rules must refer to the layer’s attributes and values.

The **`Save icon`** ![](images/manual/icon_save.png) will allow you to validate the rules, in order to ensure proper functioning of the constraints itself.

![](images/manual/editing_geoconstrain_setting.png)

Once all the constraints have been entered and validated, click on the **Close button** to confirm the rules.

In the alphanumeric constraints list you can see a summary of the setted rules.

![](images/manual/editing_geoconstrain_layer_summary.png)


## Online editing tools on cartographic client
### Geographic and alphanumeric editing

**Once the online editing function has been activated and configured on one or more layers of a WebGis project, the `Editing layers` item, in the left bar of the cartographic client, will be shown.**

A click on the Editing layers item will allow you to go to the dedicated panel.

If the editing function is activated at the Admin level, it is also possible to start the online editing also through the **Editing icon** that appears at the level of the **search and query results form** of a vector layer.

![](images/manual/editing_client_start.png) 

![](images/manual/editing_client_tool.png)

**N.B.** If the editing function is activated at the Admin level, it is also possible to start the online editing also through the **Editing icon** that appears at the level of the **attibute tables** (for every records) and in the **search and query results form** (for every results)

By clicking on the Edit layer icon associated with the layer of interest, the editing tools will be displayed.

The available tools vary based on the powers/permissions of the logged in user.

![](images/manual/g3wclient_edit_tools.png)

In the case of many editable layers, a useful **filter** allows you to view only the layers of interest in the list.

If a layer has **1:N relations** with other layers, a specific icon will be shown to the left of its name.

A click on this icon will allow you to filter, and therefore show in the list, only the layers involved in the relations

![](images/manual/g3wclient_edit_relation_filters.png)

If the logged in user is an Administrator, there will be a link in the panel to access the features unlock session

#### Create and edit features

The tools available are the following:

**Geometric layers**
 * ![](images/manual/icon_feature_add.png) **Add feature:** to add a feature
  * ![](images/manual/icon_feature_attribute.png)**Update feature attribute:** to modify the attribute values associated with an existing feature
 * ![](images/manual/icon_feature_modify.png) **Update feature vertex:** to modify the shape of a geometry
 * ![](images/manual/icon_feature_remove.png) **Remove feature**
 * ![](images/manual/icon_feature_multiattribute.png) **Update attributes for selected features:** to modify the attribute values associated with more than one features
 * ![](images/manual/icon_feature_multiattribute_relations.png) **Add/Edit child records from one or more parent features:** if the layer being edited is the parent of one or more 1:N relations, it will be possible to select multiple parents and 
   * add one or more records to the child table (only if alphanumeric) associating them with all the selected parents
   * modify one or more fields of all the records of the child table, connected to the selected father geometries
 * ![](images/manual/icon_feature_move.png) **Move feature:** to move a feature
 * ![](icon_feature_rotate.png) **Rotate feature:** to rotate a geoemtry. The rotation of point geometries is linked to the presence of a field called "rotation" in the attribute table, on which the direction of the symbology associated with the point is based. 
  * ![](images/manual/icon_feature_paste.png) **Paste features from other layers:** click on the icon, select a layer from those available in the drop-down menu (limited based on compatible geometry), select a feature,, fill in the attributes and press the green **Insert/Edit** button. The copy and paste operation can also be performed by referring to geometries deriving from local layers added by the user using the **AddLayer** tool.

 * ![](images/manual/icon_feature_copy.png) **Copy features:** to copy one or more features from the same layer

 * ![](images/manual/icon_feature_add_part.png) **Add part** of a multi-geometry
 * ![](images/manual/icon_feature_delete_part.png) **Delete part** of a multi-geometry

 * ![](images/manual/icon_feature_split.png) **Split features:** to split one or more features

 * ![](images/manual/icon_feature_dissolve.png) **Dissolve features:** to dissolve two or more features

A help panel will describe the steps to take for copy, dissolve and split operations.

In case of interaction of the editing tools with overlapping geometries, an intermediate step will allow to select the effective geometry on which to operate.

##### Snap and other available function
Activating the Add features and Update feature vertex tools allows you to:
 * activate the **intralayer snap** function
 * activate the **snap between layers**, but only if both layers are in editing mode
 * activate the **interactive area and length measure** function

**Alphanumeric layer**
 * ![](images/manual/icon_record_add.png) **Add feature:** to add a record to the alphanumeric table
 * ![](images/manual/icon_record_modify.png) **Modify features:** to modify the attributes of an existing record

Whenever a new feature/record is added or an existing feature/record is update, the attribute editing form and the respective editing widgets will be displayed as defined at the QGIS project level.

![](images/manual/editing_form.png)

Any **mandatory fields will be marked with an asterisk**.

Any **unfulfilled constraints will be highlighted** with specific warning messages shown **in red**.

The changes made can be saved only after satisfying any constraints of mandatory and/or uniqueness.

For this reason the green button **SAVE** will be disabled until all constraints are met.



### 1:N and N:M related tables editing
**G3W-SUITE allows for relational editing**; for this to be possible it is necessary that:
 * on the published QGIS project there are one or more geographic layers related (1:N) with one or more alphanumeric tables
 * on the administration panel the editing function has been activated both on the parent layer and on the child layers
 * the operator user is enabled for the editing function on these layers

**The activation, at the cartographic client level, of the editing mode on the parent layer automatically determines the possibility of also managing the information on the related table.**

**Important:** to be able to edit related data, the relations must be inserted in the form of the parent layer, defined using the Drag and Drop designer method in the Layer Properties.

After the editing has been activated on a record in the parent table, an editing icon (pen) will appear at the level of the relations references.

![](images/manual/editing_form2.png)

Moving on the macro tab relating to one of the child layers, the list of records already associated with the edited feature will be displayed

![](images/manual/editing_form_relations.png)

Clicking on it the form switch in a session dedicated to the edit of the relational table where it will be possible: **create and add a new records** related to the edited feature
 * **associate an existing records** (linked to other features or orphan) to the edited feature
 * **modify the records** currently associated with the edited feature

#### Creation of a new related child records

To add a new record in the child layer, you first need to access the relations itself.

By clicking on the icon **`Create and link a new relation`** ![](images/manual/icon_plus.png)(located at the top right) it will be show the attribute form to insert a new record.

You can fill in the individual attributes and save the new record. **The change must be validated by clicking on the `Save` button at the bottom of the form.**

If the child layer is of the geometric type, it will be possible to add a new feature in two ways:

 * **classic geometry editing**
 * **copy and paste a feature from other layers (WFS included)** present in the project and characterized by the same type of geometry

![](images/manual/editing_form_relations.png)

To copy a geometry from another layer operate in this order:
 * choose the layer from which to copy the feature from the **Copy feature from other layer** drop-down menu
   * the layers shown will be in accordance with the geometry of the edited layer
   * the layers settable not querable in the QGIS project will not be shown
   * the layers shown also include those added to the map by the user using the **AddLayer** tool 
   * neither the layer being edited (child of the relation) nor the parent layer of the relation will be shown in the list
 * click on the **Copy** button below
 * on the map, click on the feature to copy relating to the chosen layer
 * fill in the attributes associated with the new feature created
 * click on the green **Save and exit** button

#### Association of an existing record
By clicking on the icon **`Join a relation to this feature`** ![](images/manual/icon_join.png) (located at the top right) you can associate a record, already linked to other features or orphaned, to the edited feature.

In the new window displayed:
 * the list of all orphaned or already associated records will be displayed;
 * a generic filter will allow you to locate the record of interest;
 * by clicking on the individual records these will be associated with the edited features and, possibly, dissociated from other features

#### Modification of an already associated record
A series of icons appear to the right of each record associated with the edited feature.


In the case of 1:N relations with alphanumeric layers:
 * ![](images/manual/icon_record_attribute.png)**Update feature attribute:** modify the values associated with the attributes of this record; the change must be validated by clicking on the **Save** button at the bottom of the form.
 * ![](images/manual/icon_record_copy.png)**Copy feature:** duplicate a record with the possibility to change the values in the new record; the change must be validated by clicking on the **Save** button at the bottom of the form.
 * ![](images/manual/icon_record_erase.png) **Delete feature:** permanently delete the record


In the case of 1:N relations with vectorial layers:
 * ![](images/manual/icon_record_attribute.png) **Update feature attribute:** modify the values associated with the attributes of this feature; the change must be validated by clicking on the **Save** button at the bottom of the form.
 * ![](images/manual/icon_record_erase.png) **Delete feature:** permanently delete the record
 * ![](images/manual/icon_feature_modify.png) **Update deature vertex:** modify the shape of the geometry
 * ![](images/manual/icon_feature_move.png) **Move feature:** move the geometry
 * ![](images/manual/icon_join_erase.png) **Unlink relation:** to dissociate the record from the edited feature, the record will not be deleted but will become an orphan


#### Saving changes
Saving all the changes made in an editing session can be done in three ways:
 * by clicking on the **`diskette icon`** ![](images/manual/icon_save_all.png) placed at the top right. This icon is very useful in the case of complex projects with related data on various levels as it allows you to **save any changes made regardless of the form on which you are at the time of saving.** You will not be asked to confirm the save.
  * by clicking on the **`diskette icon`** ![](images/manual/icon_disk.png) placed at the top left of the TOC. The icon will only be active once you exit the attribute editing form. The changes made will be saved and you can continue making new changes. You will not be asked to confirm the save.
 * exiting edit mode by clicking on the **`Edit layer icon`** ![](images/manual/icon_edit3.png). 
 
Exiting edit mode, a modal window will be displayed which will show the list of changes made and the request for confirmation or not of saving them.

![](images/manual/editing_client_save.png)

Remember that during the editing phase the **undo/redo icons** allow you to delete/restore the latest changes made. 
 

## Geocoding map control use case for populating project layers
A specific editing function is associated with the Geocoding map control.

See the dedicate paragha

In fact, it is possible to use the results deriving from the providers associated with this map control (OSM, Bing Streets, Bing Places) to populate point layers present within the webgis service.

Below are the settings and steps to perform for this purpose.

#### Activation of Geocoding map control at Cartographic Group level
First of all, **Geocoding** must be associated with the Cartographic Group by selecting it at the **Base Layers and Map default features** session level. This will ensure that the tool is present on all WebGis services published within the Group.

![](images/manual/geocoding_providers.png)

#### Definition of the providers to use for each WebGis service
In the publication form of your QGIS project you need to define the providers to be associated with the Geocoding map control:
 * **Nominatim (OSM):** addresses based on OpenStreetMap
 * **Bing Streets:** Addresses based on Bing maps
 * **Bing Places:** places based on Bing maps (service available only for the USA)

The association is created at the **Geocoding providers** session level.

The use of the providers Bing Streets and Bing Places requires the acquisition of a free Bing API.

See [Settings](https://g3w-suite.readthedocs.io/en/v3.9.x/settings.html) section for the API key definition.

#### Using the Geocoding map control on the WebGis service
The Geocoding tool allows you to search for addresses and places (based on active providers) and view their position on the map and the associated information on the information panel on the right.

It is possible to load markers relating to multiple searches into the map.

![](images/manual/geocoding_marker.png)

On the right of the search tool there will now be three new icons:
 * **Clear markers selection:** to delete all markers inserted on the map
 * **Toogle markers visibility:** to activate/deactivate the display of markers
 * **Toogle sidebar panel:** to view the list of markers loaded in the map and their attributes on the right panel

![](images/manual/geocoding_icons.png)

#### Insert the markers present in the map into a vector layer
This function allows you to insert the results, obtained through the Geocoding map control, at the level of one or more layers, exclusively point-like, present in the WebGis service.

To be able to carry out this operation, the online editing functionality must be active on these layers.

Only in this case will there be a pencil icon at the search results level.

Clicking on this icon will show the list of point layers on which online editing is enabled.

After choosing the layer on which to insert the result, click on the pencil icon to the right of the layer name to determine the insertion of the point on the layer itself.

![](images/manual/geocoding_insert.png)

On the right, the information panel will open which will show the attributes of the self-editing layer filled in only to the fields of the same name as those of the source markers.

![](images/manual/geocoding_insert_attributes.png)

By clicking on the green **Insert/Edit** button the point will be permanently saved on the layer in use.
