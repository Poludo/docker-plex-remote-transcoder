# Docker container for Plex-Remote-Transcoder
This repository contains a set of Docker containers that make the set up of Plex-Remote-Transcoder, a distributed transcoding backend for Plex, easy and fast.

## Master container
Official Plex Docker container on which has been added a bunch of stuff to make it compatible with slave containers running remote transcoders:
- Based on the official Plex Docker image
- Plex-Remote-Transcoder installed and set up to be run as a master node
- Bundled with an SSH server & client for automatic interaction with slave containers
- Bundled with an NFS server for sharing of required Plex files to slave containers

## Slave container
Container on which is running a Plex-Remote-Transcoder client, it will automatically connect to the master container and register itself to it when started.

# Usage

## Quick start
1. Run the master container: it will automatically generate an SSH key pair in the config directory
2. Copy the "/config/.ssh" directory from the master container to the slave container (by keeping the same path)
3. Run the slave container
4. Enjoy ;)

## Master container
You have to configure it like the official Plex Docker container: https://github.com/plexinc/pms-docker#bridge-networking with some additional configuration:
```
docker create \
  --name=plex \
  --privileged \
	< ... > \
	-p <random available port for ssh>:22 \
	-p <random available port for nfs>:2049/tcp \
  poludo/plex-remote-transcoder:master
```

## Slave container
```
docker create \
  --name=plex-remote-transcoder \
  --privileged \
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

# Optional parameters

## Master container
- `-e USE_MASTER_TRANSCODER=true` Registers the master container as an available Plex transcoder. By default, the master container cannot be used for transcoding unless no slaves are available.
- `-e DISTRIBUTE_SINGLE_TRANSCODES=true` Runs a single transcoding job on multiple hosts. By default, chunks are only distributed to a single host. Please note that the use of this parameter requires the restart of all slave containers.

## Slave container
- `-e IMPORT_PLEX_MEDIA=false` Disables the import of the Plex media library from the master container over NFS. Can be useful when the media library is accessible by mounting it on the host with [Rclone](https://github.com/ncw/rclone) or [Plexdrive](https://github.com/dweidenfeld/plexdrive). Please note that the media library has to be stored in the "/data" directory of the container. Be also sure to check that the content and the (recursive) permissions of this directory are the same in the master and in the slave containers.
