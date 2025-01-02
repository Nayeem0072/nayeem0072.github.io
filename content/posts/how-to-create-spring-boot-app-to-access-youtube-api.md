---
title: "How to Create a Spring Boot App to Access Youtube API"
date: 2024-04-06T18:00:00+06:00
description: "Automate acess Youtube API"
tags: [deployment, spring-boot, java]
draft: true
---
In today's digital world, YouTube is a goldmine of videos, data, and insights, providing vast resources for businesses and developers alike. Whether you're building a content recommendation system, a video library, or simply need access to metadata from YouTube, the YouTube Data API offers powerful capabilities to interact with the platform programmatically.

Spring Boot, known for its simplicity in building production-ready applications, is an excellent framework for creating RESTful applications to interact with external services like the YouTube API. In this tutorial, we will walk through how to build a Spring Boot application that consumes the YouTube Data API to fetch channel information using Gcloud Youtube Data API v3 and Oauth integration. By the end, you’ll have a robust solution for integrating YouTube data into your Java applications, making it easier to unlock the full potential of YouTube’s vast resources.

Let’s get started!

## Create a Gcloud Project

When you use your spring boot service to access Youtube API, you need to use a Gcloud's Youtube Data API. The reason is that in order to access Youtube API to fetch data from a Youtube channel, Youtube needs to authenticate the owner of that channel. This is a standard Google authentication process, which cannot be automated to access from a service in general, because a backend service cannot authenticate using this process. For this reason, we will use GCloud, which will use a Oauth2 token to authenticate the owner of this channel, and also will use a Gcloud token to connect this service and the channel owner together.

First step of this is to create a Gcloud project, assuming you already have a Gcloud account. In order to create a Gcloud project, you can follow [this](https://cloud.google.com/resource-manager/docs/creating-managing-projects#creating_a_project).

## Enable Youtube Data API v3 from Gcloud

You can Search for `YouTube Data API v3` in the search box of Gcloud and enable the API.

## Enable OAuth 2.0

In order to enable OAuth 2.0 authentication, go `Navigation Menu` >> `APIs and Services` >> `Credentials`.

In the `Credentials` section, click `+ Create Crdentials` button and select `OAuth Client ID`. A new page will be opened.

* Select `Web Application` for the application type. 
* Put a name to identify your service. 
* Put `http://localhost:8080` in the Authorized JavaScript origins URL. Don't worry if you don't use this URL, this is not important. We are not going to use it.
* Enter `https://developers.google.com/oauthplayground` in the Authorized redirect URIs field. This is super important, and you will understand it in the following sections. 
* Save the configuation.

Now back to the previous `Credentials` page. Click on the download button of the newly created OAuth 2.0 Client ID and download the JSON version of it. 



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
