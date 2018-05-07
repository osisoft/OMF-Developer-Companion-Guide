Introduction
============

Development of OMF applications generally adheres to the following sequence:

`Understand your data`_
  You must understand how the data you intend to send to on premise on premise PI System will be organized.
  Your OMF messages must be written so that appropriate Asset Framework templates, enumerations, and element
  trees (with all required attributes
  and associated PI points) are created in Asset Framework and PI Data Servers, and updated with your time series data.

`Write the OMF application`_
  Write OMF messages to create your reference model and start feeding data into on premise PI System
  After creating your development environment, you should register your development application
  with the  PI Data Collection Manager to obtain appropriate authorization to feed data into the on premise PI System,
  and write and send OMF messages to the PI Connector Relay Ingress HTTPS REST endpoint.

`Clean up the development environment`_
  After making development changes to your OMF types, instances, and links, you should clean up your environment
  by deleting tests and older versions. There are also some items that require manual cleanup. Note that OMF
  ingress supports only
  the creation of data in the on premise PI System and does not support deleting of objects from the on premise PI System. Also, it is a
  good practice to create a separate test or quality assurance system and database before testing or
  attempting to write code on a production system. Having a test or QA system makes it easier to remove old
  code and data and to recover from errors.

The sections below describe each of these points.

Understand your data
--------------------

* Identify your assets
   *  Physical assets, such as plants, sites, equipment, I/O devices, and so on. Physical assets can have static attributes,
      which will stay immutable, or can have values whose changes will not be recorded in PI Data Archive; for example, a serial
      number of an I/O device.

   *  Data stream measurements (representing collections of values), should be sent to PI Connector Relay ingress as one
      transaction; that is, all values of a given stream should be sent in one update, and no single value can be skipped.
      Data for each of the points in the stream is recorded by the data historian with an appropriate timestamp.

* Identify hierarchical relationships between your physical and logical assets
   *  Physical assets structure - The top-most asset, which might consist of a collection of equipment, each of which has
      a collection of I/O devices. For example: consider a vehicle top-level asset with an engine child asset,
      and wheels as children assets.
   *  Data stream measurements can be attached to any of your physical assets. For example, consider a stream consisting
      of longitude and latitude values. These values can be attached to your vehicle asset, together with one stream of
      engine RPM values.


* Identify the reference model for your data in Asset Framework
   *  A reference model can be thought of as a logical representation of your physical real-world data. The reference model
      should be understandable from a technical perspective; it is not necessary for your entire organization to understand
      it. Be aware of the tools provided with your on premise PI System, such as AF Transformer, which allows
      you to build different representations of your reference model for different business units. For more information about
      AF Transformer, see this PI Developers Club article: `AF Transformer
      <https://pisquare.osisoft.com/community/developers-club/blog/2018/02/15/welcome-to-our-newest-utility-af-transformer>`_.



Write the OMF application
-------------------------


a. Create OMF type definitions, which will represent your physical and logical real-world assets.
   These type definitions will be sent to the PI Connector Relay  ingress in OMF Type messages.

   i.  Physical assets will be presented by OMF types with ``classification: static``.
   ii. Logical assets will be presented by OMF types with ``classification: dynamic``.

b. Create OMF containers from dynamic type definitions, which will represent instances of your logical real-world assets,
   These will be sent to the PI Connector Relay ingress in OMF Container messages.

c. Create OMF assets from static type definitions, which will represent instances of your physical real-world assets.
   These will be sent to the PI Connector Relay ingress in OMF Data messages.

d. Create links between:

   i.   Root node and OMF assets created from static type definitions.
   ii.  OMF assets created from static type definitions (parent/child).
   iii. OMF assets created from static OMF type definitions and OMF containers created from dynamic type definitions.

e. Send time-series values to the containers. These will be sent to the PI Connector Relay ingress
   in OMF Data messages and stored in PI Data Server.

For more information see OMF 1.0 specification.


Clean up the development environment
------------------------------------

In OMF applications, type and container definitions and their representations in on premise PI System are immutable; that is, you cannot
change the properties of a type after it has been sent to the PI Connector Relay ingress endpoint.
The same is true for instances of these types (assets and containers), and linkage between them. After you
create instances of these types and link them together by sending container and data messages to
the PI Connector Relay ingress endpoint, you cannot redefine them.

After the OMF application has been developed and deployed, the only modified data that is expected to be ingested
is your time-series data; that is, values that are sent to the container(s) in OMF data messages.

Only when an error occurs, such as when the PI Connector Relay loses its cache, type, asset and container, or their linkage information,
should this metadata be re-sent to the PI Connector Relay. To recover from errors, either handle appropriate error codes returned with
HTTP 400 responses, or send metadata information every time your
application restarts, making sure that no changes were made to the definitions and instantiations.

As a developer, you will have to deal with changes in the types and structures of your assets in Asset Framework and elsewhere.
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

1. PI Connector Relay temporary cache location.
   Stop the PI Connector Relay process. By default, if not chosen during the PI Connector Relay setup, temporary data will be stored in
   ``%ProgramData%\OSIsoft\Tau\Relay.ConnectorHost``. Delete this folder.
   Deleting this folder removes all cache entries for all producers.

2. The Asset Framework Database that you use to create your Asset Framework asset structure. Using on premise PI System Explorer,
   perform the following:

   a.  In the Navigator pane, open Library, expand Templates/Element Templates, and delete all templates with names starting with "OMF".
   b.  In the Navigator pane, open Library, expand Templates/Enumeration Sets, and delete all enumerations with names starting with "OMF".
   c.  In the Navigator pane, open Elements, expand the Elements root node, and delete all elements and their
       children elements that have names with your OMF application registrations. Ensure you delete only the applications
       related to the one you are developing and not all of them.
   d.  Check in your changes.

3. PI Data Archive PI points that were created after the container data values were sent.
   In the on premise PI System Management Tool, open Points/Point Builder. Search for and delete all PI tags that have names starting with
   your OMF application registration name.

 **When to clean up**

* Operation #1 is always required.
* Operation #2 is required if your application defines assets and links from OMF static type definitions.
* Operation #3 is required if you had previously sent time series data values to containers.
