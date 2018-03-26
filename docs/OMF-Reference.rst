
OMF reference information
=========================

The OMF specification (located `here <http://omf-docs.osisoft.com/en/v1.0/>`_) is generic in that it does not specify a particular back-end system. 

This topic is a companion to the OMF specification which describes how OMF is interpreted by PI System back-end system. 

Size Limits and Special Characters 
----------------------------------

The maximum size of OMF message content is 192Kb. 

The OMF application Name and Type (which are specified when registering your instance with PI Data Collection Manager),
should be fewer than 255 characters in length, and should not contain any control characters or any of the characters from
the following list:

::

  *'?;{}[]|`"\ 
 

All OMF JSON identities should be less than 255 characters in length, and should not contain any control characters 
from the following list: 

::

  *'?;{}[]|`"\ 

Note that it is not possible to specify the maximum length of several names or identities at once (similar to the PI 
System back-end which are presented as concatenations of each other). For example, if a PI AF Element Template name 
consists of the following parts: 

OMF.{*OMF application type*} 
Connector.{*OMF type definition identifier* | *OMF type definition name*}. 

The Maximum length of template names should be less than 260 characters (see *PI AF SDK Reference*). 
Therefore, you must calculate character lengths for both OMF application type, and OMF type definition ID and name. 

As a rule of thumb, keep the sizes of all names and identifies to 50 – 60 characters at most (although even these sizes 
might result in your application being difficult to read and understand.)
 
Message Headers
---------------

A description of each of the headers can be found in the *OMF specification*. When sending messages to a PI System, the value 
of the ``producertoken`` header must be set to a Producer Token that is obtained from the PI Data Collection Manager. 

The Producer Token is used to authenticate the sender and to authorize the sender to feed data to PI Connector 
Relay ingress endpoint. 
 
Message Types 
-------------

OMF message types fall into three categories: Type, Container, and Data, which are described below. 

Type Message 
  In a PI System, types are interpreted as either asset types or container types depending on type classification. 
  The keywords in the type definition are interpreted as follows: 


+----------------+-------------+---------------+------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                    |
+================+=============+===============+============================================================+
| Id             | String      | Required      | Unique identifier of the type. See below for details.      |
+----------------+-------------+---------------+------------------------------------------------------------+
| classification | String      | Required      | Specifies whether the type is static or dynamic. See       |
|                |             |               | below sections for details.                                |
+----------------+-------------+---------------+------------------------------------------------------------+
| type           | String      | Required      | Inherited from JSON Schema. Must be set to object.         |
+----------------+-------------+---------------+------------------------------------------------------------+
| properties     | Object      | Required      | Key-value pairs representing properties of the type.       |
+----------------+-------------+---------------+------------------------------------------------------------+
| Name           | String      | Optional      | Friendly name of the type.                                 |
+----------------+-------------+---------------+------------------------------------------------------------+
| description    | String      | Optional      | Description of the type.                                   |
+----------------+-------------+---------------+------------------------------------------------------------+
| version        | String      | Optional      | Version of the type. If omitted version 1.0.0.0 is assumed.|
+----------------+-------------+---------------+------------------------------------------------------------+
| tags           | Array       | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+
| metadata       | Object      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+


The ID value must be unique for all producers (OMF application instances) of a given type, which you specify 
during producer registration. This means that in advanced scenarios, you may send type definitions only once 
for all registered producers of this OMF application type – all of them will reuse the same definition cached 
in the Relay, in the following folder:

::

  C:\ProgramData\OSIsoft\Tau\Relay.ConnectorHost folder. 
 
Types with classification Static 
  A type with classification ``static`` represents metadata describing an asset type as AF Element Template 
  in the PI System. 

  The Id property of the static type is used to create and link concrete assets, to create AF Elements and 
  their structure in the PI System. 
  
  If the optional name property is omitted from the definition, the Id value is used to name the AF Element 
  Template; otherwise the name value is used. The template name is assembled from the following parts: 
  
