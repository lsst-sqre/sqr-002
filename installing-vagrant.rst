Installing Vagrant
==================

Official binary vagrant packages for several platforms are available from
https://www.vagrantup.com/downloads

Cut'n'paste for OSX
-------------------

.. code-block:: sh

    wget https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.4.dmg
    hdiutil mount vagrant_1.7.4.dmg
    sudo installer -package /Volumes/Vagrant/Vagrant.pkg -target /
    hdiutil unmount /Volumes/Vagrant
    rm vagrant_1.7.4.dmg

Cut'n'paste for EL
------------------

.. code-block:: sh

    sudo yum install -y https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.4_x86_64.rpm
