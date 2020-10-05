FROM debian:buster-slim
RUN apt-get update && apt-get install busybox wget gnupg2 -y && \
wget -q https://ftp-master.debian.org/keys/release-10.asc -O- | apt-key add - && \
echo "deb http://deb.debian.org/debian buster non-free" >> /etc/apt/sources.list && \
apt-get update && \
apt-get install libttspico0 libttspico-utils -y

