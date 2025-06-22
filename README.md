# The All-in-One Cybersecurity Homelab (Docker Edition)

> A complete, containerized cybersecurity home lab for offensive penetration testing, defensive analysis with a SIEM, and software reverse engineering.

## 🚀 Project Overview

This repository provides a step-by-step guide to building a powerful and isolated cybersecurity home lab using Docker. The goal is to create a safe, hands-on environment where you can practice real-world security skills without needing dedicated hardware or complex virtual machine setups.

This lab will allow you to:
*   **Attack** vulnerable machines using tools from **Kali Linux**.
*   **Defend** by monitoring network traffic and logs with a **SIEM**.
*   **Analyze** unknown binaries with **Ghidra**.
*   **Prepare** for **Capture The Flag (CTF)** competitions.

---

## 📋 Table of Contents

1.  [⚠️ Important Safety Notice](#️-important-safety-notice)
2.  [🛠️ Prerequisites](#️-prerequisites)
3.  [Phase 1: Foundational Docker Environment Setup 🏗️](#phase-1-foundational-docker-environment-setup-️)
4.  [Phase 2: Deploying Attacker and Target Machines 🎯](#phase-2-deploying-attacker-and-target-machines-)
5.  [Phase 3: Offensive Security Practice (Ethical Hacking) ⚔️](#phase-3-offensive-security-practice-ethical-hacking-️)
6.  [Phase 4: Defensive Security & SIEM Implementation 🛡️](#phase-4-defensive-security--siem-implementation-️)
7.  [Phase 5: Advanced Practice and CTF Preparation 🏆](#phase-5-advanced-practice-and-ctf-preparation-)
8.  [Credits and Acknowledgements](#-credits-and-acknowledgements)

---

## ⚠️ Important Safety Notice

This lab involves deploying intentionally vulnerable machines. It is **CRITICAL** to follow the instructions to create an **isolated Docker network**. This prevents the vulnerable containers from being exposed to your home network or the public internet, where they could be compromised by others. Use this lab for educational purposes only.

## 🛠️ Prerequisites

*   [**Docker Desktop**](https://www.docker.com/products/docker-desktop/) installed on your Windows, macOS, or Linux machine.

---

## Phase 1: Foundational Docker Environment Setup 🏗️

### Objective
Install Docker and create a secure, isolated network for all lab components.

### Key Activities
1.  **Verify Docker Installation:**
    Open a terminal and confirm Docker is running.
    ```bash
    docker --version
    ```
2.  **Create an Isolated Bridge Network:**
    This command creates a new network named `isolated_lab_net`. All containers on this network can communicate with each other, but are firewalled from your main network.
    ```bash
    docker network create --driver bridge isolated_lab_net
    ```

---

## Phase 2: Deploying Attacker and Target Machines 🎯

### Objective
Launch your Kali Linux attacker container and a vulnerable Metasploitable target container on the isolated network.

### Key Activities

#### 1. Deploy Kali Linux Container
*   **Pull the official Kali image:**
    ```bash
    docker pull kalilinux/kali-rolling
    ```
*   **Run the Kali container:**
    This command starts an interactive session inside your Kali container. Keep this terminal open to run your attacks.
    ```bash
    docker run -it --network isolated_lab_net --name kali -h kali kalilinux/kali-rolling /bin/bash
    ```
*   **Install Tools Inside the Container:**
    The base image is minimal. Run these commands *inside the Kali container's shell* to install the default toolset and Ghidra.
    ```bash
    apt update && apt upgrade -y
    apt install -y kali-linux-default ghidra
    ```

#### 2. Deploy a Vulnerable Target Machine
*   **Pull a Metasploitable 2 image:**
    ```bash
    docker pull tleemcjr/metasploitable2
    ```
*   **Run the Metasploitable container:**
    This starts the target machine in the background (`-d`) on the same isolated network.
    ```bash
    docker run -d --network isolated_lab_net --name target-meta tleemcjr/metasploitable2
    ```
*   **Find the Target's IP Address:**
    You will need this IP address for your attacks.
    ```bash
    docker inspect target-meta | grep "IPAddress"
    ```

---

## Phase 3: Offensive Security Practice (Ethical Hacking) ⚔️

### Objective
Simulate a penetration test from your Kali container against the Metasploitable target.

### Key Activities
1.  **Reconnaissance (Nmap):**
    From your `kali` container's terminal, scan the target to find open ports and services. Replace `` with the IP you found earlier.
    ```bash
    nmap -sV -A 
    ```
2.  **Vulnerability Analysis:**
    Review the Nmap output to find a weak service (e.g., vsftpd 2.3.4, which has a known backdoor).

3.  **Exploitation (Metasploit):**
    Launch the Metasploit Framework, search for the appropriate exploit, and gain a remote shell on the target.
    ```bash
    # Launch Metasploit
    msfconsole

    # Inside msfconsole
    search vsftpd
    use exploit/unix/ftp/vsftpd_234_backdoor
    set RHOSTS 
    exploit
    ```
4.  **Post-Exploitation (Ghidra):**
    After gaining access, you might find suspicious files. If you find a compiled binary, you can practice analyzing it with Ghidra to understand its purpose—a common task in CTFs.

---

## Phase 4: Defensive Security & SIEM Implementation 🛡️

### Objective
Build a SIEM with the ELK Stack to monitor your lab, detect your attacks, and learn defensive security.

### Key Activities
1.  **Deploy the ELK Stack:**
    The easiest way to launch Elasticsearch, Logstash, and Kibana is with Docker Compose. Create a `docker-compose.yml` file from a trusted source (like the official Elastic documentation) and run `docker-compose up`.
2.  **Configure Log Forwarding:**
    Deploy a log shipper like **Filebeat** as another container. Configure it to read logs from the Metasploitable container and send them to your ELK stack.
3.  **Detect & Analyze Attacks:**
    With the SIEM running, launch another `nmap` scan or `metasploit` attack. Open the **Kibana** dashboard in your web browser and watch the attack logs appear in real-time. Practice creating dashboards and alerts to visualize the malicious activity.

---

## Phase 5: Advanced Practice and CTF Preparation 🏆

### Objective
Expand your lab's capabilities and use it to solve real challenges.

### Key Activities
*   **Add More Targets:** Find other vulnerable Docker containers on Docker Hub and [VulnHub](https://www.vulnhub.com/) to practice against different vulnerabilities (e.g., web app flaws from OWASP Juice Shop).
*   **Solve CTFs:** Use your lab to tackle CTF challenges. The combination of Kali, Ghidra, and Wireshark in a controlled environment is perfect for this.
*   **Simulate Incident Response:** Create detection rules in your SIEM. When an alert fires, use your tools to investigate the "incident" and document your findings, just like a SOC analyst would.

---

## Credits and Acknowledgements

This project plan was inspired by the excellent research and tutorials from the following creators:
*   [David Bombal's Homelab Guide](https://youtu.be/izmCJlJEvQw?si=uB4XJFWbx9jOFORY)
*   [IppSec's Homelab Overview](https://youtu.be/kku0fVfksrk?si=mi9ffb0EoUxwojxX)
*   [John Hammond's SIEM Setup](https://youtu.be/cMQtGjQOq0c?si=gkDc3YnrCy_JrFrk)
*   [NetworkChuck's Hacking Lab](https://youtu.be/XIvn0ZDSmKA?si=Cov5h9J22sqqfoA4)







The best and most modern way to deploy a full open-source honeypot stack (multiple honeypots, a SIEM, and a dashboard) is to use **T-Pot**. T-Pot is a brilliant all-in-one platform that packages numerous honeypot tools (like Cowrie, Honeytrap, etc.) and a full ELK stack (Elasticsearch, Logstash, Kibana) into a single, easy-to-install system using Docker.

This plan uses T-Pot because it directly achieves your goal of a powerful, open-source system with a world map, and it is the standard method for a comprehensive honeypot deployment on AWS [1][2][3].

---

# Project: Live Threat Intelligence Honeypot on AWS with T-Pot

<p align="center"> <img src="https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white" alt="AWS"> <img src="https://img.shields.io/badge/T--Pot-4B0082?style=for-the-badge&logo=linux&logoColor=white" alt="T-Pot"> <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker"> <img src="https://img.shields.io/badge/SIEM-ELK_Stack-005571?style=for-the-badge&logo=elasticstack&logoColor=white" alt="ELK Stack"> </p>


This guide details how to deploy a comprehensive, multi-service honeypot using the T-Pot platform on Amazon Web Services (AWS). The project will create a live, public-facing decoy system to attract, capture, and analyze real-world cyberattacks, visualizing the data on a live world map.

This project is a standalone, cloud-based initiative and should be kept entirely separate from your local, isolated practice lab.

## 📋 Table of Contents

1.  [⚠️ Critical Warnings: Security & Cost](#️-critical-warnings-security--cost)
2.  [🛠️ Prerequisites](#️-prerequisites)
3.  [Phase 1: Launching the AWS EC2 Instance ☁️](#phase-1-launching-the-aws-ec2-instance-️)
4.  [Phase 2: Installing T-Pot on the VM 🍯](#phase-2-installing-t-pot-on-the-vm-)
5.  [Phase 3: Accessing Your Honeypot Dashboards 🗺️](#phase-3-accessing-your-honeypot-dashboards-️)

---

## ⚠️ Critical Warnings: Security & Cost

### Security
This project creates a virtual machine that is **intentionally exposed to the internet** to attract attackers.
*   **NEVER** store any personal or sensitive data on this machine.
*   **ONLY** use this VM for the honeypot. Do not use it for any other activity.
*   Always access the VM using a secure SSH key, not a password.

### Cost
This project requires a more powerful virtual machine than a typical free-tier instance and **will incur costs**.
*   T-Pot requires significant resources (RAM and CPU) to run its many services (ELK Stack, multiple honeypots) [2][3].
*   An instance like a `t3.xlarge` can cost around **$7-10 per day** [1].
*   **Remember to STOP or TERMINATE your EC2 instance** as soon as you are finished with the project to avoid unexpected charges.

---

## 🛠️ Prerequisites

*   An **Amazon Web Services (AWS) Account**.
*   Familiarity with the AWS Management Console.
*   An **SSH client**. This is built into macOS (Terminal) and Linux. For Windows, you can use PowerShell or [PuTTY](https://www.putty.org/).

---

## Phase 1: Launching the AWS EC2 Instance ☁️

This is the most critical phase. Follow these settings precisely to ensure T-Pot has the resources and network access it needs.

1.  **Navigate to the EC2 Dashboard:**
    *   Log into your AWS account.
    *   Go to the EC2 service and click "Launch instances".

2.  **Choose the AMI (Operating System):**
    *   In the search bar for AMIs, type **"Debian 11"** and press Enter.
    *   Click on the **"AWS Marketplace AMIs"** tab.
    *   Select the official **Debian 11** image. T-Pot requires Debian, and this specific version is recommended for compatibility [1][2].

3.  **Select the Instance Type:**
    *   T-Pot is resource-heavy. You must choose an instance with at least 4GB of RAM, but **16GB is highly recommended** for stable performance [2].
    *   Select an instance type like **`t3.xlarge`**. This provides enough power to run the honeypots and the ELK stack smoothly [3].

4.  **Configure a Key Pair:**
    *   In the "Key pair (login)" section, create a new key pair.
    *   Give it a memorable name (e.g., `tpot-key`).
    *   Download the `.pem` file and save it somewhere secure. **You will not be able to download it again.**

5.  **Configure Network Settings (Security Group):**
    *   Click "Edit" on the Network settings panel.
    *   Create a new security group.
    *   **Rule 1 (Honeypot Traffic):** Add a rule to allow **All traffic** from source **Anywhere (`0.0.0.0/0`)**. This opens all ports on your VM, allowing T-Pot to listen for attacks on any service it emulates.
    *   **Rule 2 (Management Access):** Add another rule to allow **SSH (TCP port 22)** from source **My IP**. This ensures only you can securely connect to the machine for setup.

6.  **Configure Storage:**
    *   T-Pot and its logs require significant disk space.
    *   Set the storage size to at least **128 GB** of General Purpose SSD (gp2/gp3) [3].

7.  **Launch the Instance:**
    *   Review your settings and click "Launch instance". Wait a few minutes for the instance to initialize.
    *   Once it's running, find and copy its **Public IPv4 address** from the EC2 dashboard.

---

## Phase 2: Installing T-Pot on the VM 🍯

Now you will connect to your new cloud server and install the T-Pot software.

1.  **Connect via SSH:**
    *   Open your terminal or SSH client.
    *   Use the `.pem` key file you downloaded to connect. Replace `` and `` with your actual values. The default username for Debian AMIs is `admin`.
    ```bash
    ssh -i /path/to/ admin@
    ```

2.  **Update the System and Install Git:**
    *   Once connected, run the following command to make sure the system is up-to-date and has Git installed [2]:
    ```bash
    sudo apt update && sudo apt upgrade -y && sudo apt install git -y
    ```

3.  **Clone the T-Pot Repository:**
    *   Use Git to download the official T-Pot installation files.
    ```bash
    git clone https://github.com/telekom-security/tpotce.git
    ```

4.  **Run the Installer:**
    *   Navigate into the newly created directory and run the installation script.
    ```bash
    cd tpotce/
    sudo ./install.sh
    ```
    *   The script will start an interactive setup. You will be asked to:
        *   Choose an edition. The **'STANDARD'** edition is a great choice to start with.
        *   Create a username and a strong password for web access. **Save this password securely!** This is how you will log into your dashboards.
    *   The installation will take a long time (15-30 minutes) as it downloads and configures numerous Docker containers. Once it's done, it will prompt you to reboot. Reboot the system.

---

## Phase 3: Accessing Your Honeypot Dashboards 🗺️

After the system reboots, your honeypot is live and collecting data.

1.  **Access the Web Interface:**
    *   Open a web browser and go to: `https://:64297`
    *   You will see a browser warning about an untrusted security certificate. This is normal. Click "Advanced" and proceed to the site.
    *   Log in with the web username and password you created during the installation.

2.  **Explore Your Dashboards:**
    You now have access to a powerful suite of tools:
    *   **Attack Map:** The live world map showing real-time attack sources. This may be blank at first but will populate as attackers find your honeypot.
    *   **T-Pot Dashboard:** An overview of system health and key attack metrics.
    *   **Kibana:** The data visualization tool for the ELK stack. Here you can create custom dashboards and dive deep into the raw log data to analyze attacker techniques, usernames, passwords, and payloads.

**Congratulations!** You have successfully deployed a live, enterprise-grade honeypot in the cloud. Let it run for a few hours or a day and check back to see what you've caught.

> **Final Reminder:** Don't forget to **TERMINATE** the EC2 instance in the AWS console when you are done to stop incurring costs.

