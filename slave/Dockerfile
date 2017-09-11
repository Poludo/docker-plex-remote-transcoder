FROM ubuntu:latest

# Set version for s6 overlay
ARG S6_OVERLAY_VERSION=v1.20.0.0
ARG PLATFORM_ARCH="amd64"

# Set environment variables
ENV TERM="xterm" \
	LANG="C.UTF-8" \
	LC_ALL="C.UTF-8" \
	S6_BEHAVIOUR_IF_STAGE2_FAILS=2

ARG DEBIAN_FRONTEND="noninteractive"

RUN \
# Install packages
	buildDeps=" \
		curl \
		unzip" && \
	apt-get update && \
	apt-get install -y --no-install-recommends --no-install-suggests \
		${buildDeps} \
		autofs \
		ca-certificates \
		nfs-common \
		openssh-client \
		openssh-server \
		python-psutil \
		python-setuptools && \

# Add S6 overlay
    curl -J -L "https://github.com/just-containers/s6-overlay/releases/download/${S6_OVERLAY_VERSION}/s6-overlay-${PLATFORM_ARCH}.tar.gz" \
    | tar xz -C / && \
	
# Add Plex-Remote-Transcoder
	curl -o /tmp/prt.zip -L "https://github.com/wnielson/Plex-Remote-Transcoder/archive/master.zip" && \
	unzip /tmp/prt.zip -d /tmp && \
	rm -f /tmp/prt.zip && \
	cd /tmp/Plex-Remote-Transcoder-master && \
	python setup.py install && \
	cd / && \
	rm -rf /tmp/Plex-Remote-Transcoder-master && \
	
# Add user
    useradd -U -d /config -s /bin/sh plex && \
    usermod -G users plex && \
	
# Configure Autofs
	echo "/- /etc/auto.nfsprt" >> /etc/auto.master && \
	
# Configure SSHD
	mkdir /var/run/sshd && \
	sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
	sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \

# Setup directories
    mkdir -p /config && \
	
# Cleaning
	rm -rf /etc/services.d/plex
	apt-get -y --purge autoremove ${buildDeps} && \
	rm -rf \
		/tmp/* \
		/var/lib/apt/lists/* \
		/var/tmp/*

COPY root/ /
ENTRYPOINT ["/init"]
VOLUME /config 
EXPOSE 22