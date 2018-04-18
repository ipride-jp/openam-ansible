# openam-ansible

## What's the openam-ansible
The openam-ansible automate installation of openam with ansible.

With openam-ansible, you can build the following environments.
* openam all-in-one
    * openam + opendj
* openam HA
    * openam x 2
    * opendj x 2

The version which openam-ansible supports is,
* openam 13.0
* opendj 3.0

## Installation guide
### Server requirements
The requirements of the servers, where openam and opendj will be installed, are the followings.
* CentOS 7.x
* Tomcat 7.x
* OpenJDK 8(JDK)

### Control machine requirements
The requirements of the control machine, where the ansible commands will be executed, are the followings.
* ansible 2.2

As to the installation of ansible, you can refer to the page [Install ansible](http://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-control-machine).

### Download openam-ansible
Execute the following command in the control machine.

    $ git clone https://github.com/ipride-jp/openam-ansible

### All-in-one environment

#### Server specification
* Memory >= 2GB

#### Define hosts
Define ip addresses of openam and opendj server.
```
$ cat inventories/all-in-one/hosts
[opendj1]
13.114.1.246 # change to your ip address

[openam1]
13.114.1.246 # change to your ip address

[openam:children]
openam1

[opendj:children]
opendj1
```

Set up ssh agent and add private key to ssh login openam and opendj server.
```
$ eval `ssh-agent`
$ ssh-add openam.pem # the private key to ssh login openam and opendj server
$ ssh-add -l # confirm private keys added
```

Confirm that openam and opendj server are available to install.
```
$ ansible -i inventories/all-in-one/hosts -u centos openam -m ping
13.114.1.246 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

#### Install
Add the domain name to the host file of openam1 server.
```
$ cat /etc/hosts
...

# If using aws EC2 instance, use private ip address. Otherwise, use the local ip address.
172.31.19.165 test.sample-openam.com
```

Execute the following ansible command to install.

```
$ ansible-playbook -i inventories/all-in-one -u centos -s all-in-one.install.yml
```

#### Uninstall
Execute the following ansible command to uninstall.
```
$ ansible-playbook -i inventories/all-in-one -u centos -s all-in-one.uninstall.yml
```
