Type Message
============

In an on-premises PI System, types are interpreted either as asset types or container types depending on the type classification.
The keywords in the type definition are interpreted as follows:

+--------------------+-------------+---------------+------------------------------------------------------------+
| Property           | Type        | Optionality   | Details                                                    |
+====================+=============+===============+============================================================+
| ``Id``             | String      | Required      | Unique identifier of the type. See below for details.      |
+--------------------+-------------+---------------+------------------------------------------------------------+
| ``classification`` | String      | Required      | Specifies whether the type is ``static`` or ``dynamic``.   |
|                    |             |               | See below sections for details.                            |
+--------------------+-------------+---------------+------------------------------------------------------------+
| ``type``           | String      | Required      | Inherited from JSON Schema. Must be set to object.         |
+--------------------+-------------+---------------+------------------------------------------------------------+
| ``properties``     | Object      | Required      | Key-value pairs representing properties of the type.       |
+--------------------+-------------+---------------+------------------------------------------------------------+
| ``Name``           | String      | Optional      | Friendly name of the type.                                 |
+--------------------+-------------+---------------+------------------------------------------------------------+
| ``description``    | String      | Optional      | Description of the type.                                   |
+--------------------+-------------+---------------+------------------------------------------------------------+
| ``version``        | String      | Optional      | Version of the type. If omitted version 1.0.0.0 is assumed.|
+--------------------+-------------+---------------+------------------------------------------------------------+
| ``tags``           | Array       | Optional      | Not supported.                                             |
+--------------------+-------------+---------------+------------------------------------------------------------+
| ``metadata``       | Object      | Optional      | Not supported.                                             |
+--------------------+-------------+---------------+------------------------------------------------------------+


The ``Id`` value must be unique for all OMF application of a given OMF application type, which you specify
during application registration. This means that in advanced scenarios, you may send type definitions only once
for all registered applications of the specified OMF application type; all of the applications will reuse the
same definition that is cached in the PI Connector Relay, in the following folder:

::

  %ProgramData%\OSIsoft\Tau\Relay.ConnectorHost

The default version for all OMF types is 1.0.0.0. If the default version is acceptable, it is not necessary to specify
the version in the type definition or in the subsequent container, asset, or link definitions, the default will be assumed.

If you choose to provide version information for the type, you must provide the same version with all container, asset,
and link definitions.


Types with classification Static
--------------------------------

A type with classification ``static`` represents metadata describing an asset type as Asset Framework Element Template
in Asset Framework Server.

The ``Id`` property of the static type is used to create and link concrete assets, to create Asset Framework Elements and
their structure.

If the optional name property is omitted from the definition, the ``Id`` value is used to name the Asset Framework Element
Template; otherwise the name value is used. The template name is assembled from the following parts:

::

  OMF.{name of OMF application that you registered with DCM} Connector.{id|name}

Each property of the type, except those that are marked with keywords ``isindex`` or ``isname``, represent
Configuration Asset Framework Attributes (not referencing any data) in the Asset Framework Template.

Types with classification Dynamic
---------------------------------

A type with classification ``dynamic`` represents a data stream type (that is, a set of observed or calculated values)
presented in Data Archive as PI points.
Each property of this type, except the property with the keyword ``isindex``, represents a PI point referenced
Asset Framework Attribute in the Asset Framework Template, after you link this type to a static type.

Property Types and Formats
--------------------------

The following keywords are used to define a Type Property:


+----------------+-------------+---------------+-------------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                           |
+================+=============+===============+===================================================================+
| ``type``       | String      | Required      | Required type of the Type Property which must match one of        |
|                |             |               | those listed in the table below.                                  |
+----------------+-------------+---------------+-------------------------------------------------------------------+
| ``format``     | String      | Optional      | Optional format of the Type Property type that, if                |
|                |             |               | specified, must be from the table below.                          |
+----------------+-------------+---------------+-------------------------------------------------------------------+
| ``isindex``    | Boolean     | Required      | One Property must be designated as the index by supplying         |
|                |             |               | the ``isindex`` keyword with a value of ``true``. The designated  |
|                |             |               | ``isindex`` property is used to uniquely identify discrete        |
|                |             |               | Data objects so that they can be updated or deleted after         |
|                |             |               | their initial creation.                                           |
+----------------+-------------+---------------+-------------------------------------------------------------------+
| ``isname``     | Boolean     | Optional      | One Property may be optionally designated as the name by          |
|                |             |               | supplying the ``isname`` keyword with a value of ``true``.        |
|                |             |               | Because the index must be unique across all Data objects, the     |
|                |             |               | ``isname`` keyword allows for multiple distinct Data objects      |
|                |             |               | to share a common name.                                           |
+----------------+-------------+---------------+-------------------------------------------------------------------+
| ``enum``       | Array       | Optional      | Optional enumeration declaration. Contains an array of values     |
|                |             |               | for the enumeration set. The type of value is specified           |
|                |             |               | by the keyword ``type,`` and can be set to ``integer``,           |
|                |             |               | ``number``, or ``string``.                                        |
+----------------+-------------+---------------+-------------------------------------------------------------------+
| ``name``       | String      | Optional      | Optional friendly name for the Property.                          |
+----------------+-------------+---------------+-------------------------------------------------------------------+
| ``description``| String      | Optional      | Optional description for the Property.                            |
+----------------+-------------+---------------+-------------------------------------------------------------------+

Note: PI Data Archive does not support ``Boolean`` data types. To overcome this limitation, you can use enumeration
sets, specifying a set of two values. For example:

``“type”: “string”, “enum”: [“False”, “True”].``

The PI point data type for enumeration sets is ``digital``.

OMF supports setting the format keyword to specify how a particular JSON type should be interpreted in an on-premises PI System.


+----------------+-------------+---------------+-------------------+
| Type           | Format      | Asset         | PI DA Type        |
|                |             | Framework     |                   |
|                |             | Type          |                   |
+================+=============+===============+===================+
| array          |             | Array         |                   |
+----------------+-------------+---------------+-------------------+
| boolean        |             | Boolean       |                   |
+----------------+-------------+---------------+-------------------+
| integer        | int64       | Int64         | Float64           |
+----------------+-------------+---------------+-------------------+
| integer        | int32       | Int32         | Int32             |
+----------------+-------------+---------------+-------------------+
| integer        | int16       | Int16         | Int32             |
+----------------+-------------+---------------+-------------------+
| integer        | uint64      | UInt64        | Float64           |
+----------------+-------------+---------------+-------------------+
| integer        | uint32      | UInt32        | Float64           |
+----------------+-------------+---------------+-------------------+
| number         | uint16      | Int16         | Int32             |
+----------------+-------------+---------------+-------------------+
| number         | float64     | Double        | Float64           |
+----------------+-------------+---------------+-------------------+
| number         | float32     | Single        | Float64           |
+----------------+-------------+---------------+-------------------+
| number         | float16     | Single        | Float64           |
+----------------+-------------+---------------+-------------------+
| object         | dictionary  |               |                   |
+----------------+-------------+---------------+-------------------+
| string         |             | String        | String            |
+----------------+-------------+---------------+-------------------+
| string         | date-time   | DateTime      | Timestamp         |
+----------------+-------------+---------------+-------------------+

Notes:
  The on-premises PI System does not support properties of type ``object``, and arrays of items of type ``object``.
  Properties with ``isindex`` keyword must be of type String.
  Properties of a dynamic type with the ``isindex`` keyword must also include ``format`` keyword that is set to the value ``date-time``.
  Properties with the ``isname`` keyword must be of type String. 
