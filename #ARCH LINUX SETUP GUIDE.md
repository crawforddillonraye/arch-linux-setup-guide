#ARCH LINUX SETUP GUIDE

READ ARCH LINUX WIKI 

## DOWLOADS
Download vmware workstation pro 
Use a mirror link on the archlinux wiki to dowload an iso of archlinux.
Iused the MIT mirror link

##vm setup
Follow setup instructions in vmware workstation to setup a  new virtual machine 
Ensure that you select the iso file you previously downloaded for the linux install when setting up the new vmware 
reccomend setting ram for your vmware to more than the default 768MB
2GB should be plenty, however if your machine has limited ram it is okay to go lower.
Keep in mind this will be sharing ram with your primary machine

## launching the vm
once you launch the Vmware and verify that it is running the Arch Iso boot in UEFI mode.
For windows users, go intot he file directory for your arch iso and modify the file that ends with the ".vmx" extension by insering the following into  a new line on line 2: 'firmware="efi"'
reboot the VM after making this change and it should boot in UEFI mode
continue following steps on archlinux wiki starting at 1.5

IMPORTANT on step 1.7 when pinging a website to check internet connectivity use the keyboard shortcut 'ctrl+c' to stop pinging.

## partitioning 
next is to partition your disks
you will follow the installation guide on the wiki and remember that the 2 volumes will end as sda1 and sda2
sda1 should be your boot and the other the rest of the space
format your disks with the following code
``` 
mkfs.ext4 /dev/sda2
mkfs.fat -F 32 /dev/sda1
```
# All setps after partioning must be completed in one go before rebooting or you will have to begin from this point

## mounting the file systems
run the following code 
```
mount /dev/sda2 /mnt
mount --mkdir /dev/sda1 /mnt/boot

```
## install essentials
the following code will install the basic files for linux
`pacstrap -K /mnt base linux

## configure the system
continue until step 3.2 is completed
than skip to step 3.5

## network configuration
enter the following code swapping the desired hostname for hostname
 ```
 echo hostname >> /etc/locale.conf
 pacman -Syu
 pacman -S wpa_supplicant wireless_tools network manager
 systemctl enable dhcpcd.service
 systemctl enable wpa_supplicant.service
```
i ran the following snippet and recieved a message about not using the "start command" but i would run this anyways

` systemctl start NetworkManager.service`
you wo n't know iof this has worked until after the "reboot" step

## change the root password and setup a boot loader
 next change the root password with the following command
 ` passwd `
 next install grub and set it up with the following commands
 ```
 pacman -S grub efibootmgr
 grub-install --target=x86_64-efi --efi-directory=/boot/
grub-mkconfig -o /boot/grub/grub.cfg
```
with this you should have  grub setup so that you can launch arch linux when your reboot

# make a snapshot of your system at this point so if there are any issues you can revert back to this state before it is wiped with a reboot

## reboot
first use the `exit` command exit back to the `root@archiso` and then use the command `reboot` to reboot your system and it should allow you to launch arch linux
ensure that you have internet connectivity by using `ping archlinux.org` using  `ctrl+c` to stop pinging in order to check that your internet is working.

## adding sudo users
first set your default editor to whatevver editor you want with the following command
`export EDITOR=nano`
then add your user with the user add command and change their default password
```useradd --create-home xxxx
passwd xxxx
```
to make them a sudo user first ensure you have installed the sudo command with `pacman -S sudo`
next the guide i used had me make the 'wheel' group you can make any group you want and give the group sudo permissions
we're gonna add the user we want to give sudo permissions to this group with `usermod -aG wheel xxxx`
next use the `visudo` command and scroll down the document and delete the '#'  for the group you want to give sudo permissions to

# installing a new shell
you can install any number of shells by installing it with pcman then run the name of  the shell for instance using this code
``` pacman -S zsh
zsh
```
will switch you to using the zsh shell

## installing ssh
run `pacman -S openssh`
next you can start your ssh with `systemctl start sshd`
you cna check to ensure it is up with the command `systemctl status sshd`
then you can ssh into the server ustilizing a username and ip address with the following
`ssh username@ip_address`
it wil ask you to verify the fingerpriunt when connecting for the first time, if you trust it type "yes"


## creating a desktop enviroment
i utilized lxde  first run
` pacman -S lxde`
i dowloaded all the packages myself since i had the space
next run ` systemctl enable lxdm `
then when you reboot the system it should launch the new lxde desktop enviroment

## adding aliases
these aliases are only for your current seesion unless you add them to your ~/.bashrc file with `vi ~/.bashrc`
some bash aliases i created are as follows
run `alias c='clear' ` and now you can clear the screen of clutter by typing c and hitting enter
there are lots of commands you should check out at [this guide](https://www.cyberciti.biz/tips/bash-aliases-mac-centos-linux-unix.html)