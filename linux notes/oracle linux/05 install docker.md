# Install Docker on Oracle lin

Source: https://oracle-base.com/articles/linux/docker-install-docker-on-oracle-linux-ol8

Attention: Docker commands run as the "root" user

### 1. Servers in Iran?
If your servers are located in Iran, Then you have to set
special DNS server IPs to access Docker repositories.
Find _resolv.conf_ file in _/etc_. If the file does not 
exist, then create it. Finally, put the __Shekan__ DNS
IPs in the file. Like:
```
nameserver  SHEKAN-DNS-IP-ADRESS-1
nameserver  SHEKAN-DNS-IP-ADRESS-2
```

### 2. Enable required repositories
Enable all the required repositories. To do this you are going to need the yum-utils package.
```
$ dnf install -y dnf-utils zip unzip
$ dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
```

### 3. Install Docker
Execute the following commands. docker-ce is the community
edition. The key _-y_ indicates that all the required 
dependencies will be installed. The ___--nobest___ key
sets best option to False, so that transactions are not
limited to the best candidates only.
```
$ dnf remove -y runc
$ dnf install -y docker-ce --nobest
```

### 4. Finish Docker Setup
Enable and start the Docker service.

```
$ systemctl enable docker.service
$ systemctl start docker.service
```

You can get information about docker using the following commands.

```
$ systemctl status docker.service
$ docker info
$ docker version
```
