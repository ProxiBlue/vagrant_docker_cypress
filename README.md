# vagrant_docker_cypress

A demonstration on how to run cypress withing a vagrant environment, using docker, sending display to host machine

## Requirements

* Vagrant 2.2.5 or greater (important, will not work with older vagrant versions)
* Docker 18.09.7 or greater
* vagrant plugin: https://github.com/devopsgroup-io/vagrant-hostmanager

## Install

Simply clone this git repo

```
git clone git@github.com:ProxiBlue/vagrant_docker_cypress.git
```

## Usage

* Bring up the vagrant environment using: ```vagrant up```

If this is the initial provision the docker boxes will build from provided docker files. You can just as well use pre-built docker images from docker hub.
I had simply done it this way to not have dockerhub dependency

* enter the cypress instance using: ```vagrant ssh cypress```
* you can enter the web server instance with ```vagrant ssh``` (it is the default, so no name required)

Vagrant would have mapped the base folder to ```/vagrant``` inside the instance(s).

## Small Demo site

A small demo site will exist under ```/vagrant/sites/test``` which simply outputs: Hello World
It can be accessed using either the instance IP, or the HOSTNAME mapped by the hostmanager plugin: ```https://test.proxiblue.test/```

## Run Demo test

* enter the cypress instance

```
vagrant ssh cypress
```

then

```
cd /vagrant/tests
npx cypress open
``` 

You will see the demo test listed and can be run

All display will happen on teh HOST machine due to x11 mapping form host to docker



