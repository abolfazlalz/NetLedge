In one of my projects, instead of using Docker, I used Systemctl to deploy my project.

This is not a very good idea to deploy a project with Docker in mind.
But it had an interesting method.

## Create the service file on Systemd

Create a service file on **/etc/systemd/system/** or **/lib/systemd/system/** (etc|lib).

```bash
cd /etc/systemd/system/
nano myserver.service
```

File location: **/etc/systemd/system/myserver.service**

```service
[Unit]  
Description=My Little Server  
# Documentation=[https://](https://gnnc.com.br/)  
# Author: Natan Cabral[Service]  
# Start Service and Examples  
ExecStart=/usr/local/bin/node **/home/myserver/server.js  
**# ExecStart=/usr/bin/sudo /usr/bin/node **/home/myserver/server.js**  
# ExecStart=/usr/local/bin/node /var/www/project/myserver/server.js# Options Stop and Restart  
# ExecStop=  
# ExecReload=# Required on some systems  
# WorkingDirectory=**/home/myserver/**  
# WorkingDirectory=/var/www/myproject/# Restart service after 10 seconds if node service crashes  
RestartSec=10  
Restart=always  
# Restart=on-failure# Output to syslog  
StandardOutput=syslog  
StandardError=syslog  
SyslogIdentifier=nodejs-my-server-example# #### please, not root users  
# RHEL/Fedora uses 'nobody'  
# User=nouser  
# Debian/Ubuntu uses 'nogroup', RHEL/Fedora uses 'nobody'  
# Group=nogroup# variables  
Environment=PATH=/usr/bin:/usr/local/bin  
# Environment=NODE_ENV=production  
# Environment=NODE_PORT=3001  
# Environment="SECRET=pGNqduRFkB4K9C2vijOmUDa2kPtUhArN"  
# Environment="ANOTHER_SECRET=JP8YLOc2bsNlrGuD6LVTq7L36obpjzxd"[Install]  
WantedBy=multi-user.target
```

### Enable the service

**Enable** the service on boot

```bash
systemctl enable myserver.service
```

**Start** the service
```bash
systemctl start myserver.service  
systemctl restart myserver.service  
systemctl status myserver.service
```

**Verify it** is running

```bash
journalctl -u myserver.service
```

**Reload all services** if you make changes to the service

```bash
systemctl daemon-reload  
systemctl restart myserver.service
```

List process

```bash
ps -ef | grep myserver.js
```



