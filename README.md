# Linux Notes

*This content is based on the [Essentials of Linux System Administration - LFS201](https://training.linuxfoundation.org/training/essentials-of-linux-system-administration/) course content.*

**Creation Date :** 23.03.2022 - **Author :** Hasan Guzelmansur

---

## Contents

1. [Linux Filesystem Tree Layout](#linux-filesystem-tree-layout)
    - [/home directory](#home-directory)
    - [/bin directory](#bin-directory)
    - [/sbin directory](#sbin-directory)
    - [/usr directory](#usr-directory)
    - [/etc directory](#etc-directory)
    - [/opt directory](#opt-directory)
    - [/snap directory](#snap-directory)
    - [/lib /lib32 /lib64 directories](#lib-lib32-lib64-directories)
    - [/var directory](#var-directory)
    - [/media directory](#media-directory)
    - [/mnt directory](#mnt-directory)
    - [/boot directory](#boot-directory)
    - [/sys directory](#sys-directory)
    - [/dev directory](#dev-directory)
    - [/proc directory](#proc-directory)
    - [/run directory](#run-directory)
    - [/tmp directory](#tmp-directory)
    - [/root directory](#root-directory)
    - [/srv directory](#srv-directory)
    - [/cdrom directory](#cdrom-directory)
2. [Processes](#processes)
3. [Signals](#signals)
4. [Package Management Systems](#package-management-systems)

---

## Linux Filesystem Tree Layout

- Linux file system follows a tree-like hierarchical structure starting at the root. This structure follows a standard layout recommended by Filesystem Hierarchy Standard (FHS), which is a standard maintained by the Linux Foundation.
- Tree-like structure of the root directory *command :* `tree -d -L 1`
- Unix-like systems intend to keep things simple and treat every thing as a sequence of bytes. These sequence of bytes are known as files to the OS.
- Hardware devices are also files.
- Unlike Windows which has multiple roots, the Linux only allows one root.
- The root directory is where all other directories and files on the system reside and is designated by a forward slash /.
- Linux is a multi-user environment so each user is assigned a specific directory that is accessible only to them and the system administrator.

### /home directory

- The **home directory**, also known as the login directory, is where each user stores their personal files and documents.
- The **home directory** contains your **personal configuration** files, the so-called leading dot files.
- Files in home directory are usually hidden, and need `ls` with `-a` option to view them.
- If there is a conflict between personal and system-wide configuration files, the settings in the personal files have priority.
- **home directory**, directly can grow very large as you store your files, downloads, applications, videos, pictures and sounds.
- **home directory** is the only directory that you can write on without the privileged access of root user. For the other directories, although you can read most of them without root access, you can’t modify and write on them.

### /bin directory

- **bin** is short for **binary**. This is where Linux keeps its basic programs and applications.
- Binary files are the executable files that contain compiled source code.
- Almost all basic Linux commands can be found here such `ls`, `mount`, `touch`, `pwd`, `rm`, `echo`, `grep`, `hostname` etc.
- The binaries on this directory must be available in order to attain minimal functionality for the purposes of booting and repairing a system.

### /sbin directory

- **sbin** is short for **system binary**. Similar to bin, it is also a place for storing executable programs. These executable programs are essential for system configuration, maintenance and administrative tasks.
- **sbin directory** is reserved for programs essential for booting, restoring and recovering.
- For example, `fsck`, a filesystem check and repair utility, or `reboot`, restart the system.
- Because of the need for privileged access, the directory is not part of default `$PATH` environmental variables. `$PATH` contains the paths where ordinary users search to look up their binaries and there is no need for root access to search these paths.

### /usr directory

- **usr** stands for Unix System Resources. It belongs to the user applications as opposed to /bin or /sbin directories which belong to system applications.
- Any application installed here is considered nonessential for basic system operation.
- **usr directory** is read-only and applications should not write anything into it.
- The **usr directory** has 7 different subdirectories :
    - /usr/bin contains the vast majority of binaries on your system. Binaries in this directory have a wide range of applications. For example, `vi`, `firefox`, `atom`, `gcc`, `curl`, `dpkg` etc. are all here. 
    - /usr/sbin contains programs for administrative tasks. They need privileged access. Similar to /sbin, they are not part of `$PATH`.
    - /usr/lib contains program libraries. Libraries are collections of frequently used program routines.
    - /usr/local contains self-compiled or third-party programs. This directory is similar in structure to the parent /usr/ directory and is recommended to be used by the system administrator when installing software locally.
    - /usr/src contains kernel sources, header-files and documentation.
    - /usr/include contains all header files necessary for compiling user space source code
    - /usr/share contains shareable, architecture-independent files (e.g. docs, icons, fonts, etc).

### /etc directory

- This is where all system-wide configurations are stored.
- For example, configurations like `python`, `python3`, `ssh`, `firefox` etc. are found here.

### /opt directory

- **opt** stands for optional. This is where manually installed software reside.

### /snap directory

- This directory is where your snap packages are stored. Snap packages are self-contained packages that don't depend on any other packages, dependencies, or libraries.   

### /lib /lib32 /lib64 directories

- **lib** stands for library. These are library files directories. These library files are programs that are shared among other binary applications.
- Binary files inside bin and sbin use these library files extensively.
- If you want to know which command uses which library files, you can trace them using `strace` command.

### /var directory

- **var** stands for variables. This directory contains variable data like system logging files, mail and printer spool directories, and transient and temporary files.
- These are typically file and directories that are expected to grow in size.
- For example, **/var/crash** holds information about every time a process has crashed or **/var/log** has all log files for your computer and its applications, which grow constantly with time.

### /media directory

- This is where your OS automatically mount your external removable devices such USB thumb drive.

### /mnt directory

- This is where you can mount external devices manually. It can be floppy disk, external hard drives, network drive, etc.
- /media and /mnt are basically the same; however, it is recommended to use /mnt for manual mounting and leave media directory for the OS.

### /boot directory

- This is where boot loader lives. It contains the static bootloader, kernel executable and configuration files required to boot a computer.

### /sys directory

- This is where can interact with the kernel. In other words, you can consider **sys** as an interface to the kernel.
- This directory is a virtual filesystem, which means the files live on memory and disappear after shutdown.
- In essence /sys allows you to get information about the system and its components in a structured way.

### /dev directory

- This is where your device files reside. These files allow application programs to interact with your hardware devices.
- A few examples of device files :
    - first hard disk: /dev/sda (its first partition is /dev/sda1)
    - printer: /dev/lp0
    - Computer memory: /dev/mem
    - A terminal (keyboard or screen): /dev/tty
- **/sys** and **/dev** are both related to devices, so they might be confusing. However, they are very different. **/sys** directory has files that provide information about devices. Information such as whether they are powered on, their vendor name, model, etc. On the other hand, **/dev** directory has files that allow programs to access the devices (write data to a serial port, read a hard disk, etc).

### /proc directory

- **proc** stands for process. On this directory, you can find pseudo-files containing information about system processes and resources.
- Every process on your computer would have a folder with information about that process on this directory.
- This directory is a virtual filesystem and disappears once the the computer is shut down.
- 

### /run directory

- Modern Linux distributions have included this directory as a temporary filesystem (tmpfs) which stores RAM runtime data. That means that daemons like systemd and udev, which are started very early in the boot process (and perhaps before /var/run was available) have a standardized filesystem location available where they can store runtime information. Since files on this directory are stored on RAM,they disappear after shutdown.

- /run/udev is a device manager for the Linux kernel, and /run/systemd is a suite of basic building blocks that provide a standard process for controlling what programs run when the system boots up.

### /tmp directory

- This is where applications store the temporary files they need during a session.
- For example, when you are writing a word document in a word processor, it stores a temporary file saving all you write. If for some reason your system crashes before saving the file, the word processor can search this directory to find a recent saved copy to recover your text. This directory is usually empty when you reboot your system.

### /root directory

- This is a directory for superuser (i.e. administrator). You can look at it as a root user’s home directory. It is only accessible to superuser. It is reserved for configuration files for the root account.

### /srv directory
 
- It is a service directory. If you are running a web server, you can store site-specific data on this folder.

### /cdrom directory

- This is a legacy directory to mount CD-ROM. However, today CD-ROMs are automatically mounted on media directory.

**Resources**

- [Linux File System/Structure Explained!](https://www.youtube.com/watch?v=HbgzrKJvDRw)
- [Demystifying Linux 101: File System](https://kasun-bandara.medium.com/demystifying-linux-101-file-system-and-the-permission-conflict-e04588843ff2)
- [Linux File System 101](https://medium.com/swlh/linux-file-system-101-894141449257#:~:text=Linux%20file%20system%20follows%20a%20tree%2Dlike%20hierarchical%20structure%20starting,maintained%20by%20the%20Linux%20Foundation.)
- [A source about file systems reflection on the physical hard drive](https://medium.com/consonance/file-systems-an-in-depth-intro-75de31a0e50a)
- [Unix/Linux File Systems](http://teaching.idallen.com/cst8207/13w/notes/450_file_system.html)

## Processes

## Signals

## Package Management Systems
