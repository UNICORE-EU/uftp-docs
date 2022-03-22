.. _uftp-client-building:

Building 
========

Prerequisites
-------------

You need Java and Apache Maven. Check the versions given in the `pom.xml 
<https://github.com/UNICORE-EU/uftp/blob/master/pom.xml>`__ file. 


Creating distribution packages
------------------------------

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



