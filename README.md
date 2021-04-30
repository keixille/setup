# Install Docker
Install Docker with the following commands
```
sudo apt -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt -y install docker-ce
```

# HA Proxy
## Install HA Proxy
Install HA Proxy with the following commands
```
sudo apt -y install haproxy
```

Config file location
```
/etc/haproxy/haproxy.cfg
```

Configuration file for max connection, number of process, and number of thread used
```
global
        maxconn 2000
        nbproc 1
        nbthread 4
***
```

Configuration file for load balance
```
***
frontend http_front
   bind *:80
   stats uri /haproxy?stats

   acl url_auth path_beg /auth
   use_backend http_auth if url_auth

   default_backend http_back

backend http_back
   balance roundrobin
   server back_spring1 172.31.77.2:80 check
   server back_spring2 172.31.74.106:80 check

backend http_auth
   balance roundrobin
   server auth_spring1 172.31.74.118:80 check
```

## SSL on HA Proxy
Install Certbot with the following commands
```
sudo add-apt-repository -y ppa:certbot/certbot
sudo apt-get update
sudo apt-get install -y certbot
```

Change the HA Proxy configuration file
```
***
frontend http_front
    bind *:80
    
    stats uri /haproxy?stats

    acl letsencrypt_acl path_beg /.well-known/acme-challenge/
    use_backend letsencrypt_backend if letsencrypt_acl

    default_backend http_back

backend letsencrypt_backend
    server letsencrypt 127.0.0.1:8888
***
```

Run the command below
```
sudo certbot certonly --standalone -d test.haproxy.com \
--non-interactive --agree-tos --email admin@example.com \
--http-01-port=8888
```

Concat certificate and private key
fullchain = (server + intermediate) certificate 
```
sudo mkdir -p /etc/ssl/test.haproxy.com

sudo cat /etc/letsencrypt/live/test.haproxy.com/fullchain.pem \
/etc/letsencrypt/live/test.haproxy.com/privkey.pem \
| sudo tee /etc/ssl/test.haproxy.com/test.haproxy.com.pem
```

Change HA Proxy configuration to bind SSL connection with created certificate
```
***
frontend http_front
    bind *:80
    bind *:443 ssl crt /etc/ssl/test.haproxy.com/test.haproxy.com.pem
    
    stats uri /haproxy?stats
***

```

# Install Nginx
```
sudo apt -y install nginx
sudo ufw allow 'Nginx Full'
```

Config file location for URL redirection
```
/etc/nginx/sites-available/default
```

Configuration file for redirect to localhost
```
***
location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                # try_files $uri $uri/ =404;
                proxy_pass http://127.0.0.1:8080
        }
}
***
```

# Install Java
Install Java with the following commands
```
sudo apt update
sudo apt -y install default-jre
```

Use the command below to check the version
```
java -version
```

# Install Gradle
Install Gradle with the following commands
```
sudo apt -y install vim apt-transport-https dirmngr wget software-properties-common
sudo add-apt-repository ppa:cwchien/gradle
sudo apt update
sudo apt -y install gradle
```

Use the command below to check the version
```
gradle -v
```

# Deploy Spring
On terminal
```
./gradlew bootJar
```

Output folder
```
./build/libs
```

Configuration script on start instance
```
sudo rm server.log
java -jar test-application-0.0.1-SNAPSHOT.jar > server.log & 
```

Setup cron
```
crontab -e
```

Configuration cron on start instance
```
@reboot sudo sh /opt/onStart.sh
```

# Install MongoDB
Install MongoDB Community Edition with the following commands
```
wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
sudo apt-get update
sudo apt-get install -y mongodb-org
```

To enable on start instance
```
sudo systemctl enable mongod
```

## Enable remote access on local MongoDB
Nginx config file location 
```
/etc/nginx/nginx.conf
```

Configuration for nginx.conf
```
***
stream {
    server {
        listen 27020;
        proxy_connect_timeout 300;
        proxy_timeout 300;
        proxy_pass    stream_mongo;
    }

    upstream stream_mongo {
        server 127.0.0.1:27017;
    }
}

http {
***
```

Change Security Groups Inbound

|Type|Protocol|Port Range|Source|Description
|-|-|-|-|-|
|Custom TCP Rule|TCP|27020|0.0.0.0/0|Remote MongoDB|
***

# AWS
## Install AWS CLI
Install AWS command line interface 
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo sh install
```

Start instance
```
aws ec2 start-instances --instance-ids i-05f0e7585c060ae78
```

Stop instance
```
aws ec2 stop-instances --instance-ids i-05f0e7585c060ae78
```

Modify volume
```
aws ec2 modify-volume --volume-id vol-05b58fb86f023f6d3 --size 10 --volume-type gp2
```

More EC2 command on --> https://docs.aws.amazon.com/cli/latest/reference/ec2/index.html#cli-aws-ec2
***
