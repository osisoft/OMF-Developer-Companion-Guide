Before you begin
================

Development of OMF applications generally adheres to the following sequence:

Understand your data
  You must understand how the data you intend to send to PI System will be organized. 
  Your OMF messages must be written so that appropriate AF templates, enumerations, and element trees (with all required attributes 
  and associated PI points) are created in PI AF and PI Data Servers, and updated with your time series data. 

Write OMF messages to create your reference model and start feeding data into PI System 
  After creating your development environment, you should register your development application instance 
  with the DCM/Relay to get appropriate authorization to feed data into the PI System, and write and send OMF messages to 
  the Relay's Ingress HTTP(S) REST endpoint. 
    
Perform cleanup steps
  You should know how to make development changes to your OMF types, instances, and links. You should also 
  know what needs to be manually cleaned up, and how to troubleshoot your code. Note that OMF ingress supports only 
  the creation of data in the PI System and does not support deleting of objects from the PI System. 

The sections below describe each of these points.

Understand your data 
--------------------

Identify your assets 

   *  Physical assets, such as plants, sites, equipment, I/O devices, and so on. Physical assets can have static attributes, 
      which will stay immutable, or can have values whose changes will not be recorded in the data historian; for example, a serial 
      number of an I/O device. 
   *  Logical assets, such as data streams (representing collections of values), which should be sent to Relay ingress as one 
      transaction; that is, all values of a given stream should be sent in one update, and no single value can be 
      skipped. Data for each of these points in the stream is recorded by the data historian with an appropriate timestamp. 

Identify hierarchical relationships between your physical and logical assets 

   *  Physical assets structure - The top-most asset, which might consist of a collection of equipment, each of which has 
      a collection of I/O devices. For example: consider a vehicle top-level asset with an engine child asset, 
      and wheels as children assets. 
   *  Logical assets, which can be attached to any of your physical assets. For example: consider a stream of two 
      values – longitude and latitude, which can be attached to your vehicle asset, and a stream of one
      value: RPM of the engine.


Identify the reference model for your data in AF Server 

   *  A reference model can be thought of as a logical representation of your physical real-world data. The reference model 
      should be understandable from a technical perspective; it is not necessary for your entire organization to understand 
      it. Be aware of the tools provided with your PI System, such as AF Transformer, which allows 
      you to build different representations of your reference model for different business units. For more information about 
      AF Transformer, see this PI Developers Club article: `AF Transformer
      <https://pisquare.osisoft.com/community/developers-club/blog/2018/02/15/welcome-to-our-newest-utility-af-transformer>`_. 


For more information about AF, see the PI System Explorer and PI AF SDK user manuals. 
 
a. Map your physical assets to AF elements. 
b. Link your physical assets together to create appropriate AF tree structure. 
c. Link your logical assets to appropriate AF elements to create dynamic element attributes, 
   which store their values as time-series data in PI Server. 

Write OMF messages to create your AF reference model and feed data into PI Server 
---------------------------------------------------------------------------------------

For more information see OMF 1.0 specification. 
 
a. Create OMF type definitions, which will represent your physical and logical real-world assets. 
   These will be sent to Relay ingress in OMF Type messages. 
   
   i.  Physical assets will be presented by OMF types with "classification": "static".
   ii. Logical assets will be presented by OMF types with "classification": "dynamic". 
   
b. Create OMF containers from dynamic type definitions, which will represent instances of your logical real-world assets, 
   These will be sent to the Relay ingress in OMF Container messages. 
   
c. Create OMF assets from static static type definitions, which will represent instances of your physical real-world assets.
   These will be sent to Relay ingress in OMF Data messages. 
   
d. Create links between: 

   i.   Root node and OMF assets created from static type definitions. 
   ii.  OMF assets created frorm statici type definitions (parent/child). 
   iii. OMF assets created from static OMF type definitions and OMF containers created from dynamic type definitions. 
   
e. Send series values to the containers. These will be sent to Relay Ingress 
   in OMF Data messages, and stored in PI Data Server. 


Development Environment Cleanup 
-------------------------------

In OMF applications, type and container definitions and their representations in PI System are immutable; that is, you cannot 
change the properties of a type after it has been sent to the Relay's ingress endpoint. 
The same is true for instances of these types (assets and containers), and linkage between them. After you 
create instances of these types and link them together by sending container and data messages to 
the Relay's ingress endpoint, you cannot redefine them. 

After the OMF application has been developed and deployed, the only modified data that is expected to be ingested 
is your timeseries data; that is, values that are sent to the container(s) in OMF data messages. 

Only when an error occurs, such as when the Relay loses its cache, type, asset and container, or their linkage information, 
should this metadata be resent to the Relay. To recover from errors, either handle appropriate error codes returned with 
HTTP 400 responses, or send metadata information every time your 
application restarts, making sure that no changes were made to the definitions and instantiations. 
 
As a developer, you will have to deal with changes in the types and structures of your assets in AF and elsewhere. 
To manage such changes, you must understand how to properly clean up your development 
environment, and when cleanup is required. 
 
As a rule of thumb, you should perform a cleanup: 

* When changes were made to a type (basically – any change): 

  * You modified a name or description of the type or one of its properties 
  * You added, removed, renamed a property 
  * You changed a type of a property (i.e. from number to string, etc.) 
  
* When changes were made to a container 

  * You redefined container typeid to another dynamic type 
  
* When changes were made to a data (except that of data values that you send to containerids): 

  * You redefined asset typeid to another static type 
  * You changed anything that you previously sent in the "__LINK" object 


1. Relay's temporary cache location. 
   Stop the Relay process. By default, if not chosen during Relay setup, temporary data will be stored in
   \%ProgramData\%\\OSIsoft\\Tau\\Relay.ConnectorHost. Delete this folder. 
   Deleting this folder will remove all cache for all producers. 
   
2. PI Server AF Database that you use to create your AF asset structure.
   In PI System Explorer, open Library, expand Templates/Element Templates. Delete all templates with 
   names starting with "OMF". 
   In PI System Explorer, open Elements, expand Elements root node. Delete all elements and their 
   children elements that has names of you OMF application instances registrations. 
   In PI System Explorer, check-in all your deletion changes. 
   
3. PI Data Archive PI points that were created once you sent container data values. 
   In PI System Management Tool, open Points/Point Builder. Search for PI tags that has names starting with 
   your OMF application instance registration. Delete all of them. 
 
  * Operation #1 is required always. 
  * Operation #2 is required if your application defines and links static types. 
  * Operation #3 is required if you previously sent data values to containers. 
