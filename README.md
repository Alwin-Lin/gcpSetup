# Table of content 
 - Android cloud build
    - [Create VM with exsisting image](https://github.com/Alwin-Lin/gcpSetup/blob/master/README.md#create-vm-with-exsisting-image)
    - [Setup Chrome remote desktop](https://github.com/Alwin-Lin/gcpSetup#setting-up-remote-desktop)
    - [Build Android Virtal Device(ADV)](https://github.com/Alwin-Lin/gcpSetup#building-an-android-virtual-device)
- Createing and publishing customised image
    - [Creating the VM](https://github.com/Alwin-Lin/gcpSetup#creating-the-vm)
    - [Setting up build enviroment and build](https://github.com/Alwin-Lin/gcpSetup#setting-up-build-enviroment-and-build)
    - [Creating an image from vm](https://github.com/Alwin-Lin/gcpSetup#creating-an-image-from-vm)
    - [Sharing images to public](https://github.com/Alwin-Lin/gcpSetup#sharing-images-to-public)

## Android cloud build
### Create VM with exsisting image
- Before running the createVMwithImg file, remember to change the <YOUR_VM_NAME>
- If you chose to create one from image, skip the next section and go directly to [Running the build](https://github.com/Alwin-Lin/gcpSetup/blob/master/README.md#running-the-build-with-emulator
), everything will be set up

Run createVMwithImg, or paste the following into cloud shell
``` 
gcloud compute instances create <YOUR_VM_NAME> \
    --image-project=<PROJECT_NAME> \
    --image=acd-main
    --machine-type=<MACHINE_TYPE>
```

### Setting up remote desktop
- install wget and chrome desktop for Debian
    ``` 
    sudo apt update \
    sudo apt-get install --assume-yes wget \
    wget https://dl.google.com/linux/direct/chrome-remote-desktop_current_amd64.deb \
    sudo dpkg --install chrome-remote-desktop_current_amd64.deb \
    sudo apt install --assume-yes --fix-broken
    ``` 
- To start it
    - Follow the instructions for [Configuring and starting the Chrome Remote Desktop service](https://cloud.google.com/architecture/chrome-desktop-remote-on-compute-engine#configuring_and_starting_the_chrome_remote_desktop_service)

### Building an android virtual device
1. Launch browser inside your computer instace and go to [android studio download page](https://developer.android.com/studio)
2. Download and install android studio
3. Follow instructions over at [create and manage virtal devices](https://developer.android.com/studio/run/managing-avds#createavd)

## Creating your own image:
### Why?
- Saves time, no more waiting for android to build, and no more setting up enviroment
- Prevents error caused by different computational settings and makes sure everyone is on the same page
### Creating the VM
- Go to https://console.cloud.google.com/compute/instances
- On the top, click on Create instance
- Select N1 for series, and n1-standard-4 for machine type 
- Scroll down to Boot Disk session and click on Change, change the disk size from 10 to at least 400 and hit select
   - 250Gb for Android source, 150Gb to build
- At Firewall, check both boxes then hit Create.
- To launch VM, click on SSH and it would pop up

### Setting up build enviroment and build

1. [Setup environment](https://source.android.com/setup/build/initializing)
   - For Ubuntu 18.04
   ```
   sudo apt-get install git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig
   ```
2. Python, libncurses and repo
   - Install Python 2.7
     - Note: Stick with 2.7 for now, using python 3 can cause problems down the road
   
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
  
   Note: This will sync the main by default, to check out a branch besides main, add -b <SOURCE_TAG> after the url.
   
   The list of tags can be found on [Source code tags and builds](https://source.android.com/setup/start/build-numbers#source-code-tags-and-builds)
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
     - Use ``` lunch ``` with no additional arguments to check all avalible variants
   ```
   lunch <TARGET_VARIANT> \
   
   m -j
   ```
   Note: This process can take around 3~5 hours to finish depending on the computing power
5. Run emulator
```
emulator
```
6. Common errors
- View [troubleshooting common error](https://source.android.com/setup/build/building#troubleshooting-common-build-errors) page for more

### Creating an image from VM

Note: Shut down the VM for this step.

``` 
gcloud compute images create <YOUR_IMAGE_NAME> \
    --source-disk=<SOURCE_DISK> \
    --source-disk-zone=<ZONE> \
    --force
```

### Sharing images to public
``` 
gcloud compute images add-iam-policy-binding <YOUR_IMAGE_NAME> \
    --member='allAuthenticatedUsers' \
    --role='roles/compute.imageUser'
```
