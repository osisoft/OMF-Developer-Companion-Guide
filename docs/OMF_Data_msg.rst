Data Message 
============


In PI Server, a data message serves three different purposes: 

  1: Creating concrete assets from a static type definition, 
  
  2: Linking assets and containers together and forming AF Element/Attribute structure
  
  3: Feeding container data values into PI Data Archive PI points. 


Creating Assets 
---------------

In a PI Server, an asset is interpreted as an AF Element. The properties in the asset objects are interpreted as follows: 
  

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
  
Note that in values array, properties of each asset designated with the ``isindex`` keyword must be unique. 
The scope of uniqueness is the producer.   

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

Three types of link objects are supported by PI Server: 

1. Root asset links, which create top level AF elements

2. Parent/child asset links, which create parent/child tree structure between AF elements, 

3. Asset/container links, which attach all container properties as PI point referenced AF attributes to the AF element. 

Root Asset Links 
----------------

Root asset is presented in PI Server as a top-level AF element in the AF structure tree, which is a child 
of a parent AF Element created for your OMF application instance (producer). For this type of the link, 
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
|                |             |             |               | creation to isindex property.                |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | typeversion | String      | Optional      | Optional version of the type to be linked to |
|                |             |             |               | or from. If omitted version 1.0.0.0 is       |
|                |             |             |               | assumed.                                     |
+----------------+-------------+-------------+---------------+----------------------------------------------+

Notes:
  Only asset objects of type static can be used in the target property. 
  Static type ID specified in the target typeid must be put into the source typeid. 

Parent/Child Asset Links 
------------------------

Parent/Child relationship between assets presented in PI Server as child AF element attached to a 
top level or any other sub-tree AF element parent. For this type of the link, source and target 
properties have the following keywords: 


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
| target         | typeid      | String      | Required      | ID of the static type definition used by the |
|                |             |             |               | asset, which will become a child of the      |
|                |             |             |               | source asset.                                |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | index       | String      | Required      | Asset name value as provided during its      |
|                |             |             |               | creation to isindex property.                |
+----------------+-------------+-------------+---------------+----------------------------------------------+
| target         | typeversion | String      | Optional      | Optional version of the type to be linked to |
|                |             |             |               | or from. If omitted version 1.0.0.0 is       |
|                |             |             |               | assumed.                                     |
+----------------+-------------+-------------+---------------+----------------------------------------------+

Note that only asset objects of type static can be used in both source and target properties. 

Asset/Container Links 
---------------------

Asset/Container relationship between assets and container properties presented in PI Server as children 
AF Attributes configured with PI point references under the asset AF Element parent. For this type of link, 
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


Feeding data to PI points 
-------------------------

In PI Server, container values are presented as timeseries snapshot values sent to PI points. The properties 
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
  All type definitions, containers, and assets, and the linkage should be sent to the Relay ingress endpoint 
  only one time: when the OMF application instance is started for the first time. Under normal circumstances, 
  it should not be re-transmitted every time the producer is restarted. The Relay will have all necessary 
  information in its cache to successfully receive only container data values. 

  Dynamic type of the container may have more than one property (except of isindex, which always serves as a timestamp). 
  Remember that each property is presented as PI point in PI Server. Values for every property of the container, 
  specified in its type definition, must be provided to the container values. All of these values will be sent 
  to PI Data Archive with the same timestamp. If you omit one of the values, you will end up with its default 
  in the Archive. For example, if you omit a value to a number property, PI point will receive a value of zero, 
  which might be undesirable. 
  
  
  
