---
title: "How to Run Any Application as Service on Ubuntu"
date: 2024-01-01T18:00:00+06:00
description: "Easy start, stop or monitor a Service"
tags: [deployment, ubuntu, shell, logging]
---
This guide is created to start, stop or monitor any service that can be run with shell on Ubuntu. Here in this example we run a Java application, see its status, and stop it when needed. We also use `journalctl` to monitor its recent activities.

1. Create a service config file
```
sudo nano /etc/systemd/system/your-app.service
```

2. Edit `your-app.service`
```
[Unit]
Description=Your App Service
[Service]
# If you want to run this service as root
User=root
# change this to your workspace
WorkingDirectory=/home/ubuntu/workspace
# path to executable. 
# executable is a bash script which runs app 
ExecStart=/home/ubuntu/workspace/your-app-exec
SuccessExitStatus=143
TimeoutStopSec=10
Restart=on-failure
RestartSec=5
[Install]
WantedBy=multi-user.target
```

3. Create bash script in your WorkingDirectory directory to run your app
```
sudo nano your-app-exec
```

4. Edit bash script `your-app-exec`
```
#!/bin/sh
sudo java -jar your-app.jar
```

5. Give your bash permission
```
sudo chmod u+x your-app-exec
```

6. Start/stop Service
```
sudo systemctl daemon-reload
sudo systemctl enable your-app.service
sudo systemctl start your-app
sudo systemctl status your-app
sudo systemctl stop your-app
```

> Always use `sudo systemctl daemon-reload` when you modify any service configuration

7. Setup Log
```
sudo journalctl --unit=your-app
```

8. Get log
```
sudo journalctl -f -n 300 -u your-app
```
