Prototype Binary Software Distribution Methods
==============================================

SQuaRE is investigating a number of methods for distributing binary builds of
LSST DM's science pipeline or "application" software. Initial target platforms
are CentOS 6 & 7, with support for OS X planned for as soon as build resources
become available.

We envision producing binary distribution products for official releases,
weekly development releases, and eventually on a nightly basis.  Construction
of a binary product on demand is also a possibility.  Work is underway to fully
automate this process.

Methods being evaluated include:

- Conda packages for Anaconda/Miniconda
- OpenStack images on the NCSA Nebula system
- AWS images (AMIs)
- docker containers
- CernVM-FS (see Fabio Hernandez's `lsst-cvmfs`_)
- tarballs of an installed build tree
- "all-in-one" RPMs
- QEMU/KVM images

.. _lsst-cvmfs: https://github.com/airnandez/lsst-cvmfs

Currently Available Formats
---------------------------

In this note, we describe two formats for pre-built binaries:

* :ref:`conda-packages`
* :ref:`nebula-images`
* :ref:`docker-containers`
* :ref:`aws-amis`

Desired Feedback
----------------

- Success and/or failure reports -- is anything broken?

- Are there any other binary distribution methods desired by end-users?  Eg.
  Debian/Ubuntu based VM images, containers, packages?

- How do binary products integrate into existing workflows?

.. _conda-packages:

Conda Packages
==============

A repo of "demo" conda packages (a "channel" in conda-speak) has been made
available.  Presently, only packages built on RHEL5 are available that are
expected to have broad compatibility across current distributions.  OSX packages
are planned to be added in the future.

The URL of the conda repo is: https://conda-test.lsst.codes/dev/

Bootstrapping a Miniconda environment + installing LSST packages
----------------------------------------------------------------

This is an example of "bootstrapping" a new Miniconda environment under the user
account's home directory, installing the conda packages for the LSST
`lsst_apps` meta-package, and executing the "stack demo".

.. code-block:: sh

    curl -sSL https://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh > miniconda.sh && bash miniconda.sh -b -p ~/miniconda && rm -f miniconda.sh
    export PATH="$PWD/miniconda/bin:$PATH"
    conda config --add channels https://conda-test.lsst.codes/dev/
    conda install -y lsst-apps

    # lives at ./miniconda/bin/eups-setups.sh
    source eups-setups.sh

    curl -L https://github.com/lsst/lsst_dm_stack_demo/archive/11.0.tar.gz | tar xvzf -
    cd lsst_dm_stack_demo-11.0
    setup obs_sdss
    ./bin/demo.sh --small

Vagrant based demonstration
===========================

`Vagrant`_ is a tool to "Create and configure lightweight, reproducible, and
portable development environments." -- in other words, it is essentially a user
friendly interface for spawning and accessing virtual machines.

A demonstration of launching VM instances on the NCSA Nebula OpenStack system
with Vagrant has been created called `vagrant-nebula`_.  The two brief walk
throughs below use this repository.

This simple `vagrant cheat sheet`_ may be useful.

.. _Vagrant: https://vagrantup.com
.. _vagrant-nebula: https://github.com/lsst-sqre/vagrant-nebula
.. _vagrant cheat sheet: https://gist.github.com/wpscholar/a49594e2e2b918f4d0c4

Initial Setup
-------------

To follow the examples, we assume that the reader already has a working Nebula
account, and has `Vagrant`_ installed.

* Follow the instructions in :doc:`installing-vagrant`.
* Follow the instructions for `Getting Nebula Credentials`_ and source the
  rc file into your shell's environment.

.. _Getting Nebula Credentials: https://github.com/lsst-sqre/vagrant-nebula#getting-nebula-credentials

Now, choose your adventure:

* :ref:`Docker container <vagrant-docker-run>`
* :ref:`Nebula instance <vagrant-nebula-run>`

.. _vagrant-nebula: https://github.com/lsst-sqre/vagrant-nebula

.. _vagrant-docker-run:

Running a docker image on top a Nebula instance
-----------------------------------------------

In your local computer's shell, clone and run `vagrant-nebula`_:

.. code-block:: sh

    git clone https://github.com/lsst-sqre/vagrant-nebula.git
    cd vagrant-nebula
    vagrant up el7-docker
    vagrant ssh el7-docker

The last command opened an SSH session to an instance on Nebula. In the
instance's shell, run docker:

.. code-block:: sh

    docker pull lsstsqre/centos:7-stack-lsst_apps-w_2015_45
    docker run -ti lsstsqre/centos:7-stack-lsst_apps-w_2015_45

.. _vagrant-nebula-run:

Running a Nebula instance with a pre-built science binaries
-----------------------------------------------------------

An alternative workflow is to run a pre-built stack on the Nebula instance
itself, rather than in a container.

Starting in your local shell, run `vagrant-nebula`_:

.. code-block:: sh

    git clone https://github.com/lsst-sqre/vagrant-nebula.git
    cd vagrant-nebula
    vagrant up el7
    vagrant ssh el7

Now in the shell of the instance on Nebula, you can access the pre-built stack
at ``/opt/lsst/software/stack``.

Running the "stack" demo
------------------------

From a shell on a VM or inside a running docker container, it is possible to
run the "stack" demonstration repo.

Here we run the demo:

.. code-block:: sh

    source /opt/lsst/software/stack/loadLSST.bash
    curl -L https://github.com/lsst/lsst_dm_stack_demo/archive/11.0.tar.gz | tar xvzf -
    cd lsst_dm_stack_demo-11.0
    setup obs_sdss
    ./bin/demo.sh --small


Releasing Nebula resources
--------------------------

Exit from any containers, shells/ssh sessions, etc. and return to the prompt
from which ``vagrant ssh ...`` was invoked.

Destructive shutdown
^^^^^^^^^^^^^^^^^^^^

This will terminate the Nebula instance and release the the associated storage.
*This is a destructive operation that destroys all data and is non-reversible.*

.. code-block:: sh

    vagrant destroy -f el7


Preserve instance disk state and shutdown
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Vagrant instances, or "boxes" in Vagrant-speak, which have been ``halt``-ed can be restarted by running ``vagrant up ....``.

.. code-block:: sh

    vagrant halt el7


.. _nebula-images:

OpenStack/Nebula images
=======================

Two images have been published under the LSST project on the NCSA Nebula system.

.. _table-nebula:

.. table:: Available Nebula Images

   +--------------------------------------+---------------------------------------------------+
   | ID                                   | Name                                              |
   +--------------------------------------+---------------------------------------------------+
   | 6d24e0d0-d7e9-42ea-941b-90025fde15f7 | centos-7-stack-lsst_apps-w_2015_45-20151113225236 |
   +--------------------------------------+---------------------------------------------------+
   | 3c36f5d9-2110-40d4-90da-c2ab89be8781 | centos-6-stack-lsst_apps-w_2015_45-20151113225236 |
   +--------------------------------------+---------------------------------------------------+


.. _docker-containers:

Docker Containers
=================

Demo docker images have being published via Docker Hub under the
`lsstsqre/centos`_ namespace. Instructions for configuring Docker on CentOS
7 are provided in :doc:`installing-docker`.

.. _lsstsqre/centos: https://hub.docker.com/r/lsstsqre/centos/tags/


.. _table-docker:

.. table:: Available docker tags

    +---------------------------------------------+
    | Tag                                         |
    +---------------------------------------------+
    | lsstsqre/centos:7-stack-lsst_apps-w_2015_45 |
    +---------------------------------------------+
    | lsstsqre/centos:6-stack-lsst_apps-w_2015_45 |
    +---------------------------------------------+


Example of pulling and running a docker container

.. code-block:: sh

    docker pull lsstsqre/centos:7-stack-lsst_apps-w_2015_45
    docker run -ti lsstsqre/centos:7-stack-lsst_apps-w_2015_45


.. _aws-amis:

Amazon Web Services (AWS) AMIs
==============================

Public AMIs have be posted in the two most popular [Continental US] AWS
regions. We are assuming that AWS/EC2 users are familiar with how to launch an
instance.  AWS also provides copious documentation.  See `Launching an Instance`_ for a reasonable documentation entry point.

.. _table-aws:

.. table:: Available AMIs

    +-----------+-------------------------------------------+
    | region    | ami-id                                    |
    +===========+===========================================+
    | **centos-7-stack-lsst_apps-w_2015_45-20151130234354** |
    +-----------+-------------------------------------------+
    | us-east-1 | ami-e2490b88                              |
    +-----------+-------------------------------------------+
    | us-west-2 | ami-9a0f1dfb                              |
    +-----------+-------------------------------------------+
    | **centos-6-stack-lsst_apps-w_2015_45-20151130234301** |
    +-----------+-------------------------------------------+
    | us-east-1 | ami-e44b098e                              |
    +-----------+-------------------------------------------+
    | us-west-2 | ami-7b0b191a                              |
    +-----------+-------------------------------------------+

.. _Launching an Instance: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html

See Also
========

* `vagrant-nebula`_
* :doc:`installing-vagrant`
* :doc:`installing-docker`
* `vagrant cheat sheet`_
* `aws.amazon.com <https://aws.amazon.com/>`_
