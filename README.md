# Install Docker
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce

# Install JVM
sudo apt update
sudo apt install default-jre
java -version

# Install Gradle
sudo apt -y install vim apt-transport-https dirmngr wget software-properties-common
sudo add-apt-repository ppa:cwchien/gradle
sudo apt update
sudo apt -y install gradle
gradle -v

# Deploy Spring
On terminal
```
./gradlew bootJar
```
The output folder is in 
./build/libs

Change NGINX configuration in
/etc/nginx/sites-available/default
```
location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                # try_files $uri $uri/ =404;
                proxy_pass http://127.0.0.1:8080
        }
}
```
