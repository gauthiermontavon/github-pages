---
title: Deploying web app to Google Cloud Platform
date : 2024-11-07
---


# Introduction
Since I use many tools from Google ecosystem for my personal organisation and daily routines, I have been thinking about testing Google Cloud Platform for some time. Now that I have a concrete idea for a web app project, it is time to implement and deploy it to a web server hosted in the cloud of Google.

# VM Creation
Here are just few steps for which you can easily find details by googling them.
- create VM from Compute Engine "template"
- connect to VM and install web server (I chosed Apache)

In few simple operations you get an web server online with a default index.html page.
Next step for me, I want to deploy my website (it's not ready but anyway) inside it. 

# Website deployment
In my case, here is the deploying scenario I want to implement:
- Push website to github repo
- Build a release for this repo
- Enable a github Action that copies the files to my VM's web server when release is build.

## Pushing website to github repo
No needs to document this step

## build a release for the github repo

## Github actions
