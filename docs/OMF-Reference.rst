
OMF restrictions, limits, and error codes
=========================================

The OMF specification (located `here <http://omf-docs.osisoft.com/en/v1.0/>`_) is generic in that it does not specify a particular back-end system.


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
Server back-end which are presented as concatenations of each other). For example, if an Asset Framework Element Template name
consists of the following parts:

OMF.{*OMF application type*}
Connector.{*OMF type definition identifier* | *OMF type definition name*}.

The maximum length of template names should be less than 260 characters (see *PI AF SDK Reference*).
Therefore, you must calculate character lengths for both OMF application type, and OMF type definition ID and name.

As a rule of thumb, keep the sizes of all names and identifies to 50 – 60 characters at most (although even these sizes
might result in your application being difficult to read and understand.)

Note: PI Data Archive does not support Unicode. Therefore, all data sent to PI Data Archive must
be formatted as JSON using UTF-8 character encoding. For example, in the tag shown below, both ``omf application name`` and ``container id`` must use UTF-8 character encoding:

``{omf application name}.{container id}``

Note: PI Data Archive does not support ``Boolean`` data types. To overcome this limitation, you can use enumeration
sets, specifying a set of two values. For example:

``“type”: “string”, “enum”: [“False”, “True”].``

The PI point data type for enumeration sets is ``digital``.


Message Headers
---------------

A description of each of the headers can be found in the *OMF specification*. When sending messages to the on-premises 
PI System, the value of the ``producertoken`` header must be set to a Producer Token that is obtained from the PI 
Data Collection Manager.

The Producer Token is used to authenticate the sender and to authorize the sender to feed data to PI Connector
Relay ingress endpoint.


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
| 401 Unauthorized    | Authentication failed. Provided Producer Token was not recognized. Your OMF application is             |
|                     | not registered with PI Data Collection Manager.                                                        |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 403 Forbidden       | Authentication succeeded, but not authorized. Indicates either that the Producer Token expiration date |
|                     | has been reached, or the Producer Token has been revoked. See Error Codes (below) for details.         |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 413 Payload Too     | Payload size exceeds OMF body size limit. Maximum size of either compressed or uncompressed data       |
| large               | should not exceed 192Kb.                                                                               |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 500 Internal Server | The server encountered an unexpected condition. Errors can be found in the Windows Event Viewer on the |
| error               | machine running PI Connector Relay.                                                                    |
+---------------------+--------------------------------------------------------------------------------------------------------+
| 503 Service         | The server is currently unavailable, retry later. Indicates either that PI Connector Relay service is  |
| Unavailable         | not running, or your OMF application uses wrong Ingress URL.                                           |
+---------------------+--------------------------------------------------------------------------------------------------------+

**Error codes**

+-------------+--------------------------------+-----------------------------------------------------------------------+
| Error code  | HTTP Status Code               | Description                                                           |
+=============+================================+=======================================================================+
| 1           | 401                            | Access token invalid.                                                 |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 2           | 403                            | Access token expired.                                                 |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 3           | 403                            | Access token revoked.                                                 |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 4           | 400                            | Invalid message body.                                                 |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 5           | 413                            | Payload size exceeds OMF body size limit.                             |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 6           | 400                            | Error decompressing GZIP compressed payload.                          |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 7           | 400                            | Invalid OMF message headers.                                          |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 8           | 400                            | Invalid JSON format.                                                  |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 9           | 400                            | Invalid OMF message syntax.                                           |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 10          | 400                            | Type redefinition.                                                    |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 11          | 400                            | Type is not defined.                                                  |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 12          | 400                            | Container redefinition.                                               |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 13          | 400                            | Container is not defined.                                             |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 14          | 400                            | Invalid container type classification.                                |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 15          | 400                            | Invalid root link.                                                    |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 16          | 400                            | Invalid source link.                                                  |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 17          | 400                            | Invalid target link.                                                  |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 18          | 400                            | Invalid value type for the property.                                  |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 19          | 400                            | Property does not exist in the type definition.                       |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 20          | 400                            | Value is not part of enumeration.                                     |
+-------------+--------------------------------+-----------------------------------------------------------------------+
| 21          | 400                            | Not implemented.                                                      |
+-------------+--------------------------------+-----------------------------------------------------------------------+
