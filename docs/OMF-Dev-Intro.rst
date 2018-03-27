PI Server data ingress using OMF 
================================

You use OSIsoft Message Format (OMF) to achieve high-throughput asynchronous data ingress into PI Server. 
The following terms and references might be useful for understanding the information in this and subsequent topics: 

* A producer of OMF messages intended for PI Server is called OMF application instance. 
* Every producer shall be registered with PI Server to be able to ingress OMF data. For registration process, see PI Data Collection
  Manager user manual. 
* Producers are categorized by OMF application types. Type serves as a namespace for unique OMF type definitions (see *OMF 1.0
  specification*), and their translations into PI AF Element Templates (see the OMF specification `here <http://omf-docs.osisoft.com/en/v1.0/>`_). 
* OMF messages are divided into three categories (see OMF 1.0 specification), which perform several different actions in the PI Server.
  Sequence of the messages, and whether they are required to be sent on each producer restart are important to understand 
  (see Quick Start section). 

.. toctree::

        OMF_Before
