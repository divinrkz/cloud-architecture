## Load Balancers

### Secondary Load Balancer.
-------------------------------------------------------------
| Droplet   |      Value   |
|----------|:-------------:|
| Name |  load-balancer-2-main-api-ubuntu-22-04 |
| VPC  | NY3 |
| RAM |    1GB  |
| CPU |    1 AMD vCPU  |
| Memory |    25GB  |
| OS | Ubuntu 22.04 (LTS) x64 |
| Public IPv4 |    134.122.10.33  |
| Private IPv6 |    10.108.0.6   |

-------------------------------------------------------------
Log to the droplet via ssh. ***Only SSH Authentication is allowed, Password Authentication is turned off for security purposes***).
```bash
ssh root@134.122.10.33
```
-------------------------------------------------------------


### Configuration

#### HaProxy Load Balancing
1. Install HAProxy.
```
sudo apt-get update
sudo apt-get install haproxy
haproxy -v
```

2. Open the configuration.
```
sudo nano /etc/haproxy/haproxy.cfg # Use nano
sudo vi /etc/haproxy/haproxy.cfg # Use vi
```

3. Paste the contents of haproxy config
[Config file](https://github.com/divinirakiza/cloud-config/blob/main/load-balancers/haproxy/haproxy.cfg)

4. Restart the HAProxy Status
```
sudo systemctl service haproxy restart
```

#### Keepalived Health Checks
1. Install Keepalived.
```
sudo apt-get update
sudo apt-get install keepalived
keepalived -v
```

2. Open the configuration.
```
sudo nano /etc/keepalived/keepalived.conf       # Use nano
sudo vi /etc/keepalived/keepalived.conf         # Use vi
```

3. Paste the contents of keepalived config

[Config file](https://github.com/divinirakiza/cloud-config/blob/main/load-balancers/keepalived/secondary-lb/keepalived.conf)

4. Restart the Keepalived Status
```
sudo systemctl service keepalived restart
```

### Nginx Server Blocks
1. Install Nginx
```bash
sudo apt update
sudo apt install nginx
```

2. Adjusting the firewall
```bash
sudo ufw app list                        # Show available app services in nginx
sudo ufw allow 'Nginx Full'              # Allow Nginx HTTP, HTTPs        
sudo ufw status                          # Check firewall status
systemctl status nginx                   # Check nginx webserver status
```

3. Setting Up Server Blocks
#### Backend API (api.cloud-config.dev)
1. Setup the site.
```bash
sudo mkdir -p /var/www/api.cloud-config.dev/html                 
sudo chown -R $USER:$USER /var/www/api.cloud-config.dev/html
sudo nano /etc/nginx/sites-available/api.cloud-config.dev
```

2. Open configuration file
```bash
sudo nano /etc/nginx/sites-available/api.cloud-config.dev            # Use nano
sudo vi /etc/nginx/sites-available/api.cloud-config.dev              # Use vi
```


3. Paste contents of nginx config
[Config file](https://github.com/divinirakiza/cloud-config/blob/main/load-balancers/nginx/api.cloud-config.dev.conf)

4. Enable the file config
```bash
sudo ln -s /etc/nginx/sites-available/api.cloud-config.dev /etc/nginx/sites-enabled/
```

5. Adjust Nginx Bucket size
```bash
sudo nano /etc/nginx/nginx.conf
```
Find the ```server_names_hash_bucket_size``` directive and remove the # symbol to uncomment the line.
If you are using nano, you can quickly search for words in the file by pressing CTRL and w.


6. Restart the Keepalived Status
```bash
sudo nginx -t                               #Validate
sudo systemctl service nginx restart
```
You can test this by navigating to http://api.cloud-config.dev 


#### Frontend Website (cloud-config.dev)
1. Setup the site.
```bash
sudo mkdir -p /var/www/cloud-config.dev/html                 
sudo chown -R $USER:$USER /var/www/cloud-config.dev/html
sudo nano /etc/nginx/sites-available/cloud-config.dev
```
2. Open configuration file
```bash
sudo nano /etc/nginx/sites-available/cloud-config.dev           # Use nano
sudo vi /etc/nginx/sites-available/cloud-config.dev             # Use vi
```
3. Paste contents of nginx config
[Config file](https://github.com/divinirakiza/cloud-config/blob/main/load-balancers/nginx/cloud-config.dev.conf)

4. Enable the file config
```bash
sudo ln -s /etc/nginx/sites-available/cloud-config.dev /etc/nginx/sites-enabled/
```
5. Adjust Nginx Bucket size
```bash
sudo nano /etc/nginx/nginx.conf
```
Find the ```server_names_hash_bucket_size``` directive and remove the # symbol to uncomment the line.
If you are using nano, you can quickly search for words in the file by pressing CTRL and w.

6. Restart the Keepalived Status
```bash
sudo nginx -t                               # Validate
sudo systemctl service nginx restart
```
You can test this by navigating to http://cloud-config.dev



### SSL Configuration
He use certbot letsencrypt SSL certificates.


#### Backend API (api.cloud-config.dev)
1. Install the plugin
```bash
sudo apt install certbot python3-certbot-nginx
```
2. Obtaining an SSL Certificate
```bash
sudo certbot --nginx -d api.cloud-config.dev -d www.api.cloud-config.dev
```
3. Verify Auto-Renewal
```bash
sudo systemctl status certbot.timer
```

#### Frontend Website (cloud-config.dev)
1. Install the plugin
```bash
sudo apt install certbot python3-certbot-nginx #If not installed
```
2. Obtaining an SSL Certificate
```bash
sudo certbot --nginx -d cloud-config.dev -d www.cloud-config.dev
```
3. Verify Auto-Renewal
```bash
sudo systemctl status certbot.timer
```

### Configure Reserved IP
1. Create Wrapper Script
We will need to create a DigitalOcean API token in our account.
In the control panel, click on the “API” link at the top. On the right-hand side of the API page, click “Generate new token”:

```bash
sudo nano /etc/keepalived/master.sh
```
Paste the following script
[Automatic Failover Reserved IP Script](https://github.com/divinirakiza/cloud-config/blob/main/load-balancers/scripts/automatic-failover-ip.sh).


2. Make script executable
```bash
sudo chmod +x /etc/keepalived/master.sh
```

3. Visualize the transition
```bash
sudo tail -f /var/log/nginx/access.log | awk '{print $1;}'
```