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