
## permission denied when run pip or conda install
it is because the python or conda is installed with sudo or root user. Here is one way to solve it
```
sudo env "PATH=$PATH" conda install ...
```

## change virtualbox disk size
if the virtualbox disk is dynamic, follow this document.
http://derekmolloy.ie/resize-a-virtualbox-disk/   
If the virtualbox disk is fixed size, use cloneVDI to increase the disk size.
https://clonevdi.software.informer.com/2.5/   
Then follow the first doc to expand the partition.

## install python3 on CentOS7
https://linuxize.com/post/how-to-install-python-3-on-centos-7/  
https://www.rosehosting.com/blog/how-to-install-python-3-6-4-on-centos-7/
https://janikarhunen.fi/how-to-install-python-3-6-1-on-centos-7
I found the third link is perfect. I did not get any error.
Here are my commands
```
sudo yum -y update
sudo yum -y install yum-utils
sudo yum -y groupinstall development
sudo yum -y install https://centos7.iuscommunity.org/ius-release.rpm
sudo yum -y install python36u
sudo yum -y install python36u-pip
sudo yum -y install python36u-devel
sudo yum -y install git
'''
Now python3.6 is installed.
'''
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1
sudo update-alternatives --install /usr/bin/pip3 pip3 /usr/bin/pip3.6 1
```
Now you can run python3 and pip3 directly
```

sudo pip3 install --upgrade pip
sudo pip3 install pipenv
```


## setup the default python
http://ubuntuhandbook.org/index.php/2017/07/install-python-3-6-1-in-ubuntu-16-04-lts/  
When you have multiple versions python installed on unix, you may want to specify which one is python3
```
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 1

sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 2
```
Then 
```
sudo update-alternatives --config python3
```
select the index of the python version

## setup DNS server
add below into ```/etc/sysconfig/network-scripts/ifcfg-<YOUR Adapter>```

DNS1="127.0.0.1"
DNS2="8.8.8.8"
DNS3="8.8.4.4"

##CentOS 7 / RHEL 7 restart network service
```
sudo systemctl restart network.service
```
This is the same file to change ip address

## port listening
```
sudo lsof -i -P -n | grep LISTEN
```
sudo is mandatory here.

## install docker on centos
```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce
sudo usermod -aG docker $(whoami)
sudo systemctl enable docker.service
sudo systemctl start docker.service
```





