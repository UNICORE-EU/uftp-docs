.. _uftp-client-installing:


Installation
============


Prerequisites
-------------

* Java 8 or later (OpenJDK preferred)
	
* Access to a :ref:`UFTP authentication service <authserver>` and the corresponding :ref:`UFTPD server <uftp>`. 

To use the client, you need to know the address of the :ref:`UFTP authentication service <authserver>`. You need also to have the valid credentials for the UFTP authentication.


Installation and Configuration
------------------------------

The UFTP client distribution packages are available from https://sourceforge.net/projects/unicore/files/Clients/UFTP-Client. 

If using the ``zip`` or ``tar archive``, unpack it in a location of your choice. Add the ``bin`` directory to your path. Alternatively, you can
link or copy the ``bin/uft`` script to a directory that is already on
your path, in this case edit the script and setup the required directories.

If you use the ``rpm`` or ``deb`` package, install it using the package manager of your Linux distribution.




