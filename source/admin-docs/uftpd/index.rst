.. _uftpd:

UFTPD Server
************

The UFTPD server provides a high-performance data transfer based on passive FTP. 

.. note::  
 A full UFTP server installation consists of two parts:

  #. The :ref:`UFTPD server <uftpd>`
  #. Either a `UNICORE/X
     <https://unicore-docs.readthedocs.io/en/latest/admin-docs/unicorex/index.html>`_ or 
     the more lightweight :ref:`Auth server <authserver>` package.


The UFTP server listens on two ports (which may be on two different network interfaces):

 - the command port receives control commands

 - the listen port accepts data connections from clients.

The UFTPD server is *controlled* by an :ref:`Auth server <authserver>` or `UNICORE/X
<https://unicore-docs.readthedocs.io/en/latest/admin-docs/unicorex/index.html>`_ via the
command port, and receives/sends data directly from/to a client
machine (which can be an actual user client machine or another
server). The client, e.g. :ref:`uftp-client`, connnects to the *listen* port, which has to
be accessible from external machines. The client opens additional data commection(s) via the 
passive FTP protocol.

.. image:: ../../_static/uftp-arch.png
  :width: 400
  :alt: UFTP architecture
  
The sequence for a UFTP file transfer is as follows:
  
* the client (which can be an end-user client, or a service such as a UNICORE/X server) sends 
  an authentication request to the Auth server (or another UNICORE/X server)
   
* the Auth server sends a request to the command port of UFTPD. This request notifies the UFTPD 
  server about the upcoming transfer and contains the following information 
  
     - a *secret*, i.e. a one-time password which the client will use to authenticate itself
     - the user and group id which uftpd should use to access files
     - an optional key to encrypt/decrypt the data
     - the client's IP address
    
* the UFTPD server will now  accept an incoming client connection, provided the supplied 
  *secret* (one-time password) matches the expectation.
  
* if everything is OK, an FTP session is created, and the client can use the FTP protocol to 
  open data connections, list files, transfer data etc. Data connections are opened via 
  *passive FTP*, which allows the firewall to dynamically open the requested ports (which can 
  by any port, see below if you want to a fixed port range).
  
* for each UFTP session, UFTPD will fork a process which runs as the requested user (with the 
  requested primary group).



:doc:`manual`
  Installation and Operating the UFTPD server.


:doc:`changelog`
    The Auth server changelog.
    

.. toctree::
	:maxdepth: 5
	:caption: UFTPD Server Documentation
	:hidden:
	
	manual

.. toctree::
	:maxdepth: 1
	:hidden:
	
	changelog


