# gcpSetup
## Setting up build enviroment
1. [Setup environment](https://source.android.com/setup/build/initializing)
   - [Install required packages](https://source.android.com/setup/build/initializing#installing-required-packages-ubuntu-1804)
2. [Get Repo for source control](https://source.android.com/setup/develop)
   - Python and libncurses are needed on VM
   - Install Python 2.7
   
   ```
   sudo apt-get install python
   ```
   - Insall libncurses 
   
   ``` 
   sudo apt-get install libncurses5
   ```

3. [Download from Android source tree](https://source.android.com/setup/build/downloading)

   - Config Git enviroemnt
   ``` 
   git config --global user.name "Your Name
   git config --global user.email "you@example.com 
   ```
   - Downliad source
   ```
   mkdir -p $HOME/ws/android
   cd $HOME/ws/android
   repo init -u https://android.googlesource.com/platform/manifest
   repo sync
   ```
