# Tensorflow GPU install on ubuntu 16.04    


1. Install some apt-get deps  

sudo apt-get install openjdk-8-jdk git python-dev python3-dev python-numpy python3-numpy build-essential python-pip python3-pip python-virtualenv swig python-wheel libcurl3-dev

2. install nvidia drivers 
```bash
#!/bin/bash
echo "Checking for CUDA and installing."
# Check for CUDA and try to install.
if ! dpkg-query -W cuda; then
  # The 16.04 installer works with 16.10.
  curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
  dpkg -i ./cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
  apt-get update
  apt-get install cuda -y
fi
```  

3. install cuda toolkit 
```bash
wget https://s3.amazonaws.com/personal-waf/cuda_8.0.61_375.26_linux.run   
sudo sh cuda_8.0.61_375.26_linux.run   # press and hold s to skip agreement   

# Do you accept the previously read EULA?
# accept

# Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 361.62?
# n

# Install the CUDA 8.0 Toolkit?
# y

# Enter Toolkit Location
# press enter


# Do you want to install a symbolic link at /usr/local/cuda?
# y

# Install the CUDA 8.0 Samples?
# y

# Enter CUDA Samples Location
# press enter    

# now this prints: 
# Installing the CUDA Toolkit in /usr/local/cuda-8.0 …
# Installing the CUDA Samples in /home/liping …
# Copying samples to /home/liping/NVIDIA_CUDA-8.0_Samples now…
# Finished copying samples.
```    

4. Install cudnn   
```bash
wget https://s3.amazonaws.com/personal-waf/cudnn-8.0-linux-x64-v5.1.tgz   
sudo tar -xzvf cudnn-8.0-linux-x64-v5.1.tgz   
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```    

5. Add these lines to end of ~/.bashrc:   
```
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
```   

6. Reload bashrc     
```bash 
source ~/.bashrc
```   

7. Install miniconda   
```
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

8. Reload bashrc     
```bash 
source ~/.bashrc
```   

9. Create conda env to install tf   
```bash
conda create -n tensorflow

# press y a few times 
```   

10. Activate env   
```bash
source activate tensorflow   
```

11. Install tensorflow with GPU support for python 3.6    
```
# pip install --ignore-installed --upgrade aTFUrl
pip install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.2.0-cp36-cp36m-linux_x86_64.whl
```   

12. Test tf install   
```
# start python shell   
python

# run test script   
import tensorflow as tf   

hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```  