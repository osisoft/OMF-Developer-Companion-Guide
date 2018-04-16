.. _OMF_Quick_Start_topic:

OMF Quick Start 
===============

This section provides a brief introduction to the steps necessary to begin the development of an OMF application to send
data to PI AF Server and PI Data Archive. Using the OMF 1.0 
specification and the steps in this section, you can create a minimal data ingress OMF application. More 
advanced example applications can be found in the `OMF samples 
<https://github.com/osisoft/OMF-Samples>`_.


Development Environment 
-----------------------

Before you begin, the following products should be installed and configured:

* PI Data collection Manager (DCM)

  As a developer, you must have administrator access to the PI Data Collection Manager (DCM). You must have sufficient 
  access to create a PI Server, PI Connector Relay, and OMF application nodes. You must also be able to establish 
  connections between them, and retrieve the necessary registration 
  information, which you will use in your OMF application for authentication and authorization. For 
  more information, see *PI Data Collection Manager* user manual. 


* PI Connector Relay

  You should install and configure your own development instance of the PI Connector Relay. During the
  development process, it will be necessary to stop and re-start the PI Connector Relay process, manually 
  delete cache files, and perform other actions which may prevent other data sources from sending data to 
  PI System. For more information, see *PI Connector Relay* user manual. 

* PI AF Server and PI Data Archive

  You must have administrative access to the PI AF Server and PI Data Archive. One way to create such a development 
  environment is by creating a PI Developers Club subscription at `PI Square <https://pisquare.osisoft.com/>`_
  
  While developing 
  your OMF application, it will be necessary to delete intermediate AF templates, elements, and PI points. For 
  more information, see the *PI System Explorer* user manual and the *PI System Management Tools* user manual. 
  
To speed the development process, it is recommended that all of the above products be 
installed on your computer and on the computers in the same Windows domain. Also, ensure you have sufficient access rights. 
Note that this topic demonstrates the basic OMF application development process, and not the administrative aspects 
of configuring and securing the entire solution you build using OSIsoft PI System. 


Programming language and run platform
-------------------------------------

The OMF 1.0 specification is written to be language and platform-agnostic. To start sending data to an OSIsoft PI
System, all that you need is an HTTP client and JSON libraries. For your convenience, OSIsoft provides several 
code samples written in Python 3.X, NodeJS, and Microsoft C#. Note, that you may use the samples only as 
reference material; the samples are not intended to be used in production systems.

For more information about licensing, see the sample code file headers. 

Creating a minimal OMF application
----------------------------------

This section illustrates a very simple OMF application that feeds data into the PI Data Archive without 
sending any data to the AF server. 

Before you begin, you must register your application with the DCM, 
obtain a Producer Token and Relay Ingress URL. See the *DCM* user manual for more information. 

Step 1 – OMF message headers
----------------------------

As with any OMF application, it is necessary to send three OMF Messages to the Relay ingress endpoint. All three messages 
reuse the same set of HTTP request headers, changing the ``messagetype`` value in the header to match the message type. 
The first message sent must be a ``type`` message, which defines the type of the data stream. The second must be a 
``container`` message, which creates the container of the previously defined type of the data stream. The third, and 
all consecutive messages, must be ``data`` messages, which send time series data to the PI System using the container 
that was created.

Currently, only the create action is supported by the OMF specification. Therefore, you cannot use OMF messages to 
update or delete OMF definitions sent to the PI Connector Relay. You must use other PI tools to make changes or 
make the changes manually.


The following is the list of required headers and values: 

``producertoken``
  Set the value of this header to the Producer Token that you retrieved from the DCM when your 
  application instance was registered. 
``messagetype``
  This header may contain one of the three values: ``type``, ``container`` or ``data``. The first message that is 
  sent to the ingress endpoint should always be of type ``type``.  
``messageformat``
  Set the value of this header to ``json``. The header is required, but currently accepts only this value. 
``omfversion``
  Set the value to ``1.0``

Step 2 – define OMF type and send it in OMF Type message
--------------------------------------------------------

All OMF message content are formatted as a JSON array of objects. You can optionally compress messages using 
GZIP compression. For clarity, this example does not use compression. 

For this application, to define a data stream class, you must define an OMF type with ``classification`` set 
to ``dynamic``. 

**Headers**

::

	producertoken = b7CNvN36cq
	omfversion = 1.0
	messagetype = type
	action = create
	messageformat = json

**Body**

::

  [{ 
    "id": "DataType", 
    "classification": "dynamic", 
    "type": "object", 
    "properties": { 
        "timestamp": { 
          "type": "string", 
          "format": "date-time", 
          "isindex": true 
        }, 
        "value": { 
           "type": "number" 
        } 
      } 
  }] 



Step 3 – create a container and send it in OMF Container message 
----------------------------------------------------------------

The next step is to create a container of the specified dynamic type. Note that containers should be 
created only for dynamic types. 

**Headers**

::

	producertoken = b7CNvN36cq
	omfversion = 1.0
	messagetype = container
	action = create
	messageformat = json

**Body**

::

  [{ 
    "id": "container1", 
    "typeid": "DataType" 
  }] 




Step 4 – provide data values to the container and send them in OMF Data message 
-------------------------------------------------------------------------------

Finally, you assemble data values for the created container and send it to PI Server. 

**Headers**

::

	producertoken = b7CNvN36cq
	omfversion = 1.0
	messagetype = data
	action = create
	messageformat = json

**Body**

::

  [{ 
    "containerid": "container1", 
    "values": [{ 
      "timestamp": "2018-04-22T22:24:23.430Z", 
      "value": 3.14 
      }]
  }] 
 
Note, that the ``values`` property is a JSON array, which can hold multiple values (with different timestamps) 
to be sent to the ingress endpoint in one message. 

Step 5 – validate your data 
---------------------------

You should validate that the data was created successfully in PI Server, and that your data was stored successfully
in PI Data Archive. This simple example creates one PI point and stores one value in it. 

To validate, open PI System Management Tools, navigate to Points/Point Builder, and search for your PI point. 
The name of the PI point is as follows:

``{name of OMF application that you registered with DCM}.container1`` 

Hover the mouse over the name and verify that it contains the PI point value and timestamp. For more information, 
see *PI System Management Tools user manual*. 

Step 6 – cleanup
----------------

It is highly recommended that you clean up development environment when finished coding. 
Perform the following two actions for this simple example:

1. Stop the relay process by navigating to the ``\%ProgramData\%\\OSIsoft\\Tau\\`` folder, and deleting the 
   ``Relay.ConnectorHost`` folder. 
2. Delete your PI point from the PI Data Archive. You can use Point Builder to perform this task. 


