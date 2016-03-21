# ErrorMessages
## Recording of the procedure (stupid steps) of system upgrade from 14.04 to 16.04
sudo apt-get update </br>
sudo apt-get upgrade </br>
sudo apt-get dist-upgrade </br>
sudo update-manager -d </br>

Black Screen with only one cursor </br>

choose to boot in text splash,  </br>
when choose which system to boot in, push 'e' to edit the  bootin mode, quiet splash -> text splash then F10 </br>
log in with usrname and password </br>
sudo apt-get update failed (maybe I made a bad choice here) </br>
HINT 'sudo dpkg --configure -a' </br>
then </br>

BOOT ERROR Message: </br>
kernel panic – not syncing VFS:unable to mount root fs on unknow – block(0,0) </br>

However the grub2 works fine  </br>
boot in live CD try Ubuntu mode or old kernel like 3.13.0-84 to check the grub list </br>
show all the kernels have been install </br>
dpkg --get-selections|grep linux </br>
check initrd.img </br>
ls /boot/initrd* </br>
initrd.img-4.4.0-13-generic has not been generated </br>
try to generate the img file </br>
mkinitramfs -o /boot/initrd.img-4.4.0-13-generic </br>

maybe I forgot to update kernel list of the system </br>
sudo update-initramfs -c -k 4.4.0-13?(4.4.0.13?)  </br>

sudo update-grub </br>
sudo reboot </br>
still cannot boot into system </br>

BOOT ERROR </br>
kernel panic – not syncing VFS:unable to mount root fs on unknow – block(0,0) </br>
 
Here I choose to boot into an old kernel 3.13.0-84 </br>
The interesting thing is I can boot into the system, and the system version is already 16.04 but the kernel version is still 3.13.0-84, and I generate the initrd.img for kernel 4.4	.0-13, then update grub etc. It still does not work. Then I choose to boot into the old kernel, cause that is my only choice now. Since I has googled the problems for half an hour, every evidence point to the nvidia driver I have installed for the old system by using .deb file. So I choose to switch it by the open source one, and that is really bad decision I made, cause when I applied the operation, the system updated all the initrd.imgs, and I cannot stop the system to do this. As expected, all the old kernels and the updated one cannot boot into the system. </br>

Then Try to boot into recovery mode </br>

choose to boot in recovery mode of kernel 4.4.0.13 </br>
choose opensource nvidia driver in ‘software and updates’→’additional drivers’ choose </br>
x.org x server – nouveau display driver  </br>
apply configuration </br>
update initrd.img </br>
update grub </br>
reboot </br>

now I can boot into the Ubuntu16.04 system with kernel 4.4.0 </br>
then I choose to switch the driver from open source driver to a Nvidia driver by using 'addtional drivers' tappage in 'software and updates' manager, and It works, cause I can reboot in the system now. </br>
