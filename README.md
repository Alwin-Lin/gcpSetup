In this document, we will go through 
# Table of content 
 - Android cloud build
    - [Create VM with exsisting image](https://github.com/Alwin-Lin/gcpSetup/blob/master/README.md#create-vm-with-exsisting-image)
- Createing and publishing customised image
    - [Creating the VM](https://github.com/Alwin-Lin/gcpSetup#creating-the-vm)
    - [Setting up build enviroment and build](https://github.com/Alwin-Lin/gcpSetup#setting-up-build-enviroment-and-build)
    - [Creating an image from vm](https://github.com/Alwin-Lin/gcpSetup#creating-an-image-from-vm)
    - [Sharing images to public](https://github.com/Alwin-Lin/gcpSetup#sharing-images-to-public)

## Android cloud build
### Create VM with exsisting image
- Before running the createVMwithImg file, remember to change the <YOUR_VM_NAME>
- If you are using the VM for building Android, it needs at least 400 GB, [250Gb for Android source, 150Gb to build](https://source.android.com/setup/build/requirements#hardware-requirements)

``` 
gcloud compute instances create <YOUR_VM_NAME> \
    --image-project=<PROJECT_NAME_OF_THE_IMAGE> \
    --image=<IMAGE_NAME> \
    --machine-type=<MACHINE_TYPE>
```
- What are the [Machine types](https://cloud.google.com/compute/docs/machine-types)? 
- How do I [Create a VM instance with a custom machine type](https://cloud.google.com/compute/docs/instances/creating-instance-with-custom-machine-type#create)?
## Creating your own image:
### Why?
- Saves time, no more waiting for android to build, and no more setting up enviroment
- Prevents error caused by different computational settings and makes sure everyone is on the same page
### Creating the VM
- Go to https://console.cloud.google.com/compute/instances
- On the top, click on Create instance
- For series and machine type, pick the ones you prefer
   - The image used in Android cloud build was built with n1 series standard-4 
- Scroll down to Boot Disk and click Change, the disk size needs to be least 400
   - [250Gb for Android source, 150Gb to build](https://source.android.com/setup/build/requirements#hardware-requirements)
- At Firewall, check both boxes then hit Create.
- To launch VM, click on SSH

### Setting up build enviroment and build

1. [Setup environment](https://source.android.com/setup/build/initializing)
   - For Ubuntu 18.04
   ```
   sudo apt-get install git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig
   ```
2. Python, libncurses and repo
   - Install Python 2.7
     - Note: Stick with 2.7 for now, using python 3 can cause problems when useing repo
   
   ```
   sudo apt-get install python
   ```
   - Insall libncurses 
   
   ``` 
   sudo apt-get install libncurses5
   ```
   - [Install repo](https://source.android.com/setup/develop#installing-repo)
  ```
  mkdir ~/bin
  PATH=~/bin:$PATH
  curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
  chmod a+x ~/bin/repo
  ```
   -  Install Java 
   ```
   sudo apt update
   sudo apt install default-jre
   ```

3. [Download from Android source tree](https://source.android.com/setup/build/downloading)

   - Config Git enviroemnt
   ``` 
   git config --global user.name <YOUR_NAME>
   git config --global user.email <YOUR@EMAIL.COM> 
   ```
   - Downliad source
  
   Note: If you want to sync the main branch, remove -b <SOURCE_TAG> from the command.
   
   The list of tags can be found on [Source code tags and builds](https://source.android.com/setup/start/build-numbers#source-code-tags-and-builds)
   ```
   mkdir -p $HOME/ws/android
   cd $HOME/ws/android
   repo init -u https://android.googlesource.com/platform/manifest -b <SOURCE_TAG>
   repo sync -c -j<THREAD_COUNT>
   ```
4. Building Android
   - Run setup script
   ```
   source build/envsetup.sh
   ```
   - Build target
     - Use ``` lunch ``` with no additional arguments to check all avalible variants.
     - This process can take around 3~5 hours to finish depending on the computing power
     - You can speed up the build process by adding additional threads, that however is limited by computing engine 
   ```
   lunch <TARGET_VARIANT> \ 
   m -j<THREAD_COUNT>
   ```
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
