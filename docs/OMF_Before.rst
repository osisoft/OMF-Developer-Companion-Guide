Before you begin
================

Development of OMF applications generally adheres to the following sequence:

Understand your data
  You must understand how the data you intend to send to PI Server will be organized in the PI Server back-end. 
  Your OMF messages must be written so that appropriate AF templates, element trees (with all required attributes 
  and associated PI points) are created in PI AF and PI Data Historian, and updated with your timeseries data. 

Write OMF messages to create your reference model and start feeding data into PI Server 
  After creating your development environment, you should register your development application instance 
  with the DCM/Relay to get appropriate authorization to feed data into PI Server, and write and send OMF messages to 
  the Relay's Ingress HTTP(S) REST endpoint. 
    
Perform cleanup steps
  You should know how to make development changes to your OMF types, instances, and links. You should also 
  know what needs to be manually cleaned up, and how to troubleshoot your code. 

The sections below describe each of these points.

Understand your data 
--------------------

Identify your assets. 

   *  Physical – representing plants, sites, equipment, I/O devices, etc. These assets can have static attributes, 
      which will stay immutable, or which value changes will not be recorded in the data historian. Example: serial 
      number of I/O device. 
   *  Logical – data streams, representing collections of values, which should be sent to Relay ingress as one 
      transaction. I.e. all values of a given stream should be sent in one update, no one separate value can be 
      skipped. Data for each of these points in the stream will be recorded into the data historian with an appropriate timestamp. 

Identify hierarchical relationships between your assets and data streams. 

   *  Physical assets structure - top-most asset, which has a collection of equipment, each of which has 
      collection of I/O devices. For example: vehicle top-level asset with attached child asset engine, 
      and attached children assets wheels. 
   *  Data stream assets, which can be attached to any of your physical assets. For example: stream of two 
      values – longitude and latitude, which can be attached to your vehicle asset, and stream of two 
      values – RPM and pressure of your vehicle's engine asset. 


Identify the reference model for your PI Server 

A *reference model* deals with the physical or real-world aspects of your system. The reference model
should be understandable from a technical perspective. It is not necessary for your entire organization to 
understand the reference model. If you are adopting or adapting 
OSIsoft PI Server, know the tools that we are providing. One of them is AF Schema Mapper 
(or what's its name???), which allows you to build different representations of your reference model for 
different business units of your company. 


Note: why do we call it "reference model". This model should concentrate on a physical/real-world aspects 
of your system. It should model you system from the deep technical, engineering perspective, which is 
"understandable" by technical people. You do not want to pursue the goal of making this AF model/structure 
understandable for each and every person in your company, including engineering, accounting, management, 
executive, etc. - this is a recipe for your project failure! Keep it simple! If you are adopted/adapting 
OSIsoft PI Server, know the tools that we are providing. One of them is AF Schema Mapper 
(or what's its name???), which allows you to build different representations of your reference model for 
different business units of your company. 
 
For more information about AF, see PI AF Explorer, PI AF SDK user manuals. 
 
a. Map your data's physical assets to AF elements. 
b. Link your data's physical assets together to create appropriate AF tree structure. 
c. Link your stream assets to appropriate AF elements to create dynamic element attributes, 
   which store their values in PI Data Historian. 

Write OMF messages to create your reference model and start feeding data into PI Server 
---------------------------------------------------------------------------------------

For more information see OMF 1.0 specification. 
 
a. Create OMF type definitions, which will represent your physical and data stream assets. 
   These will be sent to Relay ingress in OMF Type messages. 
   
   i.  Physical assets will be presented by OMF types with "classification": "static".
   ii. Data Stream assets will be presented by OMF types with "classification": "dynamic". 
   
b. Create OMF containers, which will represent instances of your data stream assets, which you will 
   later link to instances of your physical assets. These will be sent to Relay ingress in OMF Container messages. 
   
c. Create OMF physical (static) type instances. Later you will link them to each other and data stream 
   (dynamic) type instances (containers). These will be sent to Relay ingress in OMF Data messages. 
d. Create links between: 

   i.  Root node and static (physical) type instances. 
   ii. Static to static (parent/child) type instances. 
   iii. Static to dynamic (data stream) (parent/end leaf child) type instances. 
   
e. Update data stream (container) instances with time series values. These will be sent to Relay Ingress 
   in OMF Data messages, and update AF attributes with PI point references. 


Development Environment Cleanup 
-------------------------------

In OMF applications, type definitions and their representations in PI Server are immutable; that is, you cannot 
change the properties of a type after is has been sent to the Relay's ingress endpoint. 
The same is true for instances of these types (assets and containers), and linkage between them. Once you 
create concrete instances of these types and link them together by sending container and data messages to 
the Relay's ingress endpoint, you cannot redefine them. 

After the OMF application has been developed and deployed, the only changed data that are expected by ingress 
is the values of your timeseries data – values sent to the container(s) in data messages. 

Only in error situations, when Relay loses its cache, type, asset and container, and their linkage information 
should be resent to the Relay. To recover from this, you can send this meta information every time your 
application restarts, having that NO changes were made to these definitions and instantiations. 
 
As a developer, you will have to deal with changes in the types, structures of your assets in AF, etc. 
To be able to perform such changes, you need to understand how to properly cleanup your development 
environment, and when cleanups are required. 
 
As a rule of thumb, you need to perform a cleanup: 

* Change was made to a type (basically – any change): 

  * You modified a name or description of the type or one of its properties 
  * You added, removed, renamed a property 
  * You changed a type of a property (i.e. from number to string, etc.) 
  
* Change was made to a container 

  * You redefined container typeid to another dynamic type 
  
* Change was made to a data (except of data values that you send to containerids): 

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
 
Operation #1 is required always. 

Operation #2 is required if your application defines and links static types. 

Operation #3 is required if you previously sent data values to containers. 
