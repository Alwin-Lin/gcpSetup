# gcpSetup

## Create a Linux VM as a build machine:
### With UI
- Go to https://console.cloud.google.com/compute/instances
- On the top, click on Create instance
- Select N1 for series, and n1-standard-4 for machine type 
- Scroll down to Boot Disk session and click on Change, change the disk size from 10 to at least 400 and hit select
   - 250Gb for Android source, 150Gb to build
- At Firewall, check both boxes then hit Create.
- To launch VM, click on SSH and it would pop up
### Creating a vm directly from images
Note:
- If you chose to create one from image, skip the next section and go directly to Running the build, everything will be set up
- Before running the createVMwithImg file, remember to change the <VM_NAME>
``` 
gcloud compute instances create <VM_NAME> \
    --image-project gcpsample-311822 \
    --image aosp-env
```

## Setting up build enviroment and downloading source
1. [Setup environment](https://source.android.com/setup/build/initializing)
   - For Ubuntu 18.04
   ```
   sudo apt-get install git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig
   ```
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
4. Building Android
   - Run setup script
   ```
   source build/envsetup.sh
   ```
   - Build target
   ```
   lunch <TARGET_VARIANT>
   ```
## Running the build with emulator
The emulator was added in to your path by build process, to run: 
``` 
emulator
```
