Container Message 
=================

In the PI Server, containers are interpreted as instances of data streams. The keywords in the container 
objects are interpreted as follows: 

+----------------+-------------+---------------+------------------------------------------------------------+
| Property       | Type        | Optionality   | Details                                                    |
+================+=============+===============+============================================================+
| id             | String      | Required      | Unique identifier of the Container.                        |
+----------------+-------------+---------------+------------------------------------------------------------+
| typeid         | String      | Required      | ID of the dynamic type used by the Container.              |
+----------------+-------------+---------------+------------------------------------------------------------+
| typeversion    | String      | Optional      | Version of the dynamic type used by the Container. If      |
|                |             |               | omitted, version 1.0.0.0 is used.                          |
+----------------+-------------+---------------+------------------------------------------------------------+
| name           | String      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+
| description    | String      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+
| tags           | String      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+
| metadata       | String      | Optional      | Not supported.                                             |
+----------------+-------------+---------------+------------------------------------------------------------+


The Id value and its association with the typeid value must be unique for all OMF application instances 
of a given type (which you specify during producer registration). This means that in advanced scenarios, you 
may send container object declarations only once for all registered producers of this OMF application type; 
all of them will reuse the same declarations cached in the Relay, located in the following folder:

::

  %ProgramData%\OSIsoft\Tau\Relay.ConnectorHost

