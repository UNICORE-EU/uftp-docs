.. _uftp-client-installing:


Installation
============

Prerequisites
-------------

* Java 8 or later (OpenJDK preferred)

* Access to a UFTP authentication service (either a `UNICORE/X
  <https://unicore-docs.readthedocs.io/en/latest/admin-docs/unicorex/index.html>`_ server or the 
  :ref:`authserver`) and to the corresponding :ref:`uftpd`. 

To use the client, you need to know the address of the UFTP authentication service. You need also 
to have the valid credentials for the UFTP authentication.


Installation and Configuration
------------------------------

The UFTP client distribution packages are available from 
https://sourceforge.net/projects/unicore/files/Clients/UFTP-Client. 

If using the ``zip`` or ``tar archive``, unpack it in a location of your choice. Add the ``bin`` 
directory to your path. Alternatively, you can
link or copy the ``bin/uft`` script to a directory that is already on
your path, in this case edit the script and setup the required directories.

If you use the ``rpm`` or ``deb`` package from `Linux repositories 
<https://sourceforge.net/p/unicore/wiki/Linux_Repositories/>`_, install it using the package 
manager of your Linux distribution.




