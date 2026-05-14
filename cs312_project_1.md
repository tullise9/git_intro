# How to Deploy a Minecraft Server on an AWS EC2 Instance

# Part 1: Launch an EC2 Instance

1. From the AWS Homepage, open the EC2 Dashboard.
2. Click **Launch Instance**.
3. Configure the EC2 Instance with the following settings:
   
| Setting | Value |
|---|---|
| Name | Minecraft-Server |
| AMI | Ubuntu Server 26.04 LTS |
| Instance Type | t2.micro |
| Key Pair | Create a new `.pem` key pair (save securely, it will be needed to SSH into the server) |

4. Configure the security group, add the follwing to the inbound rules

| Type | Port | Source |
|---|---|---|
| SSH | 22 | 0.0.0.0/0 |
| Custom TCP | 25565 | 0.0.0.0/0 |

5. Click **Launch Instance**

---

# Part 2: Connect to the EC2 Instance Using SSH

1. Open a Terminal on your computer
2. On macOS or Linux, navigate to the folder containing the `.pem` file.
3. Run the following command to modify the key file permissions:
```bash
chmod 400 minecraft-key.pem
```
4. Connect to the instance by running the following command in your terminal, replace <public-ip> with the EC2 instance public IPv4 address
```bash
ssh -i minecraft-key.pem ubuntu@<public-ip>
```

---

# Part 3: Install Java and Download the MineCraft Server
While SSH'd to your EC2 instance:
1. Run the follwing commands to update package information and install Java 21
```bash
sudo apt update
sudo apt install openjdk-21-jdk -y
```
2. Verify the installation by checking for the version of java
```bash
java -version
```
3. Naviagte to the home directory
```bash
cd ~
```
4. Download the Minecraft server .jar file, this contains the executable files used to run the Minecraft server
```bash
wget https://launcher.mojang.com/v1/objects/4707d00eb834b446575d89a61a11b5d548d8c001/server.jar
```
5. Start the Minecraft Server
```bash
java -Xmx700M -Xms512M -jar server.jar nogui
```
**NOTE: the first time, the startup will fail but it will create the EULA file needed for part 4**

---

# Part 4: Accept the End User License Agreement (EULA) and Configure Automatic Startup
1. Open the EULA file
```bash
nano eula.txt
```
2. Change:

```text
eula=false
```

to:

```text
eula=true
```
3. Save the change and exit the file
4. Create the systemd service file
```bash
sudo nano /etc/systemd/system/minecraft.service
```
5. Paste the following into the file, then save and exit
```bash
[Unit]
Description=Minecraft Server
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu
ExecStart=/usr/bin/java -Xmx700M -Xms512M -jar /home/ubuntu/server.jar nogui
Restart=always

[Install]
WantedBy=multi-user.target
```
6. Enable the automatic service and start it
```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable minecraft
sudo systemctl start minecraft
```

---

#Part 5: Test the Automatic Startup Service
1. Start the Minecraft Server
```bash
java -Xmx700M -Xms512M -jar server.jar nogui
```
You should see the message "Done (...)! For help, type "help"
2. reboot the EC2 instance
```bash
sudo reboot
```
Your SSH session will end
3. Wait a 1-2 minutes then reconnect to the Instance (make sure to replace <public-ip> with the actual IP of the instance
```bash
ssh -i minecraft-key.pem ubuntu@<public-ip>
```
4. Check to see if the server is still running
```bash
sudo systemctl status minecraft
```
You should see "active (running)"



