  * Update system and install tools
  ```
  sudo dnf update -y
   25  sudo dnf install wget git -y
```

* Install OpenJDK
```
   26  sudo dnf install java-11-amazon-corretto-devel -y
   27  java -version
```

* Install and setup Tomcat
```
# Create tomcat group and user
sudo groupadd tomcat
sudo useradd -M -s /bin/nologin -g tomcat -d /opt/tomcat tomcat
# Download Tomcat
wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.99/bin/apache-tomcat-8.5.99.tar.gz
# Create directory and extract
sudo mkdir /opt/tomcat
sudo tar xvf apache-tomcat-8.5.*.tar.gz -C /opt/tomcat --strip-components=1
# Set permissions
   37  cd /opt/tomcat
   38  sudo chgrp -R tomcat /opt/tomcat
   39  sudo chmod -R g+r conf
   40  sudo chmod g+x conf
   41  sudo chown -R tomcat webapps/ work/ temp/ logs/
   42  sudo groupadd tomcat
   43  sudo useradd -M -s /bin/nologin -g tomcat -d /opt/tomcat tomcat
   46  sudo wget https://downloads.apache.org/tomcat/tomcat-10/v10.1.39/bin/apache-tomcat-10.1.39.tar.gz
   47  sudo mkdir /opt/tomcat
   48  sudo tar xvf apache-tomcat-10.1.39.*.tar.gz -C /opt/tomcat --strip-components=1
   49  sudo tar xvf apache-tomcat-10.1.39.tar.gz -C /opt/tomcat --strip-components=1
   50  cd /opt/tomcat
   51  sudo chgrp -R tomcat /opt/tomcat
   52  sudo chmod -R g+r conf
   53  sudo chmod g+x conf
   54  sudo chown -R tomcat webapps/ work/ temp/ logs/

* Create systemd service for Tomcat
```
sudo tee /etc/systemd/system/tomcat.service > /dev/null << 'EOF'
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```

* Start and enable Tomcat
```
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
sudo systemctl status tomcat
```

```
curl localhost:8000
http://PUBLIC-IP:8080/Student/api/students-list
```