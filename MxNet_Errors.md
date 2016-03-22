# Lib conflics of blas in Ubuntu 16.04 when Compiling MxNet  

Now I update linux kernel from 3.13.84 to kernel 4.4.0, </br>
lib conflics about blas lapack openBLAS </br>

import numpy will trigger this error</br>

***error message***</br>
***Error: /usr/lib/liblapack.so.3: undefined symbol: gotoblas***</br>

solution : choose lapack </br>
`sudo update-alternatives --config liblapack.so.3`</br>

I choosed /usr/lib/lapack/liblapack.so.3</br>
The error may occur because of the conflic of atlas lapack and openblas</br>
`dpkg -l | grep 'openblas\|atlas\|lapack'`</br>
This command will show the system have already installed all these three packages</br>
many solutions recommend to uninstall/autoremove openblas, but it will lag the computational speed of many depended packages,</br>
thus, Now I choose to select lapack to avoid removing openblas.</br>

Torch plugin</br>
In `config.mk` file</br>
`# whether to use torch integration. This requires installing torch.`</br>
`TORCH_PATH = $(HOME)/torch`</br>
`MXNET_PLUGINS += plugin/torch/torch.mk`</br>

If your torch are not yet works fine, or you should probably use luarocks to install nn and cunn libs, </br>
otherwise it may trigger the following error messages that</br>
`/usr/bin/ld?:cannot find lnn (maybe in this form)`</br>
`/usr/bin/ld?:cannot find lcunn`</br>

so you have to check everything is fine about torch, or you should just comment the settings about torch plugin</br> 
in config.mk of mxnet projects</br>
 
