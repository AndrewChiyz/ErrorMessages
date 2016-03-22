# Recording of compiling Caffe with upgraded cuda-7.5 in Ubuntu 16.04

1. install dependencies   
libopencv should not be installed, cause I have complied and installed the opencv 2.4.11, the software source of Ubuntu 16.04 provides the version 2.4.9. If you donot want to compile opencv, use instruction `sudo apt-get install libopencv-dev` to get the libs.      
`sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler`   
`sudo apt-get install --no-install-recommends libboost-all-dev`    
`sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev`    
2. Pull newest code from github of caffe  
`cp Makefile.config caffe/`   
`cd caffe`  
`git pull`  
`vim Makefile.config`   
There are several different settings in 16.04, I think it may occur in 15.10 or 15.04 too.
`# Uncomment to support layers written in Python (will link against Python libs)`    
`# This will require an additional dependency boost_regex provided by boost.`    
`WITH_PYTHON_LAYER := 1`    
`# Whatever else you find you need goes here.` </br>
`# INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include`  </br>
`# LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib` </br>
`INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/` </br>
`LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial` </br>
esc :wq </br>

3. Compilation </br>
`make -j 8` </br>
`make pycaffe`</br>
`make runtest`</br>
`make test`</br>

4. About CUDA (7.5) installation on 16.04 </br>
I still cannot find the reason why I cannot use .deb file to install cuda SDK as well as the NVIDIA driver. </br>
Here I get the solution https://www.pugetsystems.com/labs/articles/NVIDIA-CUDA-with-Ubuntu-16-04-beta-on-a-laptop-if-you-just-cannot-wait-775/</br>
The main idea is you have to first install NVIDIA driver by using manager 'software and updates'->'addtional drivers' to</br>
switch the nvidia drivers from opensource one to the other, here I switch it to 361.28 (tested)</br>
The answer to question that why we cannot use .deb to install cuda SDK, answer from</br> https://devtalk.nvidia.com/default/topic/924054/cuda-setup-and-installation/install-cuda-ubuntu-15-10-/ </br></br>"CUDA 6.5 doesn't list Ubuntu 15.10 as a supported distro. CUDA 7.5 doesn't officially support that distro either, which is evident on Table 1 in the document you linked." </br>
After reboot the system, use apt-get to install the dependencies </br>
`sudo apt-get install ca-certificates-java default-jre default-jre-headless fonts-dejavu-extra freeglut3 freeglut3-dev java-common libatk-wrapper-java libatk-wrapper-java-jni  libdrm-dev libgl1-mesa-dev libglu1-mesa-dev libgnomevfs2-0 libgnomevfs2-common libice-dev libpthread-stubs0-dev libsctp1 libsm-dev libx11-dev libx11-doc libx11-xcb-dev libxau-dev libxcb-dri2-0-dev libxcb-dri3-dev libxcb-glx0-dev libxcb-present-dev libxcb-randr0-dev libxcb-render0-dev libxcb-shape0-dev libxcb-sync-dev libxcb-xfixes0-dev libxcb1-dev libxdamage-dev libxdmcp-dev libxext-dev libxfixes-dev libxi-dev libxmu-dev libxmu-headers libxshmfence-dev libxt-dev libxxf86vm-dev lksctp-tools mesa-common-dev openjdk-7-jre openjdk-7-jre-headless tzdata-java x11proto-core-dev x11proto-damage-dev x11proto-dri2-dev x11proto-fixes-dev x11proto-gl-dev x11proto-input-dev x11proto-kb-dev x11proto-xext-dev x11proto-xf86vidmode-dev xorg-sgml-doctools xtrans-dev libgles2-mesa-dev nvidia-modprobe build-essential` </br> </br>
At the same time, you have to download the .run file of cuda SDK from NVIDIA.com </br>
here I download cuda_7.5.18_linux.run, (and this is the source of nightmare, I should consider that all my complied </br> projects, including opencv 2.4.11, caffe, mxnet, YOLO, deep-visual-toolbox, fast-rcnn etc. are all complied depending on </br> CUDA-7.0, but I have already uninstall/autoremove(d) them.) </br>
anyway </br>
`cd Download` </br>
`sudo chmod 755 cuda_7.5.18_linux.run` </br>
`sudo ./cuda_7.5.18_linux.run --override` </br>
push 'q' to exit from reading EULA.txt blablabla
then ***when the .run file tries to install nvidia-driver-352.39 chose no***, we donot need install drivers anymore
it should be like this. </br> </br>
`...` </br>
`Do you accept the previously read EULA? (accept/decline/quit): accept` </br>
`You are attempting to install on an unsupported configuration. Do you wish to continue? ((y)es/(n)o) [ default is no ]: y` </br>
`Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 352.39? ((y)es/(n)o/(q)uit): n` </br>
`...` </br> </br>
then yes, yes, yes blablabla </br> </br>
after installation is over, we have to set the environment varibles (no need?) </br>
`sudo vim -nw /etc/profile.d/cuda.sh` </br>
type in (use i to insert) </br>
`export PATH=$PATH:/usr/local/cuda/bin esc :wq` </br>
I have already export cuda bin path to my ~/.bashrc, and I donot know whether or not it works if we donot do this step </br>
`sudo vim -nw /etc/ld.so.conf.d/cuda.conf` </br>
typein 
`/usr/local/cuda/lib64 esc :wq` </br>
`source /etc/profile.d/cuda.sh` </br>
`sudo ldconfig` </br>
Open a new terminal </br>
`sudo vim /usr/local/cuda/include/host_config.h` </br>
line: 115 comment out error, gcc version in 16.04 is gcc-5.3.1 (here I show the updates in source code by github update style, and this is why I use '+') </br>
`+//#error -- unsupported GNU version! gcc versions later than 4.9 are not supported! ` </br>
complie sample project to check everything is fine </br>
since several findgllib.mk file pointed the nvidia-driver 352.39, We have to change the parameter UBUNTU_PKG_NAME to nvidia-361 </br>
samples/common/findgllib.mk and samples/3_Imaging/cudaDecodeGL/findgllib.mk </br>
point driver to nvidia-361 </br>
`UBUNTU_PKG_NAME = "nvidia-361"` </br>
in Makefile add </br>
`GLPATH=/usr/lib`
then make 
`make`

