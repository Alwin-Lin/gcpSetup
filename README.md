# gcpSetup

## Create a Linux VM as a build machine:
- Go to https://console.cloud.google.com/compute/instances
- On the top, click on Create instance
- Select N1 for series, and n1-standard-4 for machine type 
- Scroll down to Boot Disk session and click on Change, change the disk size from 10 to at least 400 and hit select
   - 250Gb for Android source, 150Gb to build
- At Firewall, check both boxes then hit Create.
- To launch VM, click on SSH and it would pop up

## Setting up build enviroment
1. [Setup environment](https://source.android.com/setup/build/initializing)
   - [Install required packages](https://source.android.com/setup/build/initializing#installing-required-packages-ubuntu-1804)
2. Python, libncurses and repo
   - Install Python 2.7
   
   ```
   sudo apt-get install python
   ```
   - Insall libncurses 
   
   ``` 
   sudo apt-get install libncurses5
   ```
   - Install repo

   ```
   sudo apt-get install repo
   ```
   
   -  Install Java 
   ```
   sudo apt update
   sudo apt install default-jre
   ```


3. [Download from Android source tree](https://source.android.com/setup/build/downloading)

   - Config Git enviroemnt
   ``` 
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com" 
   ```
   - Downliad source
   ```
   mkdir -p $HOME/ws/android
   cd $HOME/ws/android
   repo init -u https://android.googlesource.com/platform/manifest
   repo sync
   ```
