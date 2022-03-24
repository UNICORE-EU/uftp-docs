.. _uftp-overview:

UFTP Overview
*************

UFTP (**U**\ NICORE **FTP**) is a file transfer tool similar to Unix FTP. Its main features include 
high-performance file transfers from client to server (and vice versa), list directories, 
make/remove files or directories, sync files and data sharing. In addition, users can easily 
share their data even with users who do not have Unix-level access to the data.


UFTP Features
~~~~~~~~~~~~~

- Based on FTP protocol with separate authentication via RESTful APIs 

- Powerful :ref:`UFTP standalone client <uftp-client>` available

- Optional: multiple FTP sessions per client for large-scale transfers,
  encryption of the data streams, compression of the data streams

- Flexible integration options (authentication, user mapping), firewall-friendly

- System requirements: client: Java 8+, server: Python3


UFTP Architecture
~~~~~~~~~~~~~~~~~

.. image:: _static/uftp-arch.png
  :width: 400
  :alt: UFTP Architecture

The UFTP file server, called :ref:`UFTPD <uftpd>`, listens on two ports (which may be on two 
different network interfaces):

 - the command port receives control commands
 - the listen port accepts data connections from clients.

The UFTPD server is *controlled* by an :ref:`authserver` or `UNICORE/X
<https://unicore-docs.readthedocs.io/en/latest/admin-docs/unicorex/>`__ via the
command port, and receives/sends data directly from/to a client
machine (which can be an actual user client machine or another
server). The client, e.g. :ref:`uftp-client`, connnects to the *listen* port, which has to
be accessible from external machines. The client opens additional data commection(s) via the 
passive FTP protocol.


UFTP Applications and Use Cases
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Secure, high-performance data access/transfer
   - Powerful :ref:`UFTP commandline client <uftp-client>`
 
* Integrate data access/transfer functionality into web applications
   - RESTful APIs for authentication and FTP compliance for data access/transfer

* Data sharing in HPC environments
   - Authenticated or anonymous access

* UNICORE integration
   - Server-server file transfer and data staging for HPC applications and workflows
   - Integrated into UNICORE clients for fast file upload and download