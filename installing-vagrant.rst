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

If you use `Homebrew`_, you might want to install the Cask instead:

.. code-block:: sh

    # Install cask, if it's not already installed.
    brew install caskroom/cask/brew-cask
    # Install the Vagrant cask.
    brew cask install vagrant

.. _Homebrew: http://brew.sh/

Cut'n'paste for EL
------------------

.. code-block:: sh

    sudo yum install -y https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.4_x86_64.rpm
