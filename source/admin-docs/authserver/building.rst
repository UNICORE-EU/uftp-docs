.. _auth-server-building:

Building
========

Prerequisites
-------------

You need Java 8 and Apache Maven.

Buiding Java code
-----------------

The java code is then built and tested using

.. code:: console

	$ mvn install


Creating distribution packages
------------------------------

The following commands create the distribution packages
in tgz, deb and rpm formats.


tgz
~~~

.. code:: console

	$ mvn package -DskipTests -Ppackman -Dpackage.type=bin.tar.gz


deb
~~~

.. code:: console

	$ mvn package -DskipTests -Ppackman -Dpackage.type=deb -Ddistribution=Debian


rpm
~~~

.. code:: console

	$ mvn package -DskipTests -Ppackman -Dpackage.type=rpm -Ddistribution=RedHat



