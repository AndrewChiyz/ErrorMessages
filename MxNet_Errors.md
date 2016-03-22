# Lib conflics of blas in Ubuntu 16.04 when Compiling MxNet  

Now I update linux kernel from 3.13.84 to kernel 4.4.0, lib conflics about blas lapack openBLAS are also revealed. </br>
and `import numpy` will trigger this error</br>

***Error Message***</br>
***Error: /usr/lib/liblapack.so.3: undefined symbol: gotoblas***</br>

solution : choose lapack lib</br>
`sudo update-alternatives --config liblapack.so.3`</br>

I choosed /usr/lib/lapack/liblapack.so.3</br>
The error may occur because of the conflic of atlas lapack and openblas</br>
`dpkg -l | grep 'openblas\|atlas\|lapack'`</br>
This command shows the system have already installed all these three packages</br>
`ii  libatlas-dev  3.10.2-9 amd64  Automatically Tuned Linear Algebra Software, C header files`</br>
`ii  libatlas3-base  3.10.2-9  amd64 Automatically Tuned Linear Algebra Software, generic shared`</br>
`ii  liblapack3 3.6.0-2ubuntu2 amd64 Library of linear algebra routines 3 - shared version`</br>
`ii  libopenblas-base  0.2.15-1build1  amd64 Optimized BLAS (linear algebra) library (shared library)`</br>
`ii  libopenblas-dev  0.2.15-1build1 amd64 Optimized BLAS (linear algebra) library (development files)`</br></br>
many solutions recommend me to uninstall/autoremove openblas, but I think it might lag the computational speed </br>
of many depended packages like caffe(and openblas is an important dependency of caffe), thus, Now I choose to </br>
select lapack to avoid removing openblas.</br></br>
About Torch plugin</br>
In `config.mk` file of MxNet project, settings about torch plugin of mxnet goes </br>
`# whether to use torch integration. This requires installing torch.`</br>
`TORCH_PATH = $(HOME)/torch`</br>
`MXNET_PLUGINS += plugin/torch/torch.mk`</br>

If your torch are not yet works fine, or you should probably use luarocks to install nn and cunn libs, </br>
otherwise it may trigger the following error messages that</br>
`/usr/bin/ld?:cannot find lnn (maybe in this form)`</br>
`/usr/bin/ld?:cannot find lcunn`</br>

So now I have to check everything is fine about torch, or I can just comment the settings about torch plugin</br> 
in config.mk of mxnet projects.</br>
Before compiling mxnet, I also checked the numpy and scipy.
