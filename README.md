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
- Enable and Start Jenkins<br>
  Enable Jenkins to start on boot, and start the Jenkins service immediately.
  ```bash
  systemctl enable jenkins
  systemctl start jenkins
  ```
- Verify Jenkins Installation<br>
  Jenkins typically runs on port 8080. Ensure it is accessible by checking the connection at `http://<your-server-ip>:8080`.
- Retrieve Jenkins Admin Password<br>
  The initial admin password for Jenkins is stored in the following location. Retrieve it using the command below:
  ```bash
  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
  ```
### 4. Install Maven Plugin in Jenkins
To build Java projects with Maven, you must install the Maven plugin in Jenkins.
- Log in to Jenkins.
- Navigate to **Manage Jenkins** → **Tools** → **Maven Installation**.
- Click **Add Maven**, provide a name (e.g., `Name_Maven`), and **save**.

### 5. Create and Configure a Jenkins Job
a. **Create a New Job**
- On the Jenkins dashboard, click **New Item**.
- Enter the name `app_deploy`, choose **Freestyle project**, and click **OK**.
b. **Configure the Job**
- In General:
  - Under **Source Code Management**, select **Git** and *provide the repository URL*.
  - Click **Apply & Save**.
- Under **Build Triggers**, you can set up triggers such as SCM polling or GitHub webhooks.
- Under **Build Steps**, click **Add build step → Invoke top-level Maven targets**.
  - Set **Maven Version** to the version installed (e.g., `Maven`).
  - Set **Goals** to `clean package`.
  - Click **Apply & Save**.
- Under **Post-build Actions**, you can add further steps like archiving build artifacts or sending notifications.

### 6. Install and Configure Tomcat
a. **Download and Extract Tomcat**
- Download the Apache Tomcat tarball and extract it.
  ```bash
  wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.97/bin/apache-tomcat-9.0.97.tar.gz
  tar -zxvf apache-tomcat-9.0.97.tar.gz
  ```
b. **Modify Tomcat Configuration Files**
```bash
cd apache-tomcat-9.0.97/conf/
vi tomcat-users.xml
vi server.xml
```
- **tomcat-users.xml**: Add the necessary user roles for accessing the Tomcat manager.
  ```xml
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>
  <user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
  ```
- **server.xml**: Configure Tomcat to listen on port 8081 (or any preferred port).
  ```xml
  <Connector port="8081" protocol="HTTP/1.1"
             connectionTimeout="20000"
             redirectPort="8443"
             maxParameterCount="1000"/>
  ```
  - **context.xml**: Make any additional configurations if necessary (e.g., set cookie processors, etc.). `vi webapps/manager/META-INF/context.xml`
    ```xml
    <Context antiResourceLocking="false" privileged="true" >
        <!--  <CookieProcessor className="org.apache.tomcat.util.http.Rfc6265CookieProcessor"
                   sameSiteCookies="strict" />
    <Valve className="org.apache.catalina.valves.RemoteAddrValve"
        allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
    ```
c. **Start Tomcat**
- Start the Tomcat server using the `startup.sh` script.
  ```bash
  cd apache-tomcat-9.0.97/bin/
  sh startup.sh
  ```
d. **Verify Tomcat Access**
- You can check the connection by visiting `http://<your-server-ip>:8081`. Log in with the credentials `UN:admin Password:admin` to access the Tomcat Manager.
  
### 7. Jenkins Deployment to Tomcat
a. **Install "Deploy to Container" Plugin in Jenkins**
- Go to **Manage Jenkins → Manage Plugins → Available**.
- Search for the `Deploy to Container` plugin and **install** it.
b. **Configure Deployment in Jenkins**
- In your Jenkins Job `app_deploy`, under **Post Build Actions**, select **Deploy WAR/EAR to a container**.
- Set the Following
  - **WAR/EAR files**: `**/*.war`
  - **Context Path**: Set this to the `artifact ID`.
  - **Container**: `Choose Tomcat 9.x`.
  - **Credentials**: Add Jenkins credentials for accessing Tomcat (use `admin` as username and `admin` as password).
  - **Tomcat URL**: Provide the URL to your Tomcat server, e.g., `http://65.1.100.203:8081`.
c. **Click *Apply & Save*, then click *Build Now* to trigger the deployment.**

### 8. Verify Deployment
- After the build and deployment process completes, you can verify the deployment by accessing the Tomcat Manager at:
  ```arduino
  http://65.1.100.203:8081/manager/html
  ```
  - Here, you can manage your deployed applications.

## Conclusion
By following these steps, we have successfully installed Jenkins, configured Maven, and deployed a Java application to a Tomcat server. This setup enables automated building and deployment of Java applications using Jenkins and Tomcat.
  













