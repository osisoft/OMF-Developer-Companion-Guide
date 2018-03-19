
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








