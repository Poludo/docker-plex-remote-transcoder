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
# Configure Plex-Remote-Transcoder
	mkdir -p /var/lib/plexmediaserver && \
	ln -s /config/Library /var/lib/plexmediaserver/Library && \
# Configure NFS Server
	echo "nfs             2049/tcp" >> /etc/services && \
	mkdir -p \
		/exports/plex_bin.d \
		/exports/plex_conf.d \
		/exports/plex_media.d \
		/exports/plex_transcode.d && \
# Configure SSHD
	mkdir /var/run/sshd && \
# Configure user
	sed -i -e 's;/config:/bin/false;/config:/bin/bash;g' /etc/passwd && \
# Clean
	apt-get -y --purge autoremove ${buildDeps} && \
	rm -rf \
		/tmp/* \
		/var/lib/apt/lists/* \
		/var/tmp/*

COPY root/ /
VOLUME /data /usr/lib/plexmediaserver /exports
EXPOSE 22 2049/tcp
