Data Message
============


In an on-premises PI System, a data message serves three different purposes:

1. Creating concrete assets from a static type definition,
2. Linking assets and containers together and forming Asset Framework Element/Attribute structure
3. Feeding container data values into PI Data Archive PI points.


Creating Assets
---------------

In an on-premises PI System, an asset is interpreted as an Asset Framework Element. The properties in the asset objects are interpreted as follows:


+----------------+-------------+---------------+------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                    |
+================+=============+===============+============================================================+
| typeid         | String      | Required      | ID of the static type used by the Assets.                  |
+----------------+-------------+---------------+------------------------------------------------------------+
| typeversion    | String      | Optional      | Version of the static type used by the Assets. If omitted, |
|                |             |               | version 1.0.0.0 is used.                                   |
+----------------+-------------+---------------+------------------------------------------------------------+
| values         | Array       | Required      | Array of Asset objects. Each object contains a key-value   |
|                |             |               | pairs representing property names and their values of the  |
|                |             |               | static type used by the Asset.                             |
+----------------+-------------+---------------+------------------------------------------------------------+

Note that in the values array, properties of each asset designated with the ``isindex`` keyword must be unique.
The scope of uniqueness is the OMF application.

When creating an asset from the type which has version property set to a value other than the default value of 1.0.0.0,
you must specify the same version to the ``typeversion`` property of the container. Otherwise, the default
default ``typeversion`` of 1.0.0.0 is assumed.


Creating Links
--------------

Array of Links is a pre-defined type with the typeid ``__Link``. It has the following properties:

+----------------+-------------+---------------+------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                    |
+================+=============+===============+============================================================+
| typeid         | String      | Required      | Value must be __Link                                       |
+----------------+-------------+---------------+------------------------------------------------------------+
| values         | Array       | Required      | Array of link objects.                                     |
+----------------+-------------+---------------+------------------------------------------------------------+


Each link object has the following properties:

+----------------+-------------+---------------+------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                    |
+================+=============+===============+============================================================+
| source         | String      | Required      | An object representing the source of the link or its       |
|                |             |               | parent.                                                    |
+----------------+-------------+---------------+------------------------------------------------------------+
| target         | String      | Required      | An object representing the target of the link or its child.|
+----------------+-------------+---------------+------------------------------------------------------------+

Three types of link objects are supported by PI System:

1. Root asset links, which create top level Asset Framework elements

2. Parent/child asset links, which create parent/child tree structure between Asset Framework elements,

3. Asset/container links, which attach all container properties as PI point referenced Asset Framework
   attributes to the Asset Framework element.

Root Asset Links
----------------

The Root asset is presented in an on-premises PI System as a top-level Asset Framework element in the Asset Framework structure tree,
which is a child of a parent Asset Framework Element created for your OMF application. For this type of the link,
the source and target properties have the following keywords:


+----------------+-------------+-------------+---------------+----------------------------------------------+
| Property       | Keyword     | Type        | Optionality   | Details                                      |
+================+=============+=============+===============+==============================================+
| source         | typeid      | String      | Required      | ID of the static type definition used by the |
|                |             |             |               | asset.                                       |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| source         | index       | String      | Required      | Value must be set to __ROOT.                 |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | typeid      | String      | Required      | ID of the static type definition used by the |
|                |             |             |               | asset.                                       |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | index       | String      | Required      | Asset name value as provided during its      |
|                |             |             |               | creation to ``isindex`` property.            |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | typeversion | String      | Optional      | Optional version of the type to be linked to |
|                |             |             |               | or from. If omitted version 1.0.0.0 is       |
|                |             |             |               | assumed.                                     |
+----------------+-------------+-------------+---------------+----------------------------------------------+

Notes:
  Only asset objects of type static can be used in the target property.
  Static type ``ID`` specified in the target ``typeid`` must be put into the source ``typeid``.

When linking a target asset from the type which has version property set to a value other than the default value of 1.0.0.0,
you must specify the same version to the ``typeversion`` property of the target property of the asset been linked.
Otherwise, the default ``typeversion`` of 1.0.0.0 is assumed.

Parent/Child Asset Links
------------------------

Parent/Child relationship between assets presented in PI System as child Asset Framework element attached to a
top level or any other sub-tree Asset Framework element parent. For this type of the link, source and target
properties have the following keywords:


