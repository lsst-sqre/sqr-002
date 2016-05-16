:orphan: true

Installing Docker
=================

Centos 7
--------

.. code-block:: sh

    sudo yum install -y docker
    sudo systemctl enable docker
    sudo systemctl start docker
    sudo groupadd --system dockerroot
    sudo chgrp dockerroot /var/run/docker.sock
    sudo usermod --append --groups dockerroot $USER

In order for the permissions on ``docker.sock`` to persist across service/system
restarts, this line needs to be inserted into the ``systemd`` docker service file at
``/lib/systemd/system/docker.service/`` in the ``[Service]`` section.

.. code-block:: sh

    [Service]
    ...
     ExecStartPost=/usr/bin/chown root:dockerroot /var/run/docker.sock

The service file may be edited by hand or in an automated fashion with ``augeas``
(recommended).

.. code-block:: sh

    sudo yum install -y augeas
    sudo augtool <<'END'
    set /files/lib/systemd/system/docker.service/Service/ExecStartPost/command "/usr/bin/chown"
    set /files/lib/systemd/system/docker.service/Service/ExecStartPost/arguments/1 "root:dockerroot"
    set /files/lib/systemd/system/docker.service/Service/ExecStartPost/arguments/2 "/var/run/docker.sock"
    save
    END

The group membership change for `$USER` does not effect shells spawned under
the current login session.  In order to use docker, the user needs to either
logout/login (recommended) or cause a new session to be created.  Eg.

.. code-block:: sh

    exec sudo su -l $USER

