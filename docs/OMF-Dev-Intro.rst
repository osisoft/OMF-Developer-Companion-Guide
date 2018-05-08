On-premises PI System data ingress using OMF
============================================

You use OSIsoft Message Format (OMF) to achieve high-throughput asynchronous data ingress into an on-premises PI System.
See the OMF specification located `here <http://omf-docs.osisoft.com/en/v1.0/>`_.

The following terms and references might be useful for understanding the information in this and subsequent topics:

* A producer of OMF messages intended for the PI System is called an *OMF application*. 
* Every OMF application must be registered with the PI System to be able to ingress OMF data. For details 
  about the registration process, see the `PI Connector Administration Guide <https://techsupport.osisoft.com/Downloads/File/40489fc5-e515-4669-b185-8866a9f9f616>`_. 
* Upon registration, the OMF application is given a unique instance name. The name serves as a root Asset Framework Element node 
  name, and also as a prefix for all PI points created by the OMF application. 
* Upon registration, OMF applications are categorized by OMF application *types*. A type serves as a namespace for unique OMF type 
  and container definitions. An OMF application type also provides part of the Asset Framework Element Template name, which is created 
  for OMF types that are classified as static (See :ref:`OMF_Msg_Types_topic`). 
* There are three message types, which perform several different actions in the PI System.
  The order in which the messages are sent, and whether they are required, is important to understand
  (see :ref:`OMF_Quick_Start_topic`).



.. toctree::

        OMF_Before
