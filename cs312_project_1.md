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

# Part 3: Install Java and Download the MineCraft Server