::

  OMF.{producer type as provided during its registration} Connector.{id|name} 
  
  Each property of the type, except those that are marked with keywords ``isindex`` or ``isname``, represent 
  Configuration AF Attributes (not referencing any data) in the AF Template. 
 
Types with classification Dynamic 
  Type with classification dynamic represents a data stream type (set of observed or calculated values) 
  presented in PI System as PI points. 
  Each property of this type, except the property with the keyword ``isindex``, represent PI point referenced 
  AF Attribute in the AF Template, after you link this type under a static type. 
 
Property Types and Formats 
  The following keywords are used to define a Type Property: 


+----------------+-------------+---------------+------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                    |
+================+=============+===============+============================================================+
| type           | String      | Required      | Required type of the Type Property which must match one of |
|                |             |               | those listed in the table below.                           |
+----------------+-------------+---------------+------------------------------------------------------------+
| format         | String      | Optional      | Optional format of the Type Propety type that, if          |
|                |             |               | specified, must be from the table below.                   |
+----------------+-------------+---------------+------------------------------------------------------------+
| isindex        | Boolean     | Required      | One Property must be designated as the index by supplying  |
|                |             |               | the isindex keyword with a value of true. The designated   |
|                |             |               | isindex property is used to uniquely identify discrete     |
|                |             |               | Data objects so that they can be updated or deleted after  |
|                |             |               | their initial creation.                                    |
+----------------+-------------+---------------+------------------------------------------------------------+
| isname         | Boolean     | Optional      | One Property may be optionally designated as the name by   |
|                |             |               | supplying the isname keyword with a value of true. Because |
|                |             |               | the index must be unique across all Data objects, the      |
|                |             |               | isname keyword allows for multiple distinct Data objects   |
|                |             |               | to share a common name.                                    |
+----------------+-------------+---------------+------------------------------------------------------------+
| name           | String      | Optional      | Optional friendly name for the Property.                   |
+----------------+-------------+---------------+------------------------------------------------------------+
| description    | String      | Optional      | Optional description for the Property.                     |
+----------------+-------------+---------------+------------------------------------------------------------+

OMF supports setting the format keyword to specify how particular JSON type should be interpreted in the PI System. 



+----------------+-------------+---------------+-------------------+
| Type           | Format      | PI AF Type    | PI DA Type        |
+================+=============+===============+===================+
| array          |             | Array         |                   |
+----------------+-------------+---------------+-------------------+
| boolean        |             | Boolean       |                   |
+----------------+-------------+---------------+-------------------+
| integer        | int64       | Int64         | Float64           |
+----------------+-------------+---------------+-------------------+
| integer        | int32       | Int32         | Int32             |
+----------------+-------------+---------------+-------------------+
| integer        | int16       | Int16         | Int16             |
+----------------+-------------+---------------+-------------------+
| integer        | uint64      | Uint64        | Float64           |
+----------------+-------------+---------------+-------------------+
| integer        | uint32      | Uint32        | Float64           |
+----------------+-------------+---------------+-------------------+
| number         | uint16      | Uint16        | Float64           |
+----------------+-------------+---------------+-------------------+
| number         | float64     | Double        | Float64           |
+----------------+-------------+---------------+-------------------+
|umber           | float32     | Single        | Float32           |
+----------------+-------------+---------------+-------------------+
| number         | float16     | Single        |Float16            |
+----------------+-------------+---------------+-------------------+
| object         | dictionary  |               |                   |
+----------------+-------------+---------------+-------------------+
| string         |             | String        | String            |
+----------------+-------------+---------------+-------------------+
| string         | date-time   | DateTime      | Timestamp         |
+----------------+-------------+---------------+-------------------+

