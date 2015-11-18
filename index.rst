Prototype Binary Software Distribution Methods
==============================================

SQRE is investigating a number of methods for distributing binary builds of
LSST DM's science pipeline or "application" software. Initial target platforms
are Centos 6 & 7, with support for OSX planned for as soon as build resources
become available.

We envision producing binary distribution products for official releases,
weekly development releases, and eventually on a nightly basis.  Construction
of a binary product on demand is also a possibility.  Work is underway to fully
automate this process.

Methods being evaluated include:

- OpenStack images on the NCSA Nebula system
- AWS images (AMIs)
- docker containers
- cernvmfs (see Fabio Hernandez's `lsst-cvmfs`_)
- tarballs of an installed build tree
- "all-in-one" RPMs
- QEMU/KVM images

.. _lsst-cvmfs: https://github.com/airnandez/lsst-cvmfs

Currently Available Formats
---------------------------

* :ref:`nebula-images`
* :ref:`docker-containers`

Open questions
--------------

- How many users would like Debian/Ubuntu based VM images, containers, packages?


Vagrant based demonstration
===========================

Vagrant is a tool to "Create and configure lightweight, reproducible, and
portable development environments." -- in other words, it is essentially a user
friendly interface for spawn and accessing virtual machines.

A demonstration of launching VM instances on the NCSA Nebula OpenStack system
with vagrant has been created called `vagrant-nebula`_.  The two brief walk
throughs below use this repository.

This simple `vagrant cheat sheet`_ may be useful.

.. _vagrant cheat sheet: https://gist.github.com/wpscholar/a49594e2e2b918f4d0c4

Initial Setup
--------------

* Follow the instructions in :doc:`installing-vagrant`.
* Follow the instructions for `Getting Nebula Credentials`_ and source the the
  rc file into your shells environment.

.. _vagrant-nebula: https://github.com/lsst-sqre/vagrant-nebula

Running a docker image on top a Nebula instance
------------------------------------------------

.. code-block:: sh

    git clone https://github.com/lsst-sqre/vagrant-nebula.git
    cd vagrant-nebula
    vagrant up el7-docker
    vagrant ssh el7-docker

    docker pull lsstsqre/centos:7-stack-lsst_apps-w_2015_45
    docker run -ti lsstsqre/centos:7-stack-lsst_apps-w_2015_45

    source /opt/lsst/software/stack/loadLSST.bash
    curl -L https://github.com/lsst/lsst_dm_stack_demo/archive/11.0.tar.gz | tar xvzf -
    cd lsst_dm_stack_demo-11.0
    setup obs_sdss
    ./bin/demo.sh --small

Running a Nebula instance with a pre-built science binaries
-----------------------------------------------------------

.. code-block:: sh

    git clone https://github.com/lsst-sqre/vagrant-nebula.git
    cd vagrant-nebula
    vagrant up el7
    vagrant ssh el7

    source /opt/lsst/software/stack/loadLSST.bash
    curl -L https://github.com/lsst/lsst_dm_stack_demo/archive/11.0.tar.gz | tar xvzf -
    cd lsst_dm_stack_demo-11.0
    setup obs_sdss
    ./bin/demo.sh --small

.. _Getting Nebula Credentials: https://github.com/lsst-sqre/vagrant-nebula#getting-nebula-credentials

.. _nebula-images:

OpenStack/Nebula images
=======================


Two images have been published under the LSST project on the NCSA Nebula system.

.. _table-nebula:

.. table:: Available Nebula Images

    +--------------------------------------+--------------------------------------------------------+
    | ID                                   | Name                                                   |
    +--------------------------------------+--------------------------------------------------------+
    | 6d24e0d0-d7e9-42ea-941b-90025fde15f7 | centos-7-stack-lsst_apps-w_2015_45-20151113225236      |
    +--------------------------------------+--------------------------------------------------------+
    | 3c36f5d9-2110-40d4-90da-c2ab89be8781 | centos-6-stack-lsst_apps-w_2015_45-20151113225236      |
    +--------------------------------------+--------------------------------------------------------+

.. _docker-containers:

Docker Containers
=================

Demo docker images have being published via Docker Hub under the
`lsstsqre/centos`_ namespace. Instructions for configuring `docker` on `Centos
7` are provided in :doc:`installing-docker`.

.. _lsstsqre/centos: https://hub.docker.com/r/lsstsqre/centos/tags/


.. _table-docker:

.. table:: Available docker tags

    +---------------------------------------------+
    | Tag                                         |
    +---------------------------------------------+
    | lsstsqre/centos:7-stack-lsst_apps-w_2015_45 |
    +---------------------------------------------+
    | lsstsqre/centos:7-stack-lsst_apps-w_2015_45 |
    +---------------------------------------------+


Example of pulling and running a docker container

.. code-block:: sh

    docker pull lsstsqre/centos:7-stack-lsst_apps-w_2015_45
    docker run -ti lsstsqre/centos:7-stack-lsst_apps-w_2015_45

See Also
========

* `vagrant-nebula`_
* :doc:`installing-vagrant`
* :doc:`installing-docker`
* `vagrant cheat sheet`_
