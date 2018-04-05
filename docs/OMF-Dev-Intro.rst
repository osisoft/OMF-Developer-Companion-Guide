PI Server data ingress using OMF 
================================

You use OSIsoft Message Format (OMF) to achieve high-throughput asynchronous data ingress into PI System. See the OMF specification located `here <http://omf-docs.osisoft.com/en/v1.0/>`_.

The following terms and references might be useful for understanding the information in this and subsequent topics: 

* A producer of OMF messages intended for the PI System is called an *OMF application instance*. 
* Every producer must be registered with the PI System to be able to ingress OMF data. For details about the registration 
  process, see the *PI Data Collection Manager* user manual. 
* Upon registration, the producer is given a unique OMF application instance name. The name serves as a root AF Element node 
  name, and also as a prefix for all PI points created by the OMF application instance. 
* Upon registration, producers are categorized by OMF application *types*. A type serves as a namespace for unique OMF type 
  and container definitions. An OMF application type also provides part of the AF Element Template name, which is created 
  for OMF types that are classified as static (See :doc:`OMF_Msg_Types_topic`). 
* There are three message types, which perform several different actions in the PI System.
  The order in which the messages are sent, and whether they are required is important to understand 
  (see Quick Start section). 
* Before you begin developing OMF applications, please take the time to review the following topics:

.. toctree::

        OMF_Before
