## Database Servers

### WebServer.
-------------------------------------------------------------
| Droplet   |      Value   |
|----------|:-------------:|
| Name |  web-server-2-main-api-ubuntu-22-04 |
| VPC  | NY3 |
| RAM |    4GB  |
| CPU |    2 AMD vCPUs  |
| Memory |    25GB  |
| OS | Ubuntu 22.04 (LTS) x64 |
| Public IPv4 |    167.71.90.114  |
| Private IPv6 |    10.108.0.3 |

-------------------------------------------------------------
Log to the droplet via ssh. ***Only SSH Authentication is allowed, Password Authentication is turned off for security purposes***).
```bash
ssh root@174.138.49.168
```
-------------------------------------------------------------


### Configuration
#### Setup Node.js 
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc
nvm list-remote
nvm install v16
nvm use v16
```


#### Setup Jenkins

1. Install Jenkins
```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key |sudo gpg --dearmor -o /usr/share/keyrings/jenkins.gpg
sudo sh -c 'echo deb [signed-by=/usr/share/keyrings/jenkins.gpg] http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
sudo systemctl start jenkins.service
```

2. Opening the firewall
```bash
sudo ufw allow 8080
```

#### Setup PM2
PM2 is a process manager for our application.
1. Install PM2
```bash
npm install pm2 -g
```



