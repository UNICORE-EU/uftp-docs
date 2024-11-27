
.. _uftp-client:

UFTP client
***********

The UFTP commandline client enables users to 

* `list remote directories <manual.html#ls-command>`__ (``ls``)
 
* `upload\/download files <manual.html#cp-command>`_ (``cp``)

* `compute checksums for remote files <manual.html#checksum-command>`_ (``checksum``)

* `sync remote\/local files <manual.html#sync-command>`_ (``sync``)

* make remote directories (``mkdir``)

* delete remote files or directories (``rm``)

* `manage shares and access shared data <manual.html#data-sharing>`_ (``share``)

* `perform authentication <manual.html#auth>`_ to help integrate UFTP with other tools 

* `launch server-to-server copy operations <manual.html#rcp-command>`_ (``rcp``)


The UFTP client will connect to an authentication server (either a :ref:`UNICORE/X server 
<unicore-docs:unicorex>`
or the :ref:`authserver`) to authenticate and then to
the :ref:`uftpd` for transferring data or making a file operation.

.. image:: ../../_static/uftp-client.png
  :width: 400
  :alt: UFTP Client

The UFTP client supports username/password authentication, OIDC
token authentication and ssh-key authentication. 

The UFTP client supports multiple concurrent FTP connections for highly efficient 
data transfers in high-performance environments.

.. topic:: Features

 * :ref:`Commands <uftp-client-basic-usage>` (UNIX-like semantics) 
 
 * Supports `multi-threaded transfers <manual.html#multiple-connections>`__, 
   `encryption and compression <manual.html#encrypt-compress>`__ of the data streams

 * `Flexible authentication <manual.html#auth>`__

   * sshkey incl. support for ssh-agent (on Linux only)
   * OIDC via oidc-agent
   * Username/password


|usage-img| :doc:`basic-usage`
    Basic usage of the UFTP Client.

.. |usage-img| image:: ../../_static/usage.png
	:height: 22px
	:align: middle

|user-guide-img| :doc:`manual`
    User Manual with detailed instructions and examples for using the UFTP Client.

.. |user-guide-img| image:: ../../_static/user-guide.png
	:height: 22px
	:align: middle

|app-package-img| :doc:`building`
    Building the UFTP Client distribution packages.

.. |app-package-img| image:: ../../_static/app-package.png
	:height: 22px
	:align: middle


.. toctree::
	:maxdepth: 6
	:caption: UFTP Client Documentation
	:hidden:
      
	basic-usage
	manual
	building

