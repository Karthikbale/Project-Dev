
Install Jenkins, configure Docker as agent, set up cicd, deploy applications to k8s and much more.

## AWS EC2 Instance

- Go to AWS Console
- Instances(running)
- Launch instances


### Install Jenkins.

Pre-Requisites:
 - Java (JDK)

### Run the below commands to install Java and Jenkins

Install Java

```
sudo apt update
sudo apt install openjdk-17-jre
```

Verify Java is Installed

```
java -version
```

Now, you can proceed with installing Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

- EC2 > Instances > Click on <Instance-ID>
- In the bottom tabs -> Click on Security
- Security groups
- Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed `All traffic`).


### Login to Jenkins using the below URL:

http://<ec2-instance-public-ip-address>:8080   

Note: If you are not interested in allowing `All Traffic` to your EC2 instance
      1. Delete the inbound traffic rule for your instance
      2. Edit the inbound traffic rule to only allow custom TCP port `8080`
  
After you login to Jenkins, 
      - Run the command to copy the Jenkins Admin Password - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
      - Enter the Administrator password
      

### Click on Install suggested plugins



Wait for the Jenkins to Install suggested plugins


Create First Admin User or Skip the step 

Jenkins Installation is Successful. You can now starting using the Jenkins 


## Install the Docker Pipeline plugin in Jenkins:

   - Log in to Jenkins.
   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "Docker Pipeline".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   

Wait for the Jenkins to be restarted.

## Next Sonarqube configuration
System Requirements
Java 17+ (Oracle JDK, OpenJDK, or AdoptOpenJDK)
Hardware Recommendations:
   Minimum 2 GB RAM
   2 CPU cores
   ```
sudo apt update && sudo apt install unzip -y
sudo apt install unzip
adduser sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.8.0.112029.zip
unzip *
chmod -R 775 sonarqube-25.8.0.112029
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-25.8.0.112029
cd /sonarqube-25.8.0.112029/bin/linux-x86-64
./sonar.sh start
./sonar.sh status
```
Open publicid with :9000
login :admin 
passwd:admin
Generate token from sonarqube and add to creds to jenkins for sonarqube as a secret-key 
sonarqube scanner plugin install in jenkins
## Docker Slave Configuration

Run the below command to Install Docker
##root@ip-172-31-64-125:
```
sudo apt update
sudo apt install docker.io
```
 
### Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```
Add Dockerpipeline plugin in jenkins
Once you are done with the above steps, it is better to restart Jenkins.

```
http://<ec2-instance-public-ip>:8080/restart
```

The docker agent configuration is now successful.
```
# 1️⃣ Update system
sudo apt update && sudo apt upgrade -y

# 2️⃣ Install dependencies
sudo apt install -y curl apt-transport-https conntrack

# 3️⃣ Install Docker (if not installed)
sudo apt install -y docker.io

# 4️⃣ Add users to Docker group
sudo usermod -aG docker $USER       # current user (ubuntu)
sudo usermod -aG docker jenkins     # Jenkins user (optional)
newgrp docker                       # refresh group without logout

# 5️⃣ Test Docker
docker ps

# 6️⃣ Download & install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# 7️⃣ Verify Minikube
minikube version

# 8️⃣ Start Minikube with Docker driver
minikube start --driver=docker

# 9️⃣ Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install kubectl /usr/local/bin/
kubectl version --client

# 🔟 Check Kubernetes cluster
kubectl get nodes

# Optional — Make Jenkins use Minikube
sudo mkdir -p /var/lib/jenkins/.kube
sudo cp -r ~/.kube/config /var/lib/jenkins/.kube/
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube
```



