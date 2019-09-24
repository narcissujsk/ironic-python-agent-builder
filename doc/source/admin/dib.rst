diskimage-builder images
========================

Images built using diskimage-builder_ are recommended for production use on
real hardware.

Building
--------

... with the helper script
~~~~~~~~~~~~~~~~~~~~~~~~~~

To build an image using diskimage-builder_, run:

.. code-block:: shell

    ironic-python-agent-builder <distribution, e.g. ubuntu>

You can add other diskimage-builder_ elements via the ``-e`` flag. For example,
dhcp-all-interfaces_ is required for automatic DHCP on some operating systems
(e.g. CentOS 7):

.. code-block:: shell

    ironic-python-agent-builder -e dhcp-all-interfaces centos7

You can specify the base name of the target images:

.. code-block:: shell

    ironic-python-agent-builder -o my-ipa -e dhcp-all-interfaces centos7

... with diskimage-builder
~~~~~~~~~~~~~~~~~~~~~~~~~~

You can also use diskimage-builder_ directly. First you need to set the
``ELEMENTS_PATH`` variable to the correct location:

* If installed with ``pip install --user``, use:

  .. code-block:: bash

    export ELEMENTS_PATH=$HOME/.local/share/ironic-python-agent-builder/dib

* On Fedora/CentOS/RHEL (installed via ``sudo pip install`` or from packages):

  .. code-block:: bash

    export ELEMENTS_PATH=/usr/share/ironic-python-agent-builder/dib

* On Debian and its derivatives, if installed with ``sudo pip install``:

  .. code-block:: bash

    export ELEMENTS_PATH=/usr/local/share/ironic-python-agent-builder/dib

Now you can build an image adding the ``ironic-python-agent-ramdisk`` element,
for example:

.. code-block:: shell

    disk-image-create -o ironic-python-agent \
        ironic-python-agent-ramdisk centos7 dhcp-all-interfaces

To use a specific branch of ironic-python-agent, use:

.. code-block:: bash

    export DIB_REPOREF_ironic_python_agent=origin/stable/queens

Advanced options
----------------

SSH access
~~~~~~~~~~

SSH access can be added to DIB built IPA images with the dynamic-login_
or the devuser_ element.

The *dynamic-login* element allows the operator to inject an SSH key at boot
time via the kernel command line parameters:

* Add ``sshkey="ssh-rsa <your public key here>"`` to ``pxe_append_params``
  setting in the ``ironic.conf`` file. Disabling SELinux is required for
  systems where it is enabled, it can be done with ``selinux=0``.

  .. warning:: Quotation marks around the public key are important!

* Restart the ironic-conductor.

The *devuser* element allows creating a user at build time, for example:

.. code-block:: bash

  export DIB_DEV_USER_USERNAME=username
  export DIB_DEV_USER_PWDLESS_SUDO=yes
  export DIB_DEV_USER_AUTHORIZED_KEYS=$HOME/.ssh/id_rsa.pub
  disk-image-create debian ironic-python-agent-ramdisk devuser

.. _diskimage-builder: https://docs.openstack.org/diskimage-builder
.. _dhcp-all-interfaces: https://docs.openstack.org/diskimage-builder/latest/elements/dhcp-all-interfaces/README.html
.. _dynamic-login: https://docs.openstack.org/diskimage-builder/latest/elements/dynamic-login/README.html
.. _devuser: https://docs.openstack.org/diskimage-builder/latest/elements/devuser/README.html