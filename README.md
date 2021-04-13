# Install Docker
```
sudo apt -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt -y install docker-ce
```

# Install HA Proxy
Install HA Proxy with the following commands.
```
sudo apt -y install haproxy
```

Config file location.
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
