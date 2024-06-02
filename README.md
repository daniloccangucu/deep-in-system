# Deep in System Project

This repository documents the setup and configuration of a server as part of the Deep in System project from 01-edu. This README.md serves as a comprehensive guide detailing the knowledge acquired and the steps followed to establish and configure the server.

## What I Learned

- **SSH Configuration:** Utilized `sshd_config` to secure the server, learning about the options that can enhance security, such as disabling root login and specifying allowed users.
- **Virtual Machine Setup:** Configured a Virtual Machine with a static IP to ensure reliable and consistent network accessibility, essential for a stable server environment.
- **Sudo Group:** Explored the importance of the sudo group in Linux, which allows certain users administrative privileges without full root access.
- **Firewall Management:** Established firewall rules to control the flow of incoming and outgoing server traffic, enhancing network security.
- **Cron Jobs:** Implemented cron jobs to automate tasks like backups and system updates, essential for maintaining the health of the server.
- **Backup Strategies:** Emphasized the critical role of regular backups to ensure data integrity and recovery capabilities.
- **FTP and SSH Servers:** Set up FTP for file transfers and SSH for secure command-line access, understanding their roles in server management.

## Step-by-Step Setup

### Initial VM Configuration

1. **Static IP Configuration:**
   - Edited `/etc/netplan/50-cloud-init.yaml` to set a static IP address of `192.168.1.89`.
   - Configured the netmask, gateway, and DNS to ensure proper network operations.

   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         addresses: [10.5.124.67/24]
         routes:
            - to: 0.0.0.0/0
              via: 10.5.124.1
              on-link: true
         nameservers:
           addresses: [8.8.8.8]
   ```

2. **Checking the VM IP Address:**
   - Used `ip a` command to verify the assigned static IP address.

3. **SSH Server Setup:**
   - Modified `/etc/ssh/sshd_config` for secure SSH access.

     ```plaintext
     Port 2222
     PermitRootLogin no
     PasswordAuthentication no
     AllowUsers kratos luffy zoro nami
     ```

   - Restarted the SSH service using `sudo systemctl restart ssh`.

### User and Security Setup

1. **Creating Users and Setting Permissions:**
   - Added a new user `kratos` and granted sudo privileges.

     ```bash
     sudo adduser kratos
     sudo usermod -aG sudo kratos
     ```

   - Setup SSH key-based authentication for `kratos`.

     ```bash
     sudo -u kratos mkdir /home/kratos/.ssh
     sudo -u kratos chmod 700 /home/kratos/.ssh
     sudo -u kratos touch /home/kratos/.ssh/authorized_keys
     sudo -u kratos chmod 600 /home/kratos/.ssh/authorized_keys
     ```

2. **Generating and Configuring SSH Keys:**
   - Generated a private SSH key and added the public key to `/home/kratos/.ssh/authorized_keys`.

     ```bash
     ssh-keygen -t rsa -b 4096 -f kratos-key
     cat kratos-key.pub >> /home/kratos/.ssh/authorized_keys
     ```

3. **Firewall Configuration:**
   - Implemented rules to block unwanted traffic and allow necessary connections.

     ```bash
     sudo ufw allow 2222/tcp
     sudo ufw enable
     ```

### Scheduled Tasks and Maintenance

1. **Cron Job Configuration:**
   - Edited crontab to automate routine tasks, ensuring system efficiency.

     ```bash
     crontab -e
     ```

   - Example cron job to backup `/etc` directory daily:

     ```plaintext
     0 2 * * * tar -czf /backup/etc-backup-$(date +\%F).tar.gz /etc
     ```

### Testing and Validation

1. **Access Testing:**
   - Verified the ability to login via SSH as `luffy` using a private key and perform sudo operations.

     ```bash
     ssh -i luffy-key.pem -p 2222 luffy@192.168.1.89
     sudo ls /root
     ```

   - Confirmed connectivity and command execution for users `zoro` and `nami`.

### Application and Database Setup

1. **WordPress Configuration:**
   - Set up a MySQL user `wpuser` with access to the WordPress database.

     ```sql
     CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'wpwp';
     GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
     FLUSH PRIVILEGES;
     ```

   - Configured WordPress to use the database for content management.

## Resources and Tools Used

- **Phind Prompts:**
  - [Network Configuration](https://www.phind.com/search?cache=nlfhn7wf2d5jghdv70yu6nzj)
  - [Firewall Setup](https://www.phind.com/search?cache=tplx0hhh5ydwt8xztthxevcl)
  - [SSH Key Generation](https://www.phind.com/search?cache=vbxojaidogi4ikh2duv910pv)
  - [User Management](https://www.phind.com/search?cache=f5zo6afn1dizrwamsimtr4zn)
  - [Cron Jobs](https://www.phind.com/search?cache=tsw1pyi3eczlek8k758tjgc4)
  - [FTP Server Setup](https://www.phind.com/search?cache=dp8vbpqu2jl8y9n968ofp70c)
  - [Sudo Group Explanation](https://www.phind.com/search?cache=gvnc702zgj2l4x05kgprvxza)
  - [Static IP Configuration](https://www.phind.com/search?cache=of5ckfsscjlp185t3vvvyhs9)
  - [SSH Server Role](https://www.phind.com/search?cache=he3wfjn3scs7pt9ayvh9u67t)
  - [Firewall Role](https://www.phind.com/search?cache=aroy56mdfauq81m5zdlw7zvy)
  - [Backup Importance](https://www.phind.com/search?cache=l83j54hntiy80a2wkmtabbzb)

- **ChatGPT Prompts:**
  - [Server Setup Guidance](https://chat.openai.com/share/76b96697-6603-4f07-b3b9-486001b17072)
  - [Detailed Configurations](https://chatgpt.com/c/475135ae-a6a2-4f46-bcf9-25bc4a5b0aee)

- **Linux Manual Pages:**
  - [sshd_config(5) - Linux manual page](https://man7.org/linux/man-pages/man5/sshd_config.5.html)
