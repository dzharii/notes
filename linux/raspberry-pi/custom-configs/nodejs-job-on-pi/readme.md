```
echo "create initial directories"
sudo mkdir -p /opt/click-kss-eventgen

echo "Make a eventgen system user"
sudo adduser --system --no-create-home --disabled-password --disabled-login eventgen

echo "ensure permissions"
# sudo chown -R eventgen:nogroup /opt/click-kss-eventgen

curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

sudo apt-get install -y gcc g++ make
sudo apt-get install -y nodejs
sudo apt-get install -y supervisor

```


```
echo  '; supervisor config file

[unix_http_server]
file=/var/run/supervisor.sock   ; (the path to the socket file)
chmod=0700                       ; sockef file mode (default 0700)

[inet_http_server]
port=0.0.0.0:9001
username=admin
password=admin

[supervisord]
logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
childlogdir=/var/log/supervisor            ; ('AUTO' child log dir, default $TEMP)

; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket

; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.

[include]
files = /etc/supervisor/conf.d/*.conf
' | sudo tee /etc/supervisor/supervisord.conf


```

https://blog.risingstack.com/operating-node-in-production/


```
echo '[program:eventgen-stage]
command=node  /opt/click-kss-eventgen/dist/index.js stage
autostart=true
autorestart=true
;environment=NODE_ENV=production
stderr_logfile=/var/log/eventgen-stage.err.log
stdout_logfile=/var/log/eventgen-stage.out.log
user=pi
' | sudo tee /etc/supervisor/conf.d/eventgen-stage.conf

```

```
echo '[program:eventgen-prod]
command=node  /opt/click-kss-eventgen/dist/index.js prod
autostart=true
autorestart=true
;environment=NODE_ENV=production
stderr_logfile=/var/log/eventgen-prod.err.log
stdout_logfile=/var/log/eventgen-prod.out.log
user=pi
' | sudo tee /etc/supervisor/conf.d/eventgen-prod.conf

```


```
sudo supervisorctl reread
sudo supervisorctl update
```