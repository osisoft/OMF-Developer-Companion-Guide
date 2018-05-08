Additional development considerations
=====================================

This sections contains additional information you should be aware of when developing OMF applications.

OMF application type considerations
-----------------------------------

You specify the OMF application type when the OMF application is registered with PI Data Collection Manager.
The application type serves to classify a set of OMF applications that send the same metadata and time-series data to the on-premise PI System.

For example, you might have multiple OMF applications running on equipment with the same make, model, and I/O configuration.
All of these OMF applications generate the same reference models for each device, and send events of the same set of device
measurements to the on-premise PI System. In this case, the type and container definitions for each of the OMF applications are identical.
Note the following:

* You create a single OMF type definition with a unique Id that is used by each OMF application of the same type. All other instances should
  use the same definition without any type changes. If an OMF application attempts to redefine an existing OMF type, an HTTP
  response status code of 400 (Bad Request) is returned with error code 10 (Type redefinition).
* You create a single OMF container definition with a unique ``CONTAINERID`` that is used by each OMF application of the same type. All other instances should
  use the same definition without any OMF container changes. If an OMF application attempts to redefine an existing OMF
  container, an HTTP response status code 400 (Bad Request) is returned, with error code 12 (Container redefinition).
* At any time, any instance of a given OMF application type may define and send a new OMF type or container definition.
  This definition then becomes available to all OMF applications of the same OMF application type.
* The OMF application does not share OMF asset and link definitions between other OMF applications of
  the same type. Therefore:

  * If the OMF application is the first application of the given OMF application type, it must send OMF type and
    container definitions followed by OMF asset and link definitions the first time it is run.
  * If the instance is not the first OMF application of the given OMF application type, it must send OMF asset and link
    definitions the first time it is run; OMF types and containers are optional.
  * The OMF asset identifier (which is the value assigned to the OMF static type property with the ``isindex`` designation),
    is not required to be unique across all instances, but is case-sensitive in link definitions.
  * The OMF link definition is not currently immutable. After the OMF link definition is created, it can be redefined;
    however, care must be taken. For more information see `OMF link definition considerations`_.

OMF type version considerations
-------------------------------

The OMF type version allows you to modify OMF type definitions in a limited way. Note that using versioning of an existing
OMF static type does not create additional Asset Framework Element Templates, and only shows newly added type properties as template
Attributes. In the Asset Framework Element structure, removed attributes have a value of Excluded. Note that as with all other OMF
definitions, currently OMF supports only create actions, which means that you can create new versions, but you cannot
change or delete existing ones.

The following modifications to OMF type definitions between different versions are allowed:

-	Adding new properties to the type
-	Removing properties from the type

The following modifications are not allowed:

-	Changing the classification of the type
-	Changing the type of the existing property

The following rules apply when using the ``typeversion`` keyword with container, asset, and link definitions:

* To use a new version of a dynamic type, new container definitions must be created. Existing container definition
  cannot be redefined with the new version of the dynamic type. To specify the new container, you must use the ``typeversion``
  keyword, with the value set to the new version of the dynamic type.
* To use a new version of a static type, new asset definitions must be created. To specify the new asset that does not use
  the default type version, you must specify the ``typeversion`` keyword, with the value set to the new version of the
  static type. The asset redefinition rule is not strict, and you may redefine an existing asset to use another version
  of the static type.
* To link a new version of a static type, you must use the ``typeversion`` keyword, with the value set to the new version
  of the static type used to define the asset. Note that containers do not require ``typeversion`` during linkage, as
  they have this information since definition.


OMF link definition considerations
----------------------------------

OMF link definition creates Asset Framework Templates, Enumeration Sets, Attribute Templates, and finally instantiates Asset Framework Elements and
Attributes from the templates, and builds Asset Framework reference model structure. In other words, link definitions creates everything related
to Asset Framework and metadata storage. It does not participate in Data Archive modifications – PI point creation happens when OMF
application sends events to the defined containers.

Links between root, assets and containers are not currently immutable, so special care should be taken to not redefine
them unintentionally.
