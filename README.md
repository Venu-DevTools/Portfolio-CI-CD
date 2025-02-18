# CI/CD Project Notes

## 1. Portfolio Website Deployment
- Created a portfolio website and pushed it to GitHub.
- Set up CI/CD pipeline using Jenkins, SonarQube, and Docker.

## 2. Virtual Machine Setup
- Launched three Ubuntu 22.04 VMs (t2.medium instance type) on AWS:
  - **Jenkins VM**
  - **SonarQube VM**
  - **Docker VM**

## 3. Jenkins Setup
### Steps:
1. **Update system**: `sudo apt update`
2. **Install Java**: `sudo apt install fontconfig openjdk-17-jre`
3. **Download & Install Jenkins**:
    ```sh
    sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \ 
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \ 
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \ 
    /etc/apt/sources.list.d/jenkins.list > /dev/null
    sudo apt-get update
    sudo apt-get install jenkins
    ```
4. **Restart Jenkins**: `sudo systemctl restart jenkins`
5. **Enable port 8080 in EC2 Security Group**
6. **Access Jenkins**: `http://<Jenkins-IP>:8080/`
7. **Retrieve Initial Admin Password**:
    ```sh
    cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
8. **Install Plugins & Configure Jenkins Job**:
   - Create a Freestyle Project
   - Configure GitHub repository URL & Webhook
   - Trigger build on `git push`

## 4. SonarQube Setup
### Steps:
1. **Set Hostname**: `sudo hostnamectl set-hostname SonarQube`
2. **Install Java**: `sudo apt install fontconfig openjdk-17-jre`
3. **Download & Install SonarQube**:
    ```sh
    wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.2.0.102705.zip
    sudo apt install unzip
    unzip sonarqube-25.2.0.102705.zip
    ```
4. **Start SonarQube**:
    ```sh
    cd sonarqube-25.2.0.102705/bin/linux-x86-64/
    ./sonar.sh console
    ```
5. **Enable port 9000 in EC2 Security Group**
6. **Access SonarQube**: `http://<SonarQube-IP>:9000/`
7. **Generate Authentication Token for Jenkins**
8. **Configure Jenkins to use SonarQube Scanner**
9. **Run Analysis through Jenkins Build Pipeline**

## 5. Docker Setup
### Steps:
1. **Set Hostname**: `sudo hostnamectl set-hostname Docker`
2. **Update System**: `sudo apt update`
3. **Install Docker**:
    ```sh
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc
    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

## 6. Configurations
### Jenkins to Docker VM SSH Access
1. **Enable SSH Authentication in Docker VM**
   - Edit `/etc/ssh/sshd_config`
   - Set `PasswordAuthentication yes`
   - Restart SSH: `sudo systemctl restart sshd`
2. **Generate SSH Keys in Jenkins VM**:
    ```sh
    ssh-keygen
    ssh-copy-id ubuntu@<Docker-IP>
    ```
3. **Verify SSH Access**:
    ```sh
    ssh ubuntu@<Docker-IP>
    ```

### Jenkins to Docker Integration
1. **Configure Jenkins to use Docker VM**
2. **Create a Dockerfile in GitHub Repository**:
    ```dockerfile
    FROM nginx
    COPY static-html-directory /usr/share/nginx/html
    ```
3. **Trigger Pipeline and Deploy to Docker**
4. **Run Docker Container**:
    ```sh
    docker login -u "venuhk" -p "VenuDev@91"
    cd /home/ubuntu/website/
    docker build -t mywebsite .
    docker tag mywebsite venuhk/cicdrepo:latest
    docker push venuhk/cicdrepo:latest
    docker run -d -p 8085:80 --name=Venuwebsite mywebsite
    ```
5. **Enable port 8085 in EC2 Security Group**
6. **Access the Deployed Website**: `http://<Docker-IP>:8085/`

## Summary
- Set up Jenkins, SonarQube, and Docker VMs.
- Configured a CI/CD pipeline for automated testing and deployment.
- Successfully deployed a website using Docker and Nginx.
- Ensured secure and seamless integration between services.


