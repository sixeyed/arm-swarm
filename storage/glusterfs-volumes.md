check

```
gluster peer status
```

# Create volumes

3 volumes with different redundancy:

- class1 - most redundant, use for critical data (good perf for large objects)
- class2 - some redundancy, use for app state (okay perf for small objects)
- class3 - no redundancy, use for transient data (slightly better perf for small objects)

## Class1 - Distributed replicated - high utilization & failover

create volume - replica 3 arbiter 1
#https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.3/html/administration_guide/creating_arbitrated_replicated_volumes

> Ensure /gluster mounted from correct disk; no need to create subdirectories

```
gluster volume create gfs-class1 replica 3 arbiter 1 \
 pine64-04.sixeyed:/gluster/brick-class1 \
 pine64-06.sixeyed:/gluster/brick-class1 \
 pine64-01.sixeyed:/gluster/aribiter-class1 \
 pine64-07.sixeyed:/gluster/brick-class1 \
 pine64-05.sixeyed:/gluster/brick-class1 \
 pine64-02.sixeyed:/gluster/aribiter-class1 \
 force

gluster volume start gfs-class1
```

## Class2 - Dispersed - 3x data, 1x redundancy - high utilization, no failover

create volume

```
gluster volume create gfs-class2 disperse \
 pine64-04.sixeyed:/gluster/brick-class2 \
 pine64-05.sixeyed:/gluster/brick-class2 \
 pine64-06.sixeyed:/gluster/brick-class2 \
 pine64-07.sixeyed:/gluster/brick-class2 
 
gluster volume start gfs-class2
```

## Class3 - Distributed - high utilization, no failover

create volume

```
gluster volume create gfs-class3 \
 pine64-04.sixeyed:/gluster/brick-class3 \
 pine64-05.sixeyed:/gluster/brick-class3 \
 pine64-06.sixeyed:/gluster/brick-class3 \
 pine64-07.sixeyed:/gluster/brick-class3 
 
gluster volume start gfs-class3
```

# Mounts

Create client mountpoints (all servers & clients):

```
mkdir -p /mnt/gfs-class1 /mnt/gfs-class2 /mnt/gfs-class3
```

mount (-04 to -07)

```
echo 'localhost:/gfs-class1 /mnt/gfs-class1 glusterfs defaults,_netdev,backupvolfile-server=pine64-01.sixeyed 0 0' >> /etc/fstab
echo 'localhost:/gfs-class2 /mnt/gfs-class2 glusterfs defaults,_netdev,backupvolfile-server=pine64-01.sixeyed 0 0' >> /etc/fstab
echo 'localhost:/gfs-class3 /mnt/gfs-class3 glusterfs defaults,_netdev,backupvolfile-server=pine64-01.sixeyed 0 0' >> /etc/fstab
mount -a
```

mount (clients - ub-ub1604, linux-01 etc)

```
echo 'pine64-04.sixeyed:/gfs-class1 /mnt/gfs-class1 glusterfs defaults,_netdev,backupvolfile-server=pine64-01.sixeyed 0 0' >> /etc/fstab
echo 'pine64-04.sixeyed:/gfs-class2 /mnt/gfs-class2 glusterfs defaults,_netdev,backupvolfile-server=pine64-01.sixeyed 0 0' >> /etc/fstab
echo 'pine64-04.sixeyed:/gfs-class3 /mnt/gfs-class3 glusterfs defaults,_netdev,backupvolfile-server=pine64-01.sixeyed 0 0' >> /etc/fstab
mount -a
```