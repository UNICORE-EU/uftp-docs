.. _uftp-client-building:

|app-package-img| Building 
==========================

.. |app-package-img| image:: ../../_static/app-package.png
	:height: 32px
	:align: middle

This page shows how to build the latest version of the UFTP client directly from the sources.


Prerequisites
-------------

You need a git client, Java (11 or later) and Apache Maven. 

Cloning the GitHub repository
-----------------------------

The UFTP client is maintained as part of the 
`UFTP repository <https://github.com/UNICORE-EU/uftp>`_

Clone this repository:

.. code:: console

    $ git clone https://github.com/UNICORE-EU/uftp.git
    $ cd uftp

Creating distribution packages
------------------------------

The client code is in the ``uftp-client`` directory.

.. code:: console

    $ cd uftp-client


The following commands create the distribution packages
in ``tgz``, ``deb`` and ``rpm`` formats. The versions are taken from the `pom.xml 
<https://github.com/UNICORE-EU/uftp/blob/master/pom.xml>`__.


tgz
~~~

.. code:: console

	$ mvn package -DskipTests -Ppackman -Dpackage.type=bin.tar.gz


deb
~~~

.. code:: console

	$ mvn package -DskipTests -Ppackman -Dpackage.type=deb -Ddistribution=Debian


rpm redhat
~~~~~~~~~~

.. code:: console

	$ mvn package -DskipTests -Ppackman -Dpackage.type=rpm -Ddistribution=RedHat
