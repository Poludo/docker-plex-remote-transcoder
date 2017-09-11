FROM plexinc/pms-docker:latest

RUN \
# Install packages
	buildDeps=" \
		curl \
		unzip" && \
	apt-get update && \
	apt-get install -y --no-install-recommends --no-install-suggests \
		${buildDeps} \
		ca-certificates \
		inotify-tools \
		jq \
		nfs-kernel-server \
		openssh-client \
		openssh-server \
		python-psutil \
		python-setuptools && \
	
# Add Plex-Remote-Transcoder
	curl -o /tmp/prt.zip -L "https://github.com/wnielson/Plex-Remote-Transcoder/archive/master.zip" && \
	unzip /tmp/prt.zip -d /tmp && \
	rm -f /tmp/prt.zip && \
	cd /tmp/Plex-Remote-Transcoder-master && \
	python setup.py install && \
	cd / && \
	rm -rf /tmp/Plex-Remote-Transcoder-master && \
	
# Plex-Remote-Transcoder configuration
	ln -s /config/Library /var/lib/plexmediaserver/Library && \
	
# NFS Server configuration
	echo "nfs             2049/tcp" >> /etc/services && \
	echo "nfs             111/udp" >> /etc/services && \
	
# SSHD configuration
	mkdir /var/run/sshd && \
	sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
	sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \
	printf "StrictHostKeyChecking no\n" >> /etc/ssh/ssh_config && \

# User configuration
	sed -i -e 's;/config:/bin/false;/config:/bin/bash;g' /etc/passwd && \
	
# Cleaning
	apt-get -y --purge autoremove ${buildDeps} && \
	rm -rf \
		/tmp/* \
		/var/lib/apt/lists/* \
		/var/tmp/*

COPY root/ /

EXPOSE 22 111/udp 2049/tcp
