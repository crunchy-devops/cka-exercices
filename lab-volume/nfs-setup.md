# install nfs v3 on ubuntu 18.04

```shell
# deploy first
sudo apt-get update   # update package
sudo apt-get install -y nfs-kernel-server  # install nfs packages
sudo mkdir /opt/sfw   # create shared directory
sudo chmod 1777 /opt/sfw   # change to executable mode
sudo bash -c 'echo software > /opt/sfw/hello.txt'  # create a dummy file
#Add a line in /etc/exports  
sudo vi /etc/exports
/opt/sfw/  *(rw,sync,no_root_squash,subtree_check)
sudo exportfs -ra   # reload for the change takes effect
```

## Install nfs on the node1
```shell
sudo apt-get update 
sudo apt-get -y install nfs-common  # add  nfs client package
showmount -e <master_address_ip>   # check whether the shared mount works
sudo mkdir -p /nfs/general # create a directory
sudo mount 10.128.0.15:/opt/sfw /nfs/general # mapping of nfs share to mountpoint directory
```