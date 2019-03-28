https://blog.ruanbekker.com/blog/2019/03/05/setup-a-3-node-replicated-storage-volume-with-glusterfs/?referral=github.com

https://gluster.readthedocs.io/en/latest/Quick-Start-Guide/Architecture/#types-of-volumes


Install 

```
wget -O - https://download.gluster.org/pub/gluster/glusterfs/5/rsa.pub | apt-key add -

echo deb https://download.gluster.org/pub/gluster/glusterfs/5/5.3/Debian/stretch/arm64/apt stretch main > /etc/apt/sources.list.d/gluster.list

apt-get update

apt-get install --no-install-recommends xfsprogs glusterfs-server

service glusterd start
```


prep drive - storage nodes

```
device=sda
drive=/dev/$device
parted -a optimal $drive

> p
> mktable gpt
#> mkpart primary xfs 0% 2000GB
#> mkpart primary xfs 0% 8GB
> quit

mkfs.xfs $drive\1 -f  #parted doesn't seem to make a good xfs

mkdir /gluster 
echo $drive\1 /gluster xfs defaults 0 0  >> /etc/fstab

mount -a

#mkdir -p /gluster/brick 
#mkdir -p /gluster/arbiter 
```

prep drive - arbiter nodes (1 & 2)

```
```


probe nodes to add as peers (from -04)

```
gluster peer probe pine64-05.sixeyed
gluster peer probe pine64-06.sixeyed
gluster peer probe pine64-07.sixeyed
gluster peer probe pine64-01.sixeyed
gluster peer probe pine64-02.sixeyed

```

check

```
gluster peer status
```
