# Docker Installation and Domestic Mirror Acceleration Configuration Manual

## 1\. Overview

This document guides users to install Docker on Ubuntu systems using Alibaba Cloud mirror sources. It also configures domestic mirror acceleration repositories to solve the problems of slow downloading and connection timeout of official Docker images. Finally, official test images are used to verify whether the Docker environment is installed and configured correctly. This process is standard for initializing Docker environments on Linux servers.

## 2\. Operating Environment

1\. System: Ubuntu Linux
2. Terminal Tool: FinalShell / Xshell
3. Network: Normal external network access

## 3\. Function Description

1\. Install Docker via Alibaba Cloud domestic source to improve download speed and installation success rate.
2. Configure multiple domestic mirror acceleration addresses to speed up image pulling.
3. Complete the full process of Docker installation, configuration and environment verification.

## 4\. Operation Steps

### 4.1 Update System Dependencies

Update system source and install required dependency packages:

```Plain Text
sudo apt update \&\& sudo apt install ca-certificates curl gnupg lsb-release -y
```

### 4.2 Obtain Alibaba Cloud Docker GPG Key

Download and import the public key for Docker source verification:

```Plain Text
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### 4.3 Add Alibaba Cloud Docker Source

Write the official Alibaba Cloud Docker source into the system source list:

```Plain Text
echo "deb \[arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb\_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

### 4.4 Install Docker Engine

Update source again and install Docker core components:

```Plain Text
sudo apt update \&\& sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
```

### 4.5 Authorize Ordinary User

Add current user to the docker group to use Docker without sudo:

```Plain Text
sudo usermod -aG docker $USER
```

### 4.6 Configure Domestic Mirror Acceleration

Open the Docker configuration file via vi editor:

```Plain Text
sudo vi /etc/docker/daemon.json
```

Vi operation steps:

Press **ESC** → input **gg** → input **dG** to clear all content.
Press **i** to enter insert mode, then paste the following configuration:

```Plain Text
{
  "registry-mirrors": \[
    "https://docker.m.daocloud.io",
    "https://docker.1panel.live",
    "https://hub.rat.dev"
  ]
}
```

Press **ESC**, enter **:wq** to save and exit.

Reload system service and restart Docker:

```Plain Text
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 4.7 Docker Environment Test

Pull the official test image:

```Plain Text
sudo docker pull hello-world
```

Run the test container:

```Plain Text
docker run hello-world
```

## 5\. Command \& Shortcut Description

### 5.1 Docker Commands

* **apt update**: Update system software index
* **curl**: Download network resources in terminal
* **usermod -aG**: Add user to specified group
* **daemon-reload**: Reload system service configuration
* **systemctl restart docker**: Restart Docker service
* **docker pull**: Download remote image
* **docker run**: Create and run container

### 5.2 Vi Editor Shortcuts

* **ESC**: Switch to command mode
* **gg**: Jump to the first line
* **dG**: Delete all file content
* **i**: Enter insert mode
* **:wq**: Save and exit

## 6\. Running Result

1\. No errors during Docker installation.
2. The domestic mirror addresses can be viewed via `docker info`.
3. The hello-world image can be pulled quickly.
4. The terminal outputs **Hello from Docker!**, which means the Docker service is installed and running normally.

## 7\. Common Problems \& Notes

* The `daemon.json` file must use standard English symbols, otherwise Docker cannot restart successfully.
* Do not add extra commas at the end of JSON array elements.
* Make sure to switch vi modes correctly during editing.
* Log out and log in again after user authorization to take full effect.
* Keep network stable during installation.

## 8\. Summary

This experiment completes Docker installation based on Alibaba Cloud open source source and configures multiple domestic mirror acceleration sources. It effectively solves the problem of slow official image downloading in China. The hello-world test verifies that the Docker service is installed, configured and running normally. This set of operations is the standard basic deployment process for Docker on Ubuntu Linux servers.



