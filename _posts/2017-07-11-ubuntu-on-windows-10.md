---
layout: single
title: Ubuntu on Windows 10
description: ""
category:
tags: [windows, linux]
permalink: /2017/07/11/ubuntu-on-windows-10/
---
This post is a simple note that documents my favoriate setup on a Windows 10 machine.


# Enable Windows Subsystem Linux (WSL)

```
Settings -> Update & Security -> For Developers.
Activate the “Developer Mode”

Control panel -> Programs -> Turn Windows Features On or Off -> Enable "Windows Subsystem for Linux (Beta)"
Restart the machine
```

# Install the Ubuntu system
```
Search bash
type y to continue installing Ubuntu
Create your username and password for Ubuntu system
```
Now you should be about to find a app "Bash on Ubuntu on Windows"
In the rest of post, all commands are inside such app

# Check and update Ubuntu version
https://askubuntu.com/questions/931626/upgrade-ubuntu-14-04-to-16-04-on-on-windows-10

```
# check version
lsb_release -a

# update to latest Ubuntu
# this is the recommended way by Microsoft such it could fail though
sudo do-release-upgrade

# now check again
lsb_release -a
```

# Download Windows 10 Creators Update

You may encounter 'screen is terminiting' error when trying to upgrade. This is due to your current Windows version disallow you to do so.

You need to [update Windows](https://support.microsoft.com/en-us/instantanswers/d4efb316-79f0-1aa1-9ef3-dcada78f3fa0/get-the-windows-10-creators-update)

Well...as I am not a system administrator, I failed to update. So I will use **Ubuntu 14.04** in the following sections


# Install CUDA
```
# this is for 14.04, you can change the name to 16.04
curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1404/x86_64/cuda-repo-ubuntu1404_8.0.61-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1404_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda
```

# Install Docker

Instead of several command line. I have found a bash script to automate this. There is the copy of the script

```
wget https://gist.githubusercontent.com/spencerimp/f83a8c2d04df9f52fb8f491d1d0323b7/raw/b8831145bf425cd4e767c371d884286ec6036464/install_docker.sh

sh install_docker.sh
```

Or, you can just copy and paste the content of the script

```
# Ask for the user password
# Script only works if sudo caches the password for a few minutes
sudo true

# Install kernel extra's to enable docker aufs support
# sudo apt-get -y install linux-image-extra-$(uname -r)

# Add Docker PPA and install latest version
# sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
# sudo sh -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
# sudo apt-get update
# sudo apt-get install lxc-docker -y

# Alternatively you can use the official docker install script
wget -qO- https://get.docker.com/ | sh

# Install docker-compose
COMPOSE_VERSION=`git ls-remote https://github.com/docker/compose | grep refs/tags | grep -oP "[0-9]+\.[0-9][0-9]+\.[0-9]+$" | tail -n 1`
sudo sh -c "curl -L https://github.com/docker/compose/releases/download/${COMPOSE_VERSION}/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose"
sudo chmod +x /usr/local/bin/docker-compose
sudo sh -c "curl -L https://raw.githubusercontent.com/docker/compose/${COMPOSE_VERSION}/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose"

# Install docker-cleanup command
cd /tmp
git clone https://gist.github.com/76b450a0c986e576e98b.git
cd 76b450a0c986e576e98b
sudo mv docker-cleanup /usr/local/bin/docker-cleanup
sudo chmod +x /usr/local/bin/docker-cleanup
```

# Add your account to Docker group

By doing this, your non-root user does not need to type **sudo** when executing docker.

```
sudo docker -aG docker [YOUR ACCOUNT]
```

# Install Nvidia-Docker
If you need CUDA support (i.e. GPU) in the Docker container, you need to install this as well.

Just download the deb file from their Github release.

```
# Rembmer to check the version you prefer
# https://github.com/NVIDIA/nvidia-docker/releases/
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker_1.0.1-1_amd64.deb
sudo dpkg -i /tmp/nvidia-docker*.deb && rm /tmp/nvidia-docker*.deb
```

# Install Anaconda 3

```
# https://www.continuum.io/downloads
wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh
bash Anaconda3-4.4.0-Linux-x86_64.sh
```

# Other configuration

Default editor

```
#Set default edit as vim
sudo update-alternatives --config editor
```

Or just set the environment in your .bashrc

```
export EDITOR=vim
```

# Run Docker on Windows

[https://serverfault.com/questions/767994/can-you-run-docker-natively-on-the-new-windows-10-ubuntu-bash-userspace](https://serverfault.com/questions/767994/can-you-run-docker-natively-on-the-new-windows-10-ubuntu-bash-userspace)

This is actually tricker than I expected. We cannot just run Docker inside Ubuntu on Windows. However, we can install Docker for Windows as the engine, and Ubuntu on Windows as client, and connect them via TCP.


## Install Docker engine for Windows
[https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows](https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows)

Steps

- Download and install the Stable Channel
- Enable Hyper-V in Windows features
- Download the Docker for [Windows installer](https://download.docker.com/win/stable/InstallDocker.msi)
- You may need to restart during installation

Note:
If you Windows machine is a virtual machine, you will probably failed (like me).
