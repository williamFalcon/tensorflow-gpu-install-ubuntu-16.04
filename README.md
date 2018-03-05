# Tensorflow GPU install on ubuntu 16.04    

These instructions are intended to set up a deep learning environment for GPU-powered tensorflow.    

<hr>
Before you begin, you may need to disable the nouveau (see https://nouveau.freedesktop.org/wiki/) video driver in Ubuntu.  The nouveau driver is an opensource driver for nVidia cards.  Unfortunately, this dynamic library can cause problems with the GeForce 1080ti and can result in linux not being able to start xwindows after you enter your username and password.  You can disable the nouveau drivers like so:<br><br>

1. After you boot the linux system and are sitting at a login prompt, press ctrl+alt+F1 to get to a terminal screen.  Login via this terminal screen.
2. Create a file: /etc/modprobe.d/nouveau
3.  Put the following in the above file...
```
blacklist nouveau
options nouveau modeset=0
```

Note: Once the above changes have been made, you should be able to reboot the linux box and login using the default Ubuntu 16.04 method.

After rebooting verify that `nouveau` is not loaded by running the following command and nothing should be printed
```
lsmod | grep nouveau
```

If `nouveau` driver(s) are still loaded do not proceed with the installation guide and troubleshoot why it's still loaded.
<hr>

After following these instructions you'll have:

1. Ubuntu 16.04. 
2. Cuda 9 drivers installed.
3. A conda environment with python 3.6.    
4. The latest tensorflow version with gpu support.   

## Installation steps   

<span style="color:red">NOTE: Pay SPECIAL attention to step 3 and say NO to installing the graphics driver.</span>   

0. update apt-get   
``` bash 
sudo apt-get update
```
   
1. Install apt-get deps  
``` bash
sudo apt-get install openjdk-8-jdk git python-dev python3-dev python-numpy python3-numpy build-essential python-pip python3-pip python-virtualenv swig python-wheel libcurl3-dev   
```

2. install nvidia drivers 
``` bash
# The 16.04 installer works with 16.10.
# download drivers
curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

# download key to allow installation
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

# install actual package
sudo dpkg -i ./cuda-repo-ubuntu1604_9.1.85-1_amd64.deb 
sudo apt-get update

#  install cuda (but it'll prompt to install other deps, so we try to install twice with a dep update in between
sudo apt-get install cuda=9.1.85-1 -y
sudo apt-get install -f
sudo apt-get install cuda=9.1.85-1 -y
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
wget https://s3.amazonaws.com/open-source-william-falcon/cudnn-9.1-linux-x64-v7.1.tgz  
sudo tar -xzvf cudnn-9.1-linux-x64-v7.1.tgz  
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```    

4. Add these lines to end of ~/.bashrc:   
``` bash
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
```   

4a. Reload bashrc     
``` bash 
source ~/.bashrc
```   

5. Install miniconda   
``` bash
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh   

# press s to skip terms   

# Do you approve the license terms? [yes|no]
# yes

# Miniconda3 will now be installed into this location:
# accept the location

# Do you wish the installer to prepend the Miniconda3 install location
# to PATH in your /home/ghost/.bashrc ? [yes|no]
# yes    

```   

5a. Reload bashrc     
``` bash 
source ~/.bashrc
```   

6. Create conda env to install tf   
``` bash
conda create -n tensorflow

# press y a few times 
```   

7. Activate env   
``` bash
source activate tensorflow   
```

8. Install tensorflow with GPU support for python 3.6    
``` bash
pip install tensorflow-gpu

# If the above fails, try the part below
# pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.2.0-cp36-cp36m-linux_x86_64.whl
```   

9. Test tf install   
``` bash
# start python shell   
python

# run test script   
import tensorflow as tf   

hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```  
