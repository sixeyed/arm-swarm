
On pine64-04 / pine64-05

# Direct disk access 

## HDD - usb2tb - xfs

```
dd if=/dev/zero of=/gluster/deleteme1.img bs=1M count=1024 conv=fsync && rm /gluster/deleteme1.img
```

-05
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 28.4829 s, 37.7 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 28.7655 s, 37.3 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 28.6437 s, 37.5 MB/s

-04
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 29.3086 s, 36.6 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 28.6483 s, 37.5 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 28.506 s, 37.7 MB/s

> Average 37.5MB/s, close to practical maximum for USB2

## Flash - usb64gb - xfs

```
dd if=/dev/zero of=/gluster2/deleteme1.img bs=1M count=1024 conv=fsync && rm /gluster2/deleteme1.img
```

-05 
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 87.553 s, 12.3 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 117.56 s, 9.1 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 108.669 s, 9.9 MB/s

-04
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 104.129 s, 10.3 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 126.236 s, 8.5 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 116.96 s, 9.2 MB/s

> Average <10MB/s, miserable

# GFS-Fuse local

## Distributed-replicated 2+arbiter

```
dd if=/dev/zero of=/mnt/gfs/deleteme1.img bs=1M count=1024 conv=fsync && rm /mnt/gfs/deleteme1.img
```

-05
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 33.2355 s, 32.3 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 33.7254 s, 31.8 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 33.6427 s, 31.9 MB/s

-04
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 40.5811 s, 26.5 MB/s < outlier
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 33.1036 s, 32.4 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 33.1011 s, 32.4 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 33.3715 s, 32.2 MB/s

> (minus outlier) Average 32MB/s - loss of 5.5MB/s compared to direct write

## Distributed 2

```
dd if=/dev/zero of=/mnt/gfs-distributed/deleteme1.img bs=1M count=1024 conv=fsync && rm /mnt/gfs-distributed/deleteme1.img
```

-05
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.8892 s, 34.8 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.4827 s, 35.2 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.3316 s, 35.4 MB/s

-04
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.4276 s, 35.3 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 29.8388 s, 36.0 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 29.794 s, 36.0 MB/s

> Average 35.5MB/s - loss of 2MB/s compared to direct


## Dispersed 3x1

```
dd if=/dev/zero of=/mnt/gfs-dispersed/deleteme1.img bs=1M count=1024 conv=fsync && rm /mnt/gfs-dispersed/deleteme1.img
```

-05
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 29.6478 s, 36.2 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.2897 s, 35.4 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.4598 s, 35.3 MB/s

-04
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.7714 s, 34.9 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 31.4496 s, 34.1 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.1125 s, 35.7 MB/s

> Average 35MB/s - loss of 2.5MB/s compared to direct; comparable to distributed, 5MB/s faster than distributed-replicated


## Client - up-ub1604

Remote

### Distributed-replicated

```
sudo dd if=/dev/zero of=/mnt/gfs/deleteme1.img bs=1M count=1024 conv=fsync && sudo rm /mnt/gfs/deleteme1.img
```

1073741824 bytes (1.1 GB, 1.0 GiB) copied, 73.3457 s, 14.6 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 79.9939 s, 13.4 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 78.3057 s, 13.7 MB/s

> Average 14MB/s, 18MB/s slower than local, 23.5MB/s slower than direct

### Distributed

```
sudo dd if=/dev/zero of=/mnt/gfs-distributed/deleteme1.img bs=1M count=1024 conv=fsync && sudo rm /mnt/gfs-distributed/deleteme1.img
```

1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.5866 s, 35.1 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 31.0429 s, 34.6 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 30.1678 s, 35.6 MB/s

> Average 35MB/s, 0.5MB/s slower than local, 2.5MB/s slower than direct

### Dispersed

```
sudo dd if=/dev/zero of=/mnt/gfs-dispersed/deleteme1.img bs=1M count=1024 conv=fsync && sudo rm /mnt/gfs-dispersed/deleteme1.img
```
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 55.8854 s, 19.2 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 54.3594 s, 19.8 MB/s
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 55.0845 s, 19.5 MB/s

> Average 19.5MB/s, 15.5MB/s slower than local, 18MB/s slower than direct


# Realistic - small files - expand jenkins.war - containerized

## Remote - on up-ub1604

```
docker container run -it --rm --entrypoint sh `
 -v /mnt/gfs-class1:/gfs-class1 `
 -v /mnt/gfs-class2:/gfs-class2 `
 -v /mnt/gfs-class3:/gfs-class3 `
 --user root:root jenkins/jenkins:2.167-alpine
```

### Local disk (eMMC)

```
mkdir -p /tmp/war && cd /tmp/war && time jar -xvf /usr/share/jenkins/jenkins.war && cd / && time rm -rf /tmp/war
```

- 5.2s / 0.1s
- 4.9s / 0.1s

### Class 3 - distributed x4

```
mkdir -p /gfs-class3/war && cd /gfs-class3/war && time jar -xvf /usr/share/jenkins/jenkins.war && cd / && time rm -rf /gfs-class3/war
```
- 1m 7s / 7.1s
- 1m 8s / 8.6s

### Class 2 - dispersed x 3+1

```
mkdir -p /gfs-class2/war && cd /gfs-class2/war && time jar -xvf /usr/share/jenkins/jenkins.war && cd / && time rm -rf /gfs-class2/war
```

- 1m 16s / 9.4s
- / 

### Class 1 Distributed-replicated 2x+1

```
mkdir -p /gfs-class1/war && cd /gfs-class1/war && time jar -xvf /usr/share/jenkins/jenkins.war && cd / && time rm -rf /gfs-class1/war
```

- Failed after 19m 48s! 
- /


## Local - on pine64-04

```
docker container run -it --rm --entrypoint sh `
 -v /mnt/gfs-class1:/gfs-class1 `
 -v /mnt/gfs-class2:/gfs-class2 `
 -v /mnt/gfs-class3:/gfs-class3 `
 --user root:root sixeyed/jenkins:2.150.3-jdk-alpine-arm64
```

### Local disk (SD)

```
mkdir -p /tmp/war && cd /tmp/war && time jar -xvf /jenkins/jenkins.war && cd / && time rm -rf /tmp/war
```

- 17.1s / 0.2s
- 11.8s / 0.2s

### Class 3 - distributed x4

```
mkdir -p /gfs-class3/war && cd /gfs-class3/war && time jar -xvf /jenkins/jenkins.war && cd / && time rm -rf /gfs-class3/war
```
- 1m 2s / 5.9s
- 1m 2s / 5.8s

### Class 2 - dispersed x 3+1

```
mkdir -p /gfs-class2/war && cd /gfs-class2/war && time jar -xvf /jenkins/jenkins.war && cd / && time rm -rf /gfs-class2/war
```

- 1m 45s / 5.8s 
- 1m 45s / 6.0s 

### Class 1 Distributed-replicated 2x+1

```
mkdir -p /gfs-class1/war && cd /gfs-class1/war && time jar -xvf /jenkins/jenkins.war && cd / && time rm -rf /gfs-class1/war
```

- 1m 22s / 10.2s
- 1m 23s / 10.5s