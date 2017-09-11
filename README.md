# Docker container for Plex-Remote-Transcoder
This repository contains a set of Docker containers that make the set up of Plex-Remote-Transcoder, a distributed transcoding backend for Plex, easy and fast.

### Master container: 
Official Plex Docker container on which has been added a bunch of stuff to make it compatible with slave containers running remote transcoders:
- Based on the official Plex Docker image
- Plex-Remote-Transcoder installed and set up to be run as a master node
- Bundled with an SSH server & client for automatic interaction with slave containers
- Bundled with an NFS server for sharing of required Plex files to slave containers

### Slave container:
Container on which is running a Plex-Remote-Transcoder client, it will automatically connect to the master container and register itself to it when started.

# Usage

### Quick start
1. Run the master container: it will automatically generate an SSH key pair in the config directory
2. Copy the "/config/.ssh" directory from the master container to the slave container (by keeping the same path)
3. Run the slave container
4. Enjoy ;)

### Master container
You have to configure it like the official Plex Docker container: https://github.com/plexinc/pms-docker#bridge-networking with some additional configuration:
```
docker create \
  --name=plex \
	< ... > \
	-p <random available port for ssh>:22 \
	-p <random available port for nfs>:2049/tcp \
  poludo/plex-remote-transcoder:master
```

### Slave container
Note: The content and the (recursive) permissions of the "/data" directory has to be the same for the master and the slave containers.
```
docker create \
  --name=plex-remote-transcoder \
	-v /etc/localtime:/etc/localtime:ro \
	-v <path to config>:/config \
	-v <path to media>:/data \
	-p <random available port for ssh>:22 \
	-e SLAVE_SSH_PORT=<ssh port specified above> \
	-e MASTER_IP=<ip of the host running the master container> \
	-e MASTER_NFS_PORT=<master container nfs port> \
	-e MASTER_SSH_PORT=<master container ssh port> \
  poludo/plex-remote-transcoder:slave
```
