Introduction
============

Development of OMF applications generally adheres to the following sequence:

`Understand your data`_
  You must understand how the data you intend to send to PI System will be organized. 
  Your OMF messages must be written so that appropriate AF templates, enumerations, and element trees (with all required attributes 
  and associated PI points) are created in PI AF and PI Data Servers, and updated with your time series data. 

`Write the OMF application`_ 
  Write OMF messages to create your reference model and start feeding data into PI System 
  After creating your development environment, you should register your development application 
  with the  PI Data Collection Manager to obtain appropriate authorization to feed data into the PI System, 
  and write and send OMF messages to the Relay Ingress HTTPS REST endpoint. 
    
`Clean up the development environment`_
  After making development changes to your OMF types, instances, and links, you should clean up your environment
  by deleting tests and older versions. There are also some items that require manual cleanup. Note that OMF 
  ingress supports only 
  the creation of data in the PI System and does not support deleting of objects from the PI System. Also, it is a 
  good practice to create a seperate test or quality assurance system and database before testing or
  attempting to write code on a production system. Having a test or QA system makes it easier to remove old 
  code and data and to recover from errors. 

The sections below describe each of these points.

Understand your data 
--------------------

* Identify your assets 
   *  Physical assets, such as plants, sites, equipment, I/O devices, and so on. Physical assets can have static attributes, 
      which will stay immutable, or can have values whose changes will not be recorded in the data historian; for example, a serial 
      number of an I/O device. 
      
   *  Logical assets, such as data streams (representing collections of values), which should be sent to Relay ingress as one 
      transaction; that is, all values of a given stream should be sent in one update, and no single value can be 
      skipped. Data for each of these points in the stream is recorded by the data historian with an appropriate timestamp. 

* Identify hierarchical relationships between your physical and logical assets 
   *  Physical assets structure - The top-most asset, which might consist of a collection of equipment, each of which has 
      a collection of I/O devices. For example: consider a vehicle top-level asset with an engine child asset, 
      and wheels as children assets. 
   *  Logical assets, which can be attached to any of your physical assets. For example: consider a stream of two 
      values – longitude and latitude, which can be attached to your vehicle asset, and a stream of one
      value: RPM of the engine.


* Identify the reference model for your data in AF Server 
   *  A reference model can be thought of as a logical representation of your physical real-world data. The reference model 
      should be understandable from a technical perspective; it is not necessary for your entire organization to understand 
      it. Be aware of the tools provided with your PI System, such as AF Transformer, which allows 
      you to build different representations of your reference model for different business units. For more information about 
      AF Transformer, see this PI Developers Club article: `AF Transformer
      <https://pisquare.osisoft.com/community/developers-club/blog/2018/02/15/welcome-to-our-newest-utility-af-transformer>`_. 


For more information about AF, see the 
`PI Server documentation: <https://livelibrary.osisoft.com/LiveLibrary/web/ui.xql?action=html&resource=publist_home.html&pub_category=PI-Server>`_. 
 
a. Map your physical assets to AF elements. 
b. Link your physical assets together to create appropriate AF tree structure. 
c. Link your logical assets to appropriate AF elements to create dynamic element attributes, 
   which store their values as time-series data in PI Server. 

Write the OMF application
-------------------------

 
a. Create OMF type definitions, which will represent your physical and logical real-world assets. 
   These type definitions will be sent to Relay ingress in OMF Type messages. 
   
   i.  Physical assets will be presented by OMF types with ``classification: static``.
   ii. Logical assets will be presented by OMF types with ``classification: dynamic``. 
   
b. Create OMF containers from dynamic type definitions, which will represent instances of your logical real-world assets, 
   These will be sent to the Relay ingress in OMF Container messages. 
   
c. Create OMF assets from static type definitions, which will represent instances of your physical real-world assets.
   These will be sent to Relay ingress in OMF Data messages. 
   
d. Create links between: 

   i.   Root node and OMF assets created from static type definitions. 
   ii.  OMF assets created frorm statici type definitions (parent/child). 
   iii. OMF assets created from static OMF type definitions and OMF containers created from dynamic type definitions. 
   
e. Send time-series values to the containers. These will be sent to Relay Ingress 
   in OMF Data messages and stored in PI Data Server. 

For more information see OMF 1.0 specification. 


Clean up the development environment 
------------------------------------

In OMF applications, type and container definitions and their representations in PI System are immutable; that is, you cannot 
change the properties of a type after it has been sent to the Relay's ingress endpoint. 
The same is true for instances of these types (assets and containers), and linkage between them. After you 
create instances of these types and link them together by sending container and data messages to 
the Relay's ingress endpoint, you cannot redefine them. 

After the OMF application has been developed and deployed, the only modified data that is expected to be ingested 
is your time-series data; that is, values that are sent to the container(s) in OMF data messages. 

Only when an error occurs, such as when the Relay loses its cache, type, asset and container, or their linkage information, 
should this metadata be re-sent to the Relay. To recover from errors, either handle appropriate error codes returned with 
HTTP 400 responses, or send metadata information every time your 
application restarts, making sure that no changes were made to the definitions and instantiations. 
 
As a developer, you will have to deal with changes in the types and structures of your assets in AF and elsewhere. 
To manage such changes, you must understand how to properly clean up your development 
environment, and when cleanup is required. 
 
As a rule of thumb, you should perform a cleanup: 

* When changes were made to a type (basically – any change): 

  * You modified a name or description of the type or one of its properties 
  * You added, removed, or renamed a property 
  * You changed a type of a property (that is, from number to string, and so on) 
  
* When changes were made to a container 

  * You redefined container typeid to another dynamic type definition 
  
* When changes were made to a data (except that of data values that you send to containerids): 

  * You redefined asset typeid to another static type definition
  * You changed anything that you previously sent in the "__LINK" object 

**What to clean up**

1. Relay's temporary cache location. 
   Stop the Relay process. By default, if not chosen during Relay setup, temporary data will be stored in
   ``\%ProgramData\%\\OSIsoft\\Tau\\Relay.ConnectorHost``. Delete this folder. 
   Deleting this folder removes all cache entires for all producers. 
   
2. PI Server AF Database that you use to create your AF asset structure. Using PI System Explorer, perform the following:

   a.  Open Library, expand Templates/Element Templates. Delete all templates with names starting with "OMF". 
   b.  Open Library, expand Templates/Enumeration Sets. Delete all enumerations with names starting with "OMF". 
   c.  Open Elements, expand Elements root node. Delete all elements and their 
       children elements that have names of your OMF application registrations. 
   d.  Check in your changes. 
   
3. PI Data Archive PI points that were created after the container data values were sent. 
   In the PI System Management Tool, open Points/Point Builder. Search for and delete all PI tags that have names starting with 
   your OMF application registration name. 
 
 **When to clean up**
 
* Operation #1 is always required. 
* Operation #2 is required if your application defines assets and links from OMF static type definitions. 
* Operation #3 is required if you had previously sent timeseeries data values to containers. 
