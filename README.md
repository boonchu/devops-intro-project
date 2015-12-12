Instructions for Intro to DevOps Project
========================================

## Part 0 - Local Installation

1. Install VirtualBox
2. Install Vagrant
3. Install Packer
4. Install git

## Part I - Build Boxes with Packer

### To make an image for local development
1. run `packer build -only=virtualbox-iso application-server.json`
2. run `cd virtualbox`
3. run `vagrant box add ubuntu-14.04.2-server-amd64-appserver_virtualbox.box --name devops-appserver`
4. run `vagrant up`
5. run `vagrant ssh` to connect to the server


### To make application server image for cloud

See full directions at: https://www.udacity.com/wiki/ud611

### To make control (CI and monitoring) server image for cloud

## Part II - Cloning, Developing and Running the web application

1. On your local machine go to the root directory of this repository

    run `git clone https://github.com/chef/devops-kungfu.git devops-kungfu`

2. Open http://localhost:8080 from your local machine to see the app running.

3. If you want to run tests on the app - 
    In the VM run:
    `cd devops-kungfu`
    To install app specific node packages: 
    `sudo npm install`
    Now you can run tests - `grunt -v`
                                     

### Run Jenkins Locally

https://discussions.udacity.com/t/lesson-3-running-jenkins-and-graphite-locally-no-credit-card-disclosure-necessary/31907

```

cd ~/parent_directory_where_devops_was_cloned

cd devops/packer-templates
packer build -only=virtualbox-iso control-server.json
cd ../output/virtualbox

```

##### add Vagrantfile to the above directory, then

vagrant box add control-ubuntu-14.04.3-server-amd64.box --name devops-ctrlserver
vagrant up

The Vagrantfile I used is given below:

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. 

Vagrant.configure(2) do |config|

  config.vm.box = "devops-ctrlserver"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8088" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 80, host: 8080

end
```

###### Once the server is up, Graphite and Jenkins can be respectively accessed with your browser at:
http://localhost:80801
http://localhost:8080/jenkins5

I did encounter an error using "vagrant up":

```
==> default: Mounting shared folders...
    default: /vagrant => /home/roop/devops/output/virtualbox
Failed to mount folders in Linux guest. This is usually because
the "vboxsf" file system is not available. Please verify that
the guest additions are properly installed in the guest and
can work properly. The command attempted was:

mount -t vboxsf -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3` vagrant /vagrant
mount -t vboxsf -o uid=`id -u vagrant`,gid=`id -g vagrant` vagrant /vagrant

The error output from the last command was:

stdin: is not a tty
mount: unknown filesystem type 'vboxsf'

```

###### Fixed with Sync Folder

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. 

Vagrant.configure(2) do |config|

  config.vm.box = "devops-appserver"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder.
  config.vm.synced_folder "../../output", "/home/vagrant/output", create: true

end
```
