# Project Documentation: Jenkins and Tomcat Setup for Application Deployment

## Overview
This documentation outlines the steps to install Jenkins, configure Maven in Jenkins, and deploy a Java application to a Tomcat server. It covers installing required dependencies, configuring Jenkins for building Java applications, and setting up Tomcat for hosting the deployed application.

## Prerequisites
- A Red Hat-based Linux system (e.g., CentOS, RHEL).
- Root or sudo privileges.
- A running instance of Jenkins and Tomcat.

## Step-by-Step Guide

### 1. Installing Java 17
First, install Java 17, which is required for running Jenkins and Maven.

```bash
yum install java-17* -y
```

### 2. Installing Git
Next, install Git, which will be used for fetching the source code repositories.
```bash
yum install git -y
```

### 3. Installing Jenkins
- To install Jenkins on a Red Hat-based system, prefer jenkins installation guide for Red Hat [Jenkins Installation page](https://www.jenkins.io/doc/book/installing/linux/#long-term-support-release-3)
```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum upgrade
# Add required dependencies for the jenkins package
sudo yum install fontconfig java-17-openjdk
sudo yum install jenkins
sudo systemctl daemon-reload
```
- Enable and Start Jenkins
  Enable Jenkins to start on boot, and start the Jenkins service immediately.
  ```bash
  systemctl enable jenkins
  systemctl start jenkins
  ```
- Verify Jenkins Installation
  Jenkins typically runs on port 8080. Ensure it is accessible by checking the connection at http://<your-server-ip>:8080.

