# Tensorflow GPU install on ubuntu 16.04    

These instructions are intended to set up a deep learning environment for GPU-powered tensorflow.      
[See here for pytorch GPU install instructions](https://github.com/williamFalcon/pytorch-gpu-install)

After following these instructions you'll have:

1. Ubuntu 16.04. 
2. Cuda 9.0 drivers installed.
3. A python virtuaenv with python 3.6    
4. The latest tensorflow version with gpu support.   

---   
### Step 0: Noveau drivers     
Before you begin, you may need to disable the opensource ubuntu NVIDIA driver called [nouveau](https://nouveau.freedesktop.org/wiki/).

**Option 1: Modify modprobe file**
1. After you boot the linux system and are sitting at a login prompt, press ctrl+alt+F1 to get to a terminal screen.  Login via this terminal screen.
2. Create a file: /etc/modprobe.d/nouveau
3.  Put the following in the above file...
```
blacklist nouveau
options nouveau modeset=0
```
4. reboot system   
```bash
reboot
```   
    
5. On reboot, verify that noveau drivers are not loaded   
```
lsmod | grep nouveau
```

If `nouveau` driver(s) are still loaded do not proceed with the installation guide and troubleshoot why it's still loaded.    

**Option 2: Modify Grub load command**    
From [this stackoverflow solution](https://askubuntu.com/questions/697389/blank-screen-ubuntu-15-04-update-with-nvidia-driver-nomodeset-does-not-work)    

1. When the GRUB boot menu appears : Highlight the Ubuntu menu entry and press the E key.
Add the nouveau.modeset=0 parameter to the end of the linux line ... Then press F10 to boot.   
2. When login page appears press [ctrl + ALt + F1]    
3. Enter username + password   
4. Uninstall every NVIDIA related software:   
```bash    
sudo apt-get purge nvidia*  
sudo reboot   
```   

---   
## Installation steps     


0. update apt-get   
``` bash 
sudo apt-get update
```
   
1. Install apt-get deps  
``` bash
sudo apt-get install openjdk-8-jdk git python-dev python3-dev python-numpy python3-numpy build-essential python-pip python3-pip swig python-wheel libcurl3-dev curl   
```

2. install nvidia drivers 
``` bash
# The 16.04 installer works with 16.10.
# download drivers
curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.0.176-1_amd64.deb

# download key to allow installation
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

# install actual package
sudo dpkg -i ./cuda-repo-ubuntu1604_9.0.176-1_amd64.deb

#  install cuda (but it'll prompt to install other deps, so we try to install twice with a dep update in between
sudo apt-get update
sudo apt-get install cuda-9-0   
```    

2a. reboot Ubuntu
```bash
sudo reboot
```    

2b. check nvidia driver install 
``` bash
nvidia-smi   

# you should see a list of gpus printed    
# if not, the previous steps failed.   
``` 

3. Install cudnn   
``` bash
wget https://s3.amazonaws.com/open-source-william-falcon/cudnn-9.0-linux-x64-v7.1.tgz  
sudo tar -xzvf cudnn-9.0-linux-x64-v7.1.tgz  
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```    

4. Add these lines to end of ~/.bashrc:   
``` bash
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
export PATH="$PATH:/usr/local/cuda/bin"
```   

4a. Reload bashrc     
``` bash 
source ~/.bashrc
```   

5. Install Python virtualenv and virtualenvwrapper for Python 3.6   
``` bash
sudo pip3 install virtualenv virtualenvwrapper
```   

5a. Add these lines to end of  ~/.bashrc:
``` bash
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```  

5b. Reload bashrc     
``` bash 
source ~/.bashrc
```   

6. Create virtualenv to install tf   
``` bash
mkvirtualenv tensorflow -p python3.6
```   

7. Activate env   
``` bash
workon tensorflow   
```

8. Install tensorflow with GPU support for python 3.6    
``` bash
pip install --upgrade pip

pip install tensorflow-gpu

# If the above fails, try the part below
pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.11.0-cp36-cp36m-linux_x86_64.whl
```   

9. Test tf install   
``` bash
# start python shell   
python

# run test script   
import tensorflow as tf   

hello = tf.constant('Hello, TensorFlow!')

# when you run sess, you should see a bunch of lines with the word gpu in them (if install worked)
# otherwise, not running on gpu
sess = tf.Session()
print(sess.run(hello))
```  
