# Tensotflow (GPU enabled) installation on Linux Ubuntu
```
The official tensorflow installation guide(https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html#pip-installation) sometime has obscure instruction and errors due to setup & version difference at the local machine. Following is my note on what problems occurred during my installation and how I solved it.
```
```
My machine specifications:
 - type: Desktop (linux 64)
 - OS: ubuntu 14.04 LTS
 - graphic card: NVIDIA GeForce GTX 970

```
### Installation Overview
```
Tensorflow instruction page gives a few options to install on linux machines. I used pip install simply because I didn't have anaconda or other stuff installed previously and didn't want to install too many additional stuffs. But, it might be a good idea to use those since they have their own environment so less chance to interact with existing settings of python. The instruction says I need Cuda Installation before Tensorflow installation. (In reality, in my poor execution of those instructions, the order got messed up, but it worked ok generally- if something is not ok, the error message will tell you to go back to certain step.)
```
###During the CUDA 7.5 installation on Ubuntu 14. for setting up TensorFlow for GPU (NVIDIA GeForce 970), the error message said installation failed because X server is running.
``` This appeared to work
1) Hit CTRL+ALT+F1 and login using your credentials.
2) kill your current X server session by typing sudo service lightdm stop or sudo stop lightdm
3) I didn't have to do this (Enter runlevel 3 by typing sudo init 3 and install your *.run file.)

When x server is stopped, all the graphic interface is gone (since x server is managing graphical interface) and only a blank terminal is available. Then I went into a directory where cuda installation execution file is, then followed their instruction (https://developer.nvidia.com/cuda-downloadsRun : `sudo sh cuda_7.5.18_linux.run`) to install cuda, and it went successfully.
4) Then I started X server: (You might be required to reboot when the installation finishes. If not, run sudo service lightdm start or sudo start lightdm to start your X server again.)
```
### When installling cudnn, the directories of files (folder name cudnn-7.5-linux-x64-v4 below- my folder was called just cuda when I opened tgz file) were slightly different, but it is easy to figure out and change the command accordingly. Also it assumes cuda was installed the default directory /usr/local/cuda. If someone had different location for cuda, need to change accordingly (I used all default locations).
```bash
tar xvzf cudnn-7.5-linux-x64-v4.tgz
sudo cp cudnn-7.5-linux-x64-v4/cudnn.h /usr/local/cuda/include
sudo cp cudnn-7.5-linux-x64-v4/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

###After installing cuda toolkit 7.5 and cudnn 7.0 (just default latest version when I installed), I followed configure instruction (https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html#optional-install-cuda-gpus-on-linux), where a lot of confusion happens.
```
First of all, in the paragraph Configure TensorFlow's canonical view of Cuda libraries, I was puzzled I couldn't figure out where the "configure script" was, and what does "the root of your source tree" mean.
Someone in stackoverflow gave a link(https://github.com/tensorflow/tensorflow). Then I git cloned those to my download folder (doesn't have to be the same) and ran a bash command below (in the downloaded directory).
```
```bash
./configure
```
```
Then, it asks bunch of questions. Most can be answered as written in the tensorflow instruction. There were a couple of things I stumbled on:
1) No SWIG_PATH error on the question 'Do you wish to build TensorFlow with GPU support?'
-> It turns out I didn't have swig installed. Installing the latest version from swig.org solved the problem.
2) Mine had old version of gcc (4.8), so I had to install/upgrade it to 4.9. It seems that currently there are newer version available.
3) Cudnn version: Mine is not 4.0.4. I checked version by a bash command "nvcc -V" (do it as root.) and it was 7.0(?), but the configure would not accept it, so I left it as default. Same applies to the location of cudnn library.
```
###Building target with GPU support
```
I didn't have bazel, the tensorflow setup page (https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html) has links to pre-requisite setups before pip installation. Bazel can be found here: (http://www.bazel.io/docs/install.html). Bazel needs JDK 8 or later, and my Ubuntu (14.04) needed a different installation which is well written in the bazel installation page.
Somehow this stuff didn't work- but bazel ran ok.
```
```bash
chmod +x PATH_TO_INSTALL.SH
./PATH_TO_INSTALL.SH --user
```
### Testing tensorflow
```
run python or ipython and run some codes
```
```python
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
print(sess.run(hello))
```
```
When I accidentally ran ipython in the tensorflow source directory, I got error:
"ImportError: cannot import name pywrap_tensorflow", but it turned out that python needs to run outside the source directory, otherwise python will load library from a wrong place in side the source directory.
```
### Testing if Tensorflow is running on GPU
```
I followed typing some code here:
https://www.tensorflow.org/versions/r0.9/how_tos/using_gpu/index.html
And it works beautifully!
```