.. _uftp:

UFTP Documentation
******************

**UFTP** (**U**\ NICORE **F**\ ile **T**\ ransfer **P**\ rotocol) is a high-performance data streaming library and file transfer tool. It can be also integrated into `UNICORE <https://www.unicore.eu>`_, allowing to transfer data from client to server (and vice versa), as well as providing data staging and third-party transfer between UFTP-enabled UNICORE sites.

UFTP Components
~~~~~~~~~~~~~~~

The UFTP contains client and server components for the UNICORE high-performance file transfer toolkit:

* :ref:`uftp-client` - the standalone "uftp" client application
* :ref:`uftpd` - the 'uftpd' server
* :ref:`authserver` - a set of services providing authentication for UFTP as well as data sharing features

UFTP is best used using the client-side application (:ref:`uftp-client`), but is easily integrated into custom applications due to its FTP compliance.

A full UFTP server installation consists of two parts:

 #. The :ref:`UFTP file server <uftpd>`

 #. Either a UNICORE/X server or a standalone authentication service (:ref:`authserver`).



UFTP features
~~~~~~~~~~~~~

 - dynamic firewall port opening using passive FTP. :ref:`UFTPD <uftpd>` requires only 
   a single open port

 - :ref:`UFTP standalone client <uftp-client>` available

 - supports third-party FTP clients such as ``curl`` or ``ftp``, 
   after getting a one-time password via the :ref:`authserver` (RESTful API)
   or a UNICORE/X storage endpoint

 - integrated into UNICORE clients for fast file upload and download

 - integrated with UNICORE servers for fast data staging and
   server-to-server file transfers

 - optional encryption of the data streams using a  symmetric 
   key algorithm

 - optional compression of the data streams (using gzip)

 - optional multiple TCP streams per data connection based on code from the JPARSS library (Copyright (c) 2001 Southeastern Universities Research Association, Thomas Jefferson National Accelerator Facility)

 - partial reads/writes to a file. If supported by the filesystem,
   multiple UFTP processes can thus read/write a file in parallel
   (striping)

 - support for getting file checksums with the HASH command

 - command port protected by SSL


How does UFTP work
~~~~~~~~~~~~~~~~~~

The :ref:`UFTP file server <uftpd>`, called 'uftpd', listens on two ports (which may be on two different network interfaces):

 - the command port receives control commands

 - the listen port accepts data connections from clients.

The uftpd server is "controlled" by an :ref:`Auth server <authserver>` or UNICORE/X via the
command port, and receives/sends data directly from/to a client
machine (which can be an actual user client machine or another
server). The client connnects to the "listen" port, which has to
be accessible from external machines. The client opens additional data commection(s) via the passive FTP protocol.


Getting Support
~~~~~~~~~~~~~~~

For more information, please see the :ref:`support` page.



.. toctree::
	:maxdepth: 2
	:caption: UFTP Documentation
	:hidden:

	uftp-client/index
	uftpd/index
	authserver/index
	
.. raw:: html

   	<hr>

.. toctree::
	:hidden:
	
	support
	license
   
	
   
Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`


Last updated: |today|
