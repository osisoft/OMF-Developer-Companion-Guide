OMF Quick Start 
===============

This section provides a brief introduction to the steps necessary to begin the development of an OMF application to send
data to a PI system. Using the OMF 1.0 
specification and the steps in this section, you can create a minimal data ingress OMF application. More 
advanced applications can be found in the OMF samples. 

To speed the development process, it is recommended that all of the following products be 
installed on your computer and the compters in the same Windows Domain. Also ensure you have sufficient access rights. 

Note that this topic demonstrates the basic OMF application development process, and not the administrative aspects 
of configuring and securing the entire solution you build using OSIsoft PI System. 

Development Environment 
-----------------------

Before you begin, the following products should be installed and configured:

* PI Data collection Manager (DCM)

  As a developer, you must have administrative access to the DCM. You must have sufficient access to create a PI Server, 
  PI Connector Relay, OMF application nodes. You must also be able to establish connections between them, and retrieve the 
  necessary registration 
  information, which you will use in your OMF application for the authentication and authorization process. For 
  more information, see *PI Data Collection Manager* user manual. 
  
* PI Connector Relay

  You should install and configure your own development instance of the PI Connector Relay. During the
  development process, you will need to stop and re-start the PI Connector Relay process, manually delete cache files, and 
  perform other actions which may prevent other data sources from sending data to the PI System. For more 
  information, see *PI Connector Relay* user manual. 

* PI System 

  You must have administrative access to the PI AF Server and PI Data Archive. While developing 
  your OMF application, you will need to delete intermediate AF templates, elements, and PI points. For 
  more information, see the *PI System Explorer* user manual and the *PI System Management Tools* user manual. 

Programming Language and Running Platform
-----------------------------------------

The OMF 1.0 specification is written to be language and platform-agnostic. To start sending data to an OSIsoft PI
system, all that you need is an HTTP client and JSON libraries. For your convenience, OSIsoft provides several 
code samples  written in Python 3.X, NodeJS and Microsoft C#. Note, that you may use the samples only as 
reference material; the samples are not intended to be used in production systems.

For more information about licensing, see the sample code file headers. 

Creating a minimal OMF application
----------------------------------

This section illustrates a very simple OMF application. Using PI Asset Framework and PI AF Server, the application 
feeds data into the PI Data Archive without sending any meta-data. 
 
Before you begin, you must register your application with the PI Data Collection Manager (DCM), 
obtain a Producer Token and Relay Ingress URL. See *DCM* user manual for more information. 

Step 1 – OMF message headers
----------------------------

As with any OMF application, you need to send three OMF Messages to the Relay's ingress endpoint. All three 
will reuse the same set of HTTP request headers, changing a value of one header according to the message type. 

The following is the list of required headers, and their values: 

``producertoken``
  Set the value of this header to the Producer Token that you retrieved from the DCM while registering your 
  application instance. 
``messagetype``
  This header may contain one of the three values: "type", "container" or "data". The first message that is 
  sent to the ingress endpoint should always be of type ``type``. So, set the header to value "type". 
``messageformat``
  Set the value of this header to ``json``. The header is required, but currently accepts only this value. 
``omfversion``
  Set the value to ``1.0``

Step 2 – define OMF type and send it in OMF Type message
--------------------------------------------------------

All OMF message content should be presented as JSON array of objects. Optionally, you can compress it using 
GZIP compression. For clarity, we do not using compression in this walk-through. 

For this minimal application, you need to define an OMF type with "classification" set to "dynamic", 
to define a data stream class. Note, that all OMF identifies are case sensitive.  

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


OMF message with the following contents, and "messagetype" header set to "type" should be send first, before 
sending any other messages. 

Step 3 – create a container and send it in OMF Container message 
----------------------------------------------------------------

Next, you need to create a container of the above specified dynamic type. Note, containers should be 
created only for dynamic types. 

::

  [{ 
    "id": "container1", 
    "typeid": "DataType" 
  }] 


Set "messagetype" header to the value "container". This message should be sent after type message specifying "typeid" property. 

Step 4 – provide data values to the container and send them in OMF Data message 
-------------------------------------------------------------------------------

Finally, you need to assemble data values for the created container, and send it to the PI System. 

::

  [{ 
    "containerid": "container1", 
    "values": [{ 
      "timestamp": "2018-04-22T22:24:23.430Z", 
      "value": 3.14 
  }] 
 
Note, that "values" property is a JSON array, which can hold multiple values (with different timestamps) 
to be sent to the ingress endpoint in one message. 

Step 5 – validate your data 
---------------------------

Before you can call your development process "done", you need to validate whether everything was created in 
PI System, and your data successfully arrived into PI Data Archive. This simple example creates one PI point 
and stores one value in it. 

To validate, open PI System Management Tools, navigate to Points/Point Builder, and search for your PI point. 
Its name should be as follows:

``name of OMF application that you registered with DCM.container1`` 

Hover the mouse over the name and validate the PI point value and timestamp. For more information, see PI System 
*Management Tools user manual*. 

Step 6 – cleanup
----------------

It is highly recommended that after you done with the development, you clean up development environment. 
For this simple example, you need to perform two actions: 

1. Stop Relay process, navigate to C:\ProgramData\OSIsoft\Tau\ folder, and delete the "Relay.ConnectorHost" sub-folder. 

2. Delete your PI point from the PI Data Archive. You may use Point Builder to perform this action. 