+----------------+-------------+-------------+---------------+----------------------------------------------+
| Property       | Keyword     | Type        | Optionality   | Details                                      |
+================+=============+=============+===============+==============================================+
| source         | typeid      | String      | Required      | ID of the static type definition used by the |
|                |             |             |               | asset, which will become a parent of the     |
|                |             |             |               | target asset.                                |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| source         | index       | String      | Required      | Asset name value as provided during its      |
|                |             |             |               | creation to ``isindex`` property.            |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| source         | typeversion | String      | Optional      | Optional version of the type to be linked to |
|                |             |             |               | or from. If omitted, version 1.0.0.0 is      |
|                |             |             |               | assumed.                                     |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | typeid      | String      | Required      | ID of the static type definition used by the |
|                |             |             |               | asset, which will become a child of the      |
|                |             |             |               | source asset.                                |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | index       | String      | Required      | Asset name value as provided during its      |
|                |             |             |               | creation to ``isindex`` property.            |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | typeversion | String      | Optional      | Optional version of the type to be linked to |
|                |             |             |               | or from. If omitted, version 1.0.0.0 is      |
|                |             |             |               | assumed.                                     |
+----------------+-------------+-------------+---------------+----------------------------------------------+

Note that only asset objects of type static can be used in both source and target properties.

When linking an asset from the type which has version property set to a value other than the default value of 1.0.0.0,
you must specify the same version to the ``typeversion`` property of the source or target properties of the asset been linked.
Otherwise, the default ``typeversion`` of 1.0.0.0 is assumed.

Asset/Container Links
---------------------

Asset/Container relationship between assets and container properties presented in an on-premises PI System as children
Asset Framework Attributes configured with PI point references under the Asset Framework Element parent. For this type of link, 
source and target properties have the following keywords:


+----------------+-------------+-------------+---------------+----------------------------------------------+
| Property       | Keyword     | Type        | Optionality   | Details                                      |
+================+=============+=============+===============+==============================================+
| source         | typeid      | String      | Required      | ID of the static type definition used by the |
|                |             |             |               | asset, which will become a parent of the     |
|                |             |             |               | target asset.                                |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| source         | index       | String      | Required      | Asset name value as provided during its      |
|                |             |             |               | creation to isindex property.                |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| source         | typeversion | String      | Optional      | Optional version of the type to be linked to |
|                |             |             |               | or from. If omitted version 1.0.0.0 is       |
|                |             |             |               | assumed.                                     |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | containerid | String      | Required      | ID of the container created from dynamic     |
|                |             |             |               | type definition.                             |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | typeversion | String      | Optional      | Optional version of the type to be linked to |
|                |             |             |               | or from.                                     |
+----------------+-------------+-------------+---------------+----------------------------------------------+

Note that only asset objects of static type can be used in the source property. In addition, only container
objects of dynamic types can be used in the target property.

When linking a source asset from the type which has version property set to a value other than the default value of 1.0.0.0,
you must specify the same version to the ``typeversion`` property of the source property of the asset been linked.
Otherwise, the default ``typeversion`` of 1.0.0.0 is assumed.

It is not necessary to provide the ``typeversion`` property for the target property: containers already have the ``typeversion``
information from their definitions.

Feeding data to PI points
-------------------------

In an on-premises PI System, container values are presented as timeseries event values sent to Data Archive PI points. The properties
in the container objects are interpreted as follows:

+----------------+-------------+---------------+------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                    |
+================+=============+===============+============================================================+
| containerid    | String      | Required      | ID of the container created from  dynamic type.            |
+----------------+-------------+---------------+------------------------------------------------------------+
| typeversion    | String      | Optional      | Version of the dynamic type used by the Container. If      |
|                |             |               | omitted, version 1.0.0.0 is used.                          |
+----------------+-------------+---------------+------------------------------------------------------------+
|values          | Array       | Required      | Array of timeseries data value objects. Each object        |
|                |             |               | contains a key-value pairs representing property names and |
|                |             |               | their values of the dynamic type used by the Container.    |
+----------------+-------------+---------------+------------------------------------------------------------+

Notes:
  All type definitions, containers, and assets, and the linkage, should be sent to the PI Connector Relay ingress endpoint
  only one time: when the OMF application is started for the first time. Under normal circumstances,
  it should not be re-transmitting this information every time the OMF application is restarted. The PI Connector Relay will have all necessary
  information in its cache to successfully receive only container data values.

  Dynamic type of the container may have more than one property (except ``isindex``, which serves as a timestamp).
  Remember that each property is presented as a PI point in an on-premises PI System. Values for every property of the container
  (specified in its type definition, must be provided to the container values. All of the values are sent
  to PI Data Archive with the same timestamp. Omitting one of the values results in the default being stored
  in the Archive. For example, if you omit a value to a number property, PI point will receive a value of zero,
  which might not be what was intended.
