OMF Developer Companion Guide
=============================

The information in these topics describes how to create an application that uses the OSIsoft Message Format (OMF) 
to send data to the OSIsoft PI Data Archive and PI Asset Framework Server using the PI Data Collection Manager (DCM). 

The OSIsoft Message Format (OMF) defines a set of message headers and message bodies that are used to generate 
compliant messages for inputting data into either an on-premises PI Server, OSIsoft Cloud Services, or another 
compliant backend system.

OMF can be used to develop data acquisition applications on platforms and in languages for which there are no 
supported OSIsoft libraries.

The OMF specification does not define or depend on any particular binary message protocol (HTTP, AMQP, Kafka, 
and so on.) Rather, it is based on an abstract message type, where a message consists of a set of key / value pairs, 
called the header, and a binary payload, called the body. OMF messages can thus be constructed using any message 
protocol that defines headers and bodies. For up-to-date information about the specific binary protocols currently 
supported by OSIsoft systems, consult OSIsoft Cloud Services (OCS) and PI Connector Relay documentation.

The OMF specification (located `here <http://omf-docs.osisoft.com/en/v1.0/>`_) is generic in that it does not specify 
a particular back-end system. The information in these topics represent a companion to the OMF specification that 
describes how OMF is interpreted by the PI Server back end. 

This is a test


.. toctree::

   OMF-Dev-Intro
   OMF_Quick_Start

.. toctree::

   OMF_Msg_Types
   OMF-Reference

.. toctree::

   Other_considerations
   

   
   
   
   
