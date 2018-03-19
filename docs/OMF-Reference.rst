
OMF reference information
=========================

The OMF specification (located here) is generic in that it does not specify a particular back-end system. 
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







