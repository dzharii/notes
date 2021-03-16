
# Disable apt-daily.service

```sh
# Disable the systemd task
# https://unix.stackexchange.com/questions/315502/how-to-disable-apt-daily-service-on-ubuntu-cloud-vm-image
systemctl stop apt-daily.timer
systemctl disable apt-daily.timer
systemctl mask apt-daily.service
systemctl daemon-reload


# Disable config option APT::Periodic::Enable
cat > /etc/apt/apt.conf.d/99elasticluster <<__EOF
APT::Periodic::Enable "0";
// undo what's in 20auto-upgrade
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Unattended-Upgrade "0";
__EOF

apt-config shell AutoAptEnable APT::Periodic::Enable
# AutoAptEnable='0'
mv /usr/lib/apt/apt.systemd.daily /usr/lib/apt/apt.systemd.daily.DISABLED
```