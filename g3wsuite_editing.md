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

The editing function manages both direct editing on geometric and alphanumeric layers, and editing on layers in a 1:N or N:M relations, also both geometric and alphanumeric.

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
 * **`Relation reference: with some option limitation`**
 * **`Checkbox`**
 * **`Date/time`:** management limited to the date
 * **`Attachment`**
 * **`Range`**
 * **`Text edit`** multiline and html included
 * **`Unique values`**: this widget will be equipped with a **pick layer** tool at the cartographic client level
 * **`Value map`**
 * **`Value relations`** with `QGIS expression-based filter` management, the suite not support `sort by value` and `allow multiple selections` options

 **The expression-based filter can also be dependent on the values of other fields on the form and useful to create cascading drill down.**
   
With regard to the **`Attachment widget`**, it is necessary to specify that the association of a multimedia file with a feature requires that this file is uploaded to a dedicated space (exposed on the web) on the server and that the association takes place via a URL that refers to that file.

This solution allows you to consult the associated attachments also from QGIS or from other GIS software.

**Additional settings at single layer level**

In the **`Attribute Form`** section of the **`Layer Properties`** it is also possible to define for every field:
 * **enable/disable editing**
 * **mandatory and/or unique constraints**
 * **range of acceptable values** through the **Range** widget
 * **default values**
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
 * **define the editing activation scale** (only for geometric tables)
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

Through these two optional settings it is possible to define two fields of the table of the layer being edited automatically filled in.

These two fields will contain references to G3W-SUITE users who creators or modifiers of the individual features edited.

All settings defined at the QGIS level for these fields (eg default values) will no longer be considered.

It is recommended that these fields be set as non-editable at the project level.

#### Activation of online editing on multiple layers at the same time
In the case of a project in which it is necessary to activate the online editing function on many layers, by defining the same options for each of them it is possible to use the Project layer action function.

![](images/manual/multi_layer_icon.png)

![](images/manual/multi_layer_form.png)


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


## Online editing tools at cartographic client level
### Geographic and alphanumeric editing

**Once the online editing function has been activated and configured on one or more layers of a WebGis project, the `Editing` item, inside the `Tools` menu of the cartographic client, will be shown.**

In the case of many editable layers, a useful **filter** allows you to view only the layers of interest in the list.

If the editing function is activated at the Admin level, it is also possible to start the online editing also through the **Editing icon** that appears at the level of the **search and query results form** of a vector layer.

![](images/manual/editing_client_start.png) 

![](images/manual/editing_client_tool.png)

By clicking on the **`Data Layers`** item, the side menu will show the editing tools for all the layers on which this function is activated.

The actual activation of the editing function for the individual layers will take place by clicking on the **`Edit layer`** icon.

#### Create and edit features

The tools available are the following:

**Geometric layers**
 * ![](images/manual/icon_feature_add.png) **Add feature:** to add a feature
  * ![](images/manual/icon_feature_attribute.png)**Update feature attribute:** to modify the attribute values associated with an existing feature
 * ![](images/manual/icon_feature_modify.png) **Update feature vertex:** to modify the shape of a geometry
 * ![](images/manual/icon_feature_remove.png) **Remove feature**
 * ![](images/manual/icon_feature_multiattribute.png) **Update attributes for selected features:** to modify the attribute values associated with more than one features
 
 * ![](images/manual/icon_feature_move.png) **Move feature:** to move a feature

  * ![](images/manual/icon_feature_paste.png) **Paste features from other layers:** query another layer with the same geometry, select the features to copy, press the **`Paste`** icon, select the features to paste and confirm. The copy and paste operation can also be performed by referring to geometries deriving from layers added by the user using the AddLayer tool.

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
 * on the published QGIS project there are one or more geographic layers related (1:n or N:M) with one or more alphanumeric tables
 * on the administration panel the editing function has been activated both on the parent layer and on the child layers
 * the operator user is enabled for the editing function on these layers

**The activation, at the cartographic client level, of the editing mode on the parent layer automatically determines the possibility of also managing the information on the related table.**

**Important:** to be able to edit related data, the relations must be inserted in the form of the parent layer, defined using the Drag and Drop designer method in the Layer Properties.

After the editing has been activated on a record in the parent table, an editing icon (pen) will appear at the level of the relations references.

![](images/manual/editing_form2.png)

Moving on the macro tab relating to one of the child layers, the list of records already associated with the edited feature will be displayed

![](images/manual/editing_form_relations.png)

Clicking on it the form switch in a session dedicated to the edit of the relational table where it will be possible: * **create and add a new records** related to the edited feature
 * **associate an existing records** (linked to other features or orphan) to the edited feature
 * **modify the records** currently associated with the edited feature

#### Creation of a new related child records
By clicking on the icon **`Create and link a new relation`** ![](images/manual/icon_plus.png)(located at the top right) it will be show the attribute form to insert a new record.

You can fill in the individual attributes and save the new record. **The change must be validated by clicking on the `Save` button at the bottom of the form.**

If the child layer is of the geometric type, it will be possible to add a new feature in two ways:

 * **classic geometry editing**
 * **copy and paste a feature from other layers (WFS included)** present in the project and characterized by the same type of geometry

![](images/manual/editing_form_relations.png)

To copy a geometry from another layer operate in this order:
 * select the layer from which to copy the geometry from the drop-down menu
 * click on the Copy button on the right of the list
 * click on the feature of the layer to copy from
 * select the feature of interest by consulting its attributes and/or zooming in on it, only one feature per operation
 * fill in the attribute form of the pasted feature, some fields may already be filled in if they are omonymus to those of the feature's origin layer

#### Association of an existing record
By clicking on the icon **`Join a relation to this feature`** ![](images/manual/icon_join.png) (located at the top right) you can associate a record, already linked to other features or orphaned, to the edited feature.

In the new window displayed:
 * the list of all orphaned or already associated records will be displayed;
 * a generic filter will allow you to locate the record of interest;
 * by clicking on the individual records these will be associated with the edited features and, possibly, dissociated from other features

#### Modification of an already associated record
A series of icons appear to the right of each record associated with the edited feature:
 * ![](images/manual/icon_join_erase.png) **Unlink relation:** to dissociate the record from the edited feature, the record will not be deleted but will become an orphan
 * ![](images/manual/icon_record_erase.png) **Delete feature:** permanently delete the record
 * ![](images/manual/icon_record_attribute.png) **Update feature:** modify the values associated with the attributes of this record; the change must be validated by clicking on the Save button at the bottom of the form.

#### Saving changes
Saving all the changes made in an editing session can be done in three ways:
 * by clicking on the **`diskette icon`** ![](images/manual/icon_disk.png) placed at the top right. This icon is very useful in the case of complex projects with related data on various levels as it allows you to **save any changes made regardless of the form on which you are at the time of saving.** You will not be asked to confirm the save.
  * by clicking on the **`diskette icon`** ![](images/manual/icon_save_all.png) placed at the top left of the TOC. The icon will only be active once you exit the attribute editing form. The changes made will be saved and you can continue making new changes. You will not be asked to confirm the save.
 * bexiting edit mode by clicking on the **`Edit layer icon`** ![](images/manual/icon_edit2.png). 
 
BExiting edit mode, a modal window will be displayed which will show the list of changes made and the request for confirmation or not of saving them.

![](images/manual/editing_client_save.png)

Remember that during the editing phase the **undo/redo icons** allow you to delete/restore the latest changes made. 
 
