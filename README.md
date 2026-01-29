# DevSecOps Pipeline with SonarQube, Trivy & OWASP

## Definitions

**SonarQube**: Static code analysis tool for detecting bugs, vulnerabilities, and code smells.

**Trivy**: Container security scanner for detecting vulnerabilities in images and dependencies.

**OWASP Dependency-Check**: Tool to identify known vulnerabilities in project dependencies.

## Setup Steps

### Step 1: Launch EC2 Instance
- **OS**: Ubuntu
- **Instance Type**: t2.large
- **Storage**: 30GB
- **Security Groups**: 8080, 9000

**Docker Installation:**
```bash
sudo apt update -y
sudo apt install docker.io -y
sudo apt install docker-compose -y
sudo ps
sudo usermod -aG docker ubuntu
sudo reboot
sudo ps
```

### Step 2: Jenkins Installation
**Install Java & Jenkins:**
```bash
sudo apt update -y
sudo apt install openjdk-17-jre -y
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update -y
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
**Access Jenkins**: `http://<instance-ip>:8080`

### Step 3: Install Jenkins Plugins
Navigate to **Manage Jenkins → Plugins → Available Plugins**
- SonarQube Scanner
- Sonar Quality Gates
- OWASP Dependency-Check
- Docker

### Step 4: SonarQube Setup
**Run SonarQube Container:**
```bash
docker run -d --name sonarqube-server -p 9000:9000 sonarqube:lts-community
docker ps
```

**Access SonarQube**: `http://<instance-ip>:9000`  
**Default Credentials**: admin/admin

**Configure Jenkins Integration:**
1. SonarQube: Create token (**Administration → Security → Users → Tokens**)
2. Jenkins: Add token (**Manage Jenkins → Credentials → System → Global credentials**)
3. Jenkins: Configure SonarQube server (**Manage Jenkins → System → SonarQube servers**)
4. SonarQube: Setup webhook `http://<jenkins-ip>:8080/sonarqube-webhook/`

### Step 5: Trivy Installation
```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt update -y
sudo apt install trivy -y
```

### Step 6: OWASP Dependency-Check Setup
**Configure in Jenkins:**
1. Go to **Manage Jenkins → Tools**
2. Find **Dependency-Check installations**
3. Click **Add Dependency-Check**
4. Name: `owasp-dp-check`
5. Install automatically from github.com
6. Save

**Note**: Initial setup takes ~30 minutes to download vulnerability database.

## Pipeline Usage
All tools are now configured and ready for CI/CD pipeline integration.
