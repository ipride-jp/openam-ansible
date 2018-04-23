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

### HA environment

#### Define hosts
Define ip addresses of openam and opendj server.
```
$ cat inventories/ha/hosts
[opendj1]
54.199.214.92

[opendj2]
13.231.212.122

[openam1]
54.199.214.92

[openam2]
13.231.212.122

[openam:children]
openam1
openam2

[opendj:children]
opendj1
opendj2
```

Set up ssh agent and add private key to ssh login openam and opendj server.
```
$ eval `ssh-agent`
$ ssh-add openam.pem # the private key to ssh login openam and opendj server
$ ssh-add -l # confirm private keys added
```

Confirm that openam and opendj server are available to install.
```
$ ansible -i inventories/ha/hosts -u centos openam -m ping
54.199.214.92 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
13.231.212.122 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

**Set up load balancer.**<br>
For example, you can create a load balancer in aws, and add the target servers to the load balancer.

**Make load balaner as stickness.**<br>
In the load balaner of aws, you should enable "application generated cookie stickiness" with cookie name "amlbcookie".

**Set up dns.**<br>
You can create an A-record for your domain, and make the A-record refer to your load balaner. As an example, here use "ansible.company-in-cloud.com" as the dns name of the openam server.

#### Install
Change the fqdn of opendj servers in file ha/group_vars/all.yml.

```
opendj_host1: ip-172-31-19-165.ap-northeast-1.compute.internal
opendj_host2: ip-172-31-24-48.ap-northeast-1.compute.internal
```

Change openam_site_url and openam_cookie_domain in file ha/group_vars/openam.yml.
```
openam_site_url: http://ansible.company-in-cloud.com/{{openam_service_name}}
openam_cookie_domain: .company-in-cloud.com
```

Execute the following command to install.
```
$ ansible-playbook -i inventories/ha -u centos -s ha.install.yml
```

#### Config
After the status of targer servers in the load balancer become "InService", you can execute the following command to config openam.
```
$ ansible-playbook -i inventories/ha -u centos -s ha.config.yml
```

#### Uninstall
```
$ ansible-playbook -i inventories/ha -u centos -s ha.uninstall.yml
```
