# Install Docker
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce
```

# Install Java
Install Java with the following commands.
```
sudo apt update
sudo apt install default-jre
```

Use the command below to check the version.
```
java -version
```

# Install Gradle
Install Java with the following commands.
```
sudo apt -y install vim apt-transport-https dirmngr wget software-properties-common
sudo add-apt-repository ppa:cwchien/gradle
sudo apt update
sudo apt -y install gradle
```

Use the command below to check the version.
```
gradle -v
```

# Deploy Spring
On terminal
```
./gradlew bootJar
```

The output folder is in 
```
./build/libs
```

Change NGINX config
```
/etc/nginx/sites-available/default
```

```
location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                # try_files $uri $uri/ =404;
                proxy_pass http://127.0.0.1:8080
        }
}
```
