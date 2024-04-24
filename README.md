# **Install Sonarqube in Ubuntu Linux**

## **Prerequisites**

If Ubuntu isn't your primary operating system, you can opt to use a virtual machine instead. However, remember that SonarQube supports Ubuntu 22.04 and newer versions. Ensure that you have a clean installation of Ubuntu Linux.

## **Verify or Install Java 17**

Before installing SonarQube, you need to ensure that Java 17 is installed on your system. Open a terminal and follow these steps to verify or install Java 17:

1. **Check Java Version:**
    
    ```bash
    java -version
    ```
    
    If Java 17 is installed, you should see output similar to the following:
    
    ```
    openjdk 17.0.10 2024-01-16
    OpenJDK Runtime Environment (build 17.0.10+7-Ubuntu-123.10.1)
    OpenJDK 64-Bit Server VM (build 17.0.10+7-Ubuntu-123.10.1, mixed mode, sharing)
    ```
     
2. **Install Java 17:** If Java 17 is not installed, you can install it using the following commands:
    
    ```bash
    sudo apt update
    sudo apt install openjdk-17-jdk
    ```
    
3. **Verify Installation:** Now you should see output similar to the previous step confirming the installation of Java 17.
    
    ```bash
    java -version
    ```
    

## Increase Limits

These steps will increase the limits for the sonarqube user and set the parameters for optimal functioning of SonarQube.

1. Edit the `limits.conf` file using the command:
    
    ```
    sudo nano /etc/security/limits.conf
    ```
    
2. Paste the following values at the bottom of the file:
    
    ```
    sonarqube   -   nofile   65536
    sonarqube   -   nproc    4096
    ```
    
3. Edit the `sysctl.conf` file using the command:
    
    ```
    sudo nano /etc/sysctl.conf
    ```
    
4. Paste the following value at the bottom of the file:
    
    ```
    vm.max_map_count = 262144
    ```
    
5. Reboot your system to apply the new limits:
    
    ```
    sudo reboot
    ```
    

## **Install Sonarqube**

1. Install Sonarqube using the following commands:
    
    ```jsx
    sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.65466.zip
    sudo apt install unzip
    sudo unzip sonarqube-9.9.0.65466.zip -d /opt
    sudo mv /opt/sonarqube-9.9.0.65466 /opt/sonarqube
    sudo groupadd sonar
    sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar
    sudo chown sonar:sonar /opt/sonarqube -R
    ```
    
2. Create service for Sonarqube
    
    ```jsx
    sudo nano /etc/systemd/system/sonar.service
    ```
    
3. Paste the following into the file:
    
    ```jsx
    [Unit]
    Description=SonarQube service
    After=syslog.target network.target
    
    [Service]
    Type=forking
    
    ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
    ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
    
    User=sonar
    Group=sonar
    Restart=always
    
    LimitNOFILE=65536
    LimitNPROC=4096
    
    [Install]
    WantedBy=multi-user.target
    ```
    
4. Start Sonarqube and Enable service
    
    ```
    sudo systemctl start sonar
    sudo systemctl enable sonar
    sudo systemctl status sonar
    sudo tail -f /opt/sonarqube/logs/sonar.log
    ```
    

## **Access the Sonarqube UI**

After completing the installation, you can access the SonarQube web interface by navigating to [`http://localhost:9000`](http://localhost:9000/) in your web browser. 

Please note that by default, SonarQube creates a user with Administrator privileges. You can log in with the following credentials: 

`Username: admin` 

`Password: admin`

> After logging in for the first time, the website will ask you to change your password for security reasons.
