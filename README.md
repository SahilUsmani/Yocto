# Yocto

1. Install Yocto from the yocto website, there are many versions of yocto's reference distribution poky. Choose the latest stable LTS version.

2. Use git clone to clone the poky and use the -b flag to choose the version(like kirkstone). Check the linux distros version compatibilty with the poky's version compatibility.
	- example: git clone -b kirkstone <yocto poky link from github>

3. Install the necessary meta layers from the yocto's meta layers index or the openembedded's meta layers index. Prioritize the yocto's index first if it's not available in yocto index then find it on openembedded's index.

4. Raspberrypi meta layer: meta-raspberrypi. It can be cloned from the github too and choose the branch using -b. The branch should be the same version of the poky. If kirkstone is chosen for poky then kirkstone must be chosen for the meta layers too.
	- example: git clone -b kirkstone <meta-layer link>

5. Poky has a build environment inside it's directory named "oe-init-build-env". This build environment is a POSIX shell script executable. It can be executed by following example
	- example: source oe-init-build-env build
Here "build" is a directory which will be created with a directory inside it called "conf". Conf will have files named "bblayers.conf local.conf and one more".

6. bblayers.conf will contain the path to all the meta-layers which will be needed to build the kernel image. For more information see the bblayers.conf file.

7. local.conf will have the configuration variables which needs to be configured according to the target machine.

8. Edit the both files with the necessary information.

9. Now use the bitbake engine to build the image. Execute the bitbake command inside the build directory.
	- example: "bitbake core-image-minimal" "bitbake core-image-full-cmdline" etc. 
Choose the command acoording to the needs, for exampple core-image-minimal is a basic image and core-image-full-cmdline is a full image and have additional tools like openssh extra.

10. Baking all the bits to create the image will take lots of time and internet. Make sure the internet is not blocking some of the repos.

11. When the bitbake is done, there will be many directories which will be created inside conf.

12. One of them is "tmp" which will inhabit the image created "/tmp/deploy/images". The image will be created in many formats. What I need is .wic file which will zipped with xz or bz2 or etc.

13. Along with this there will be .wic.bz2 file which will be a symbolic link to the original wic.bz2 file. So make sure to find the original file using the following command:
	- command: readlink -f <image-filename>.wic.bz2
	
14. When the original file is found, copy it to another folder and then unzip it.

15. Time to flash the .wic image to the sd card, no need to partition the sd card the .wic has it all. The following command can be used to flash it using dd utility:
	- command: sudo dd if=<.wic file> of=/dev/sdx(sdcard) bs=4M status=progress conv=fsync
	- Here, sudo is run as root obviously
	- dd is raw data copy tool
	- if=... is input file
	- of=... is output device/target block device (sd card)
	- bs=4M is block size 4 megabytes, larger block size = faster copy
	- status=progress is show live copy progress otherwise it will be done silently with no output
	- conv=fsync is force flush to disk after writing
	
16. Take precaution into /dev/sdx otherwise it'll erase our own disk. Run the command lsblk before inserting sd card to host and then run the lsblk once again after inserting the sd card to host, for clear identification of the disk.

17. Once it's done plug the sd card to raspberrypi, connect to internet and then boot.


Command/tool to add meta layers without going to edit bb layer file:-
bitbake-layer add-layers ../<path to the meta layer dir>/<neta-layer>