Notes:
  The property type object, and the array of item type object are not supported by the PI System. 
  Properties with ``isindex`` keyword must be of type String. 
  Poperties of a dynamic type with the ``isindex`` keyword must also include a format that has been set to date-time. 
  Properties with the ``isname`` keyword must be of type String. 
  
  Container Message 
    In the PI System, containers are interpreted as instances of data streams. The keywords in the container 
    objects are interpreted as follows: 

+----------------+-------------+---------------+------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                    |
+================+=============+===============+============================================================+
| id             | String      | Required      | Unique identifier of the Container.                        |
+----------------+-------------+---------------+------------------------------------------------------------+
| typeid         | String      | Required      | ID of the dynamic type used by the Container.              |
+----------------+-------------+---------------+------------------------------------------------------------+
| typeversion    | String      | Optional      | Version of the dynamic type used by the Container. If      |
|                |             |               | omitted, version 1.0.0.0 is used.                          |
+----------------+-------------+---------------+------------------------------------------------------------+
| name           | String      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+
| description    | String      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+
| tags           | String      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+
| metadata       | String      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+


The Id value and its association with the typeid value must be unique for all producers (OMF application instances) 
of a given type (which you specify during producer registration). This means that in advanced scenarios, you 
may send container object declarations only once for all registered producers of this OMF application type; 
all of them will reuse the same declarations cached in the Relay, located in the following folder:

::

  C:\ProgramData\OSIsoft\Tau\Relay.ConnectorHost folder. 

Data Message 
------------

In a PI System, a data message serves three different purposes: 

  1: Creating concrete assets from a static type definition, 
  
  2: Linking assets and containers together and forming AF Element/Attribute structure
  
  3: Feeding container data values into PI Data Archive PI points. 


Creating Assets 
  In a PI System, an asset is interpreted as an AF Element. The properties in the asset objects are interpreted as follows: 
  

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

Three types of link objects are supported by the PI System: 

1. Root asset links, which create top level AF elements

2. Parent/child asset links, which create parent/child tree structure between AF elements, 

3. Asset/container links, which attach all container properties as PI point referenced AF attributes to the AF element. 

Root Asset Links 
  Root asset is presented in PI System as a top-level AF element in the AF structure tree, which is a child 
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
  Parent/Child relationship between assets presented in PI System as child AF element attached to a 
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
  Asset/Container relationship between assets and container properties presented in PI System as children 
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
  In PI System, container values are presented as timeseries snapshot values sent to PI points. The properties 
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
  Remember that each property is presented as PI point in PI System. Values for every property of the container, 
  specified in its type definition, must be provided to the container values. All of these values will be sent 
  to PI Data Archive with the same timestamp. If you omit one of the values, you will end up with its default 
  in the Archive. For example, if you omit a value to a number property, PI point will receive a value of zero, 
  which might be undesirable. 
 
HTTP Response and Error Codes 
-----------------------------

The following status codes are returned by PI Connector Relay accepting OMF messages over HTTP. 
 

+---------------------+--------------------------------------------------------------------------------------------------------+
| Status code         | Description                                                                                            |
+=====================+========================================================================================================+
| 204 No Content      | OMF message was successfully processed. Response message does not have any content.                    |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 400 Bad request     | The OMF message was malformed or not understood. The client should not retry sending the message       |
|                     | without modifications.                                                                                 |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 401 Unauthorized    | Authentication failed. Provided Producer Token was not recognized. Your OMF application instance is    |
|                     | not registered with PI Data Collection Manager.                                                        |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 403 Forbidden       | Authentication succeeded, but not authorized. Indicates that either the Producer Token expiration date |
|                     | has been reached (error code 2) or that the Producer Token has been revoked (error code 3).            |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 413 Payload Too     | Payload size exceeds OMF body size limit. Maximum size of either compressed or uncompressed data       |
| large               | should not exceed 192Kb.                                                                               |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 500 Internal Server | The server encountered an unexpected condition. Errors can be found in the Windows Event Viewer on the |
| error               | machine running PI Connector Relay.                                                                    |
+---------------------+--------------------------------------------------------------------------------------------------------+
 
