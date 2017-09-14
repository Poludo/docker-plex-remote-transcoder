FROM plexinc/pms-docker:latest

# Set environment variables
ENV S6_BEHAVIOUR_IF_STAGE2_FAILS=2

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
	curl -o /tmp/prt-wnielson.zip -L "https://github.com/wnielson/Plex-Remote-Transcoder/archive/master.zip" && \
	curl -o /tmp/prt-JJK801.zip -L "https://github.com/JJK801/Plex-Remote-Transcoder/archive/master.zip" && \
	mkdir -p /app && \
	unzip /tmp/prt-wnielson.zip -d /app/prt-wnielson && \
	unzip /tmp/prt-JJK801.zip -d /app/prt-JJK801 && \
	
# Plex-Remote-Transcoder configuration
	ln -s /config/Library /var/lib/plexmediaserver/Library && \
	
# NFS Server configuration
	echo "nfs             2049/tcp" >> /etc/services && \
	
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

EXPOSE 22 2049/tcp
