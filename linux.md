
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


