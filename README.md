# gcpSetup
This page will walk you through from creating a VM instance to launching an Android emulator manually.

Alternativly, use the pre-built image if you wish to skip customization

## Set up with pre-built image
- Before running the createVMwithImg file, remember to change the <VM_NAME>
- If you chose to create one from image, skip the next section and go directly to [Running the build](https://github.com/Alwin-Lin/gcpSetup/blob/master/README.md#running-the-build-with-emulator
), everything will be set up

Run createVMwithImg, or paste the following into cloud shell
``` 
gcloud compute instances create <VM_NAME> \
    --image-project gcpsample-311822 \
    --image aosp-env
```

## Manualy create a Linux VM and build:
### Creating the VM
- Go to https://console.cloud.google.com/compute/instances
- On the top, click on Create instance
- Select N1 for series, and n1-standard-4 for machine type 
- Scroll down to Boot Disk session and click on Change, change the disk size from 10 to at least 400 and hit select
   - 250Gb for Android source, 150Gb to build
- At Firewall, check both boxes then hit Create.
- To launch VM, click on SSH and it would pop up

### Setting up build enviroment and downloading source

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
   lunch <TARGET_VARIANT> \
   
   m -j
   ```
   Note: This process can take around 3~5 hours to finish depending on the computing power

## Running the build with emulator
The emulator was added in to your path by build process, to run: 
``` 
emulator
```

## Setting up remote desktop on Windows 10
- What does this do?
    - Allows you to accsess cloud machine on your local machine
- How much does it cost?
    - Varies depending on the machine you use, visit the [Pricing Calculator](https://cloud.google.com/products/calculator#id=6bfdb97f-013e-480a-b02a-a9192be6ce09) for an estimate
- How do I do it?
    - install wget and chrome desktop for Debian
    ``` 
    sudo apt update \
    sudo apt-get install --assume-yes wget \
    wget https://dl.google.com/linux/direct/chrome-remote-desktop_current_amd64.deb \
    sudo dpkg --install chrome-remote-desktop_current_amd64.deb \
    sudo apt install --assume-yes --fix-broken
    ``` 
    -  Follow the rest of the instructions [here](https://cloud.google.com/architecture/chrome-desktop-remote-on-compute-engine#configuring_and_starting_the_chrome_remote_desktop_service)
