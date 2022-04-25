# Linux Notes

*This content is based on the [Essentials of Linux System Administration - LFS201](https://training.linuxfoundation.org/training/essentials-of-linux-system-administration/) course content.*

**Creation Date :** 23.03.2022 - **Author :** Hasan Guzelmansur

---

## Contents

1. [Linux Filesystem Tree Layout](#linux-filesystem-tree-layout)
2. [Processes](#processes)
    - [Background Process](#background-process)
    - [Foreground Process](#foreground-process)
    - [Stopped-CTRL+Z](#stopped-ctrlz)
    - [Jobs](#jobs)
    - [Daemons](#daemons)
    - [The Init Process](#the-init-process)
    - [States of a Process in Linux](#states-of-a-process-in-linux)
3. [Signals](#signals)
4. [Package Management Systems](#package-management-systems)
    - [RPM-Based Package Managers](#rpm-based-package-managers)
    - [Debian-Based Package Managers](#debian-based-package-managers)
    - [Arch-Based Package Managers](#arch-based-package-managers)
5. [System Monitoring](#system-monitoring)
    - [sar](#sar)
    - [atop](#atop)
    - [VmStat - Virtual Memory Statistics](#vmstat---virtual-memory-statistics)
    - [Other Command Line Tools to Monitor Linux Performance (for System and Network)](#other-command-line-tools-to-monitor-linux-performance-for-system-and-network)
6. [Process Monitoring](#process-monitoring)
    - [TOP](#top)
    - [ps](#ps)
    - [Htop - Linux Process Monitoring](#htop---linux-process-monitoring)
    - [Lsof - List Open Files](#lsof---list-open-files)
    - [monit](#monit)
7. [Memory: Monitoring Usage and Tuning](#memory-monitoring-usage-and-tuning)
    - [Profiling Application Memory Usage with Valgrind](#profiling-application-memory-usage-with-valgrind)
8. [I/O Monitoring and Tuning](#io-monitoring-and-tuning)
9. [I/O Scheduling](#io-scheduling)
10. [Linux Filesystems and the VFS](#linux-filesystems-and-the-vfs)
11. [Disk Partitioning](#disk-partitioning)
12. [Th ext2/ext3/ext4 Filesystems](#th-ext2ext3ext4-filesystems)
---

## Linux Filesystem Tree Layout

- Linux file system follows a tree-like hierarchical structure starting at the root. This structure follows a standard layout recommended by Filesystem Hierarchy Standard (FHS), which is a standard maintained by the Linux Foundation.
- Tree-like structure of the root directory *command :* `tree -d -L 1`
- Unix-like systems intend to keep things simple and treat every thing as a sequence of bytes. These sequence of bytes are known as files to the OS.
- Hardware devices are also files.
- Unlike Windows which has multiple roots, the Linux only allows one root.
- The root directory is where all other directories and files on the system reside and is designated by a forward slash `/`.
- Linux is a multi-user environment so each user is assigned a specific directory that is accessible only to them and the system administrator.

**/home directory**

- The **home directory**, also known as the login directory, is where each user stores their personal files and documents.
- The **home directory** contains your **personal configuration** files, the so-called leading dot files.
- Files in home directory are usually hidden, and need `ls` with `-a` option to view them.
- If there is a conflict between personal and system-wide configuration files, the settings in the personal files have priority.
- **home directory**, directly can grow very large as you store your files, downloads, applications, videos, pictures and sounds.
- **home directory** is the only directory that you can write on without the privileged access of root user. For the other directories, although you can read most of them without root access, you can’t modify and write on them.

**/bin directory**

- **bin** is short for **binary**. This is where Linux keeps its basic programs and applications.
- Binary files are the executable files that contain compiled source code.
- Almost all basic Linux commands can be found here such `ls`, `mount`, `touch`, `pwd`, `rm`, `echo`, `grep`, `hostname` etc.
- The binaries on this directory must be available in order to attain minimal functionality for the purposes of booting and repairing a system.

**/sbin directory**

- **sbin** is short for **system binary**. Similar to bin, it is also a place for storing executable programs. These executable programs are essential for system configuration, maintenance and administrative tasks.
- **sbin directory** is reserved for programs essential for booting, restoring and recovering.
- For example, `fsck`, a filesystem check and repair utility, or `reboot`, restart the system.
- Because of the need for privileged access, the directory is not part of default `$PATH` environmental variables. `$PATH` contains the paths where ordinary users search to look up their binaries and there is no need for root access to search these paths.

**[Go to Top](#contents)**

**/usr directory**

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

**/etc directory**

- This is where all system-wide configurations are stored.
- For example, configurations like `python`, `python3`, `ssh`, `firefox` etc. are found here.

**/opt directory**

- **opt** stands for optional. This is where manually installed software reside.

**/snap directory**

- This directory is where your snap packages are stored. Snap packages are self-contained packages that don't depend on any other packages, dependencies, or libraries.   

**/lib /lib32 /lib64 directories**

- **lib** stands for library. These are library files directories. These library files are programs that are shared among other binary applications.
- Binary files inside bin and sbin use these library files extensively.
- If you want to know which command uses which library files, you can trace them using `strace` command.

**/var directory**

- **var** stands for variables. This directory contains variable data like system logging files, mail and printer spool directories, and transient and temporary files.
- These are typically file and directories that are expected to grow in size.
- For example, **/var/crash** holds information about every time a process has crashed or **/var/log** has all log files for your computer and its applications, which grow constantly with time.

**/media directory**

- This is where your OS automatically mount your external removable devices such USB thumb drive.

**/mnt directory**

- This is where you can mount external devices manually. It can be floppy disk, external hard drives, network drive, etc.
- /media and /mnt are basically the same; however, it is recommended to use /mnt for manual mounting and leave media directory for the OS.

**/boot directory**

- This is where boot loader lives. It contains the static bootloader, kernel executable and configuration files required to boot a computer.

**/sys directory**

- This is where can interact with the kernel. In other words, you can consider **sys** as an interface to the kernel.
- This directory is a virtual filesystem, which means the files live on memory and disappear after shutdown.
- In essence /sys allows you to get information about the system and its components in a structured way.

**[Go to Top](#contents)**

**/dev directory**

- This is where your device files reside. These files allow application programs to interact with your hardware devices.
- A few examples of device files :
    - first hard disk: /dev/sda (its first partition is /dev/sda1)
    - printer: /dev/lp0
    - Computer memory: /dev/mem
    - A terminal (keyboard or screen): /dev/tty
- **/sys** and **/dev** are both related to devices, so they might be confusing. However, they are very different. **/sys** directory has files that provide information about devices. Information such as whether they are powered on, their vendor name, model, etc. On the other hand, **/dev** directory has files that allow programs to access the devices (write data to a serial port, read a hard disk, etc).

**/proc directory**

- **proc** stands for process. On this directory, you can find pseudo-files containing information about system processes and resources.
- Every process on your computer would have a folder with information about that process on this directory.
- This directory is a virtual filesystem and disappears once the the computer is shut down.

**/run directory**

- Modern Linux distributions have included this directory as a temporary filesystem (tmpfs) which stores RAM runtime data. That means that daemons like systemd and udev, which are started very early in the boot process (and perhaps before /var/run was available) have a standardized filesystem location available where they can store runtime information. Since files on this directory are stored on RAM,they disappear after shutdown.

- /run/udev is a device manager for the Linux kernel, and /run/systemd is a suite of basic building blocks that provide a standard process for controlling what programs run when the system boots up.

**/tmp directory**

- This is where applications store the temporary files they need during a session.
- For example, when you are writing a word document in a word processor, it stores a temporary file saving all you write. If for some reason your system crashes before saving the file, the word processor can search this directory to find a recent saved copy to recover your text. This directory is usually empty when you reboot your system.

**/root directory**

- This is a directory for superuser (i.e. administrator). You can look at it as a root user’s home directory. It is only accessible to superuser. It is reserved for configuration files for the root account.

**/srv directory**
 
- It is a service directory. If you are running a web server, you can store site-specific data on this folder.

**/cdrom directory**

- This is a legacy directory to mount CD-ROM. However, today CD-ROMs are automatically mounted on media directory.

**Resources**

- [Linux File System/Structure Explained!](https://www.youtube.com/watch?v=HbgzrKJvDRw)
- [Demystifying Linux 101: File System](https://kasun-bandara.medium.com/demystifying-linux-101-file-system-and-the-permission-conflict-e04588843ff2)
- [Linux File System 101](https://medium.com/swlh/linux-file-system-101-894141449257#:~:text=Linux%20file%20system%20follows%20a%20tree%2Dlike%20hierarchical%20structure%20starting,maintained%20by%20the%20Linux%20Foundation.)
- [A source about file systems reflection on the physical hard drive](https://medium.com/consonance/file-systems-an-in-depth-intro-75de31a0e50a)
- [Unix/Linux File Systems](http://teaching.idallen.com/cst8207/13w/notes/450_file_system.html)

**[Go to Top](#contents)**

## Processes

- In Linux, a process is any active (running) instance of a program. 
- It is made up of the program instruction, data read from files, other programs or input from a system user.
- A program is any executable file held in storage on your machine. Anytime you run a program, you have created a process.
- The most accurate way to identify a process is by process ID (PID).

### Background Process

- **Background processes** (also referred to as non-interactive/automatic processes) – are processes not connected to a terminal; they don’t expect any user input.
- To list and manage background jobs, we will use the `bg` command.

### Foreground Process

- **Foreground processes** (also referred to as interactive processes) – these are initialized and controlled through a terminal session. In other words, there has to be a user connected to the system to start such processes; they haven’t started automatically as part of the system functions/services
- `fg` command brings the most recently run job/process to the foreground.
- `fg <Process Name>` Use the fg command again, but select a specific job to bring to the foreground.

### Stopped-CTRL+Z

- If you press Control + Z for a foreground job, or enter the stop command for a background job, the job stops. This job is called a stopped job.

### Jobs

- Job control is the ability to stop/suspend the execution of processes and continue/resume their execution as per your requirements.
- The jobs command is part of your operating system and shell, such as bash/ksh or POSIX shell.
- **Syntax :** 
```

jobs
jobs jobID
jobs [options] jobID

```
- Use the `type` command or `command` command to find out if it a shell builtin. For instance, open the terminal application and then type:
```
type -a jobs
command -V jobs
```
|Option|Description|
|------|-----------|
|`-l`|Show process IDs in addition to the normal information.|
|`-p`|Show process IDs only.|
|`-n`|Show only processes that have changed status since the last notification are printed.|
|`-r`|Restrict output to running jobs only.|
|`-s`|Restrict output to stopped jobs only.|
|`-x`|COMMAND is run after all job specifications that appear in ARGS have been replaced with the process ID of that job’s process group leader.|

**[Go to Top](#contents)**

### Daemons

- These are special types of background processes that start at system startup and keep running forever as a service; they don’t die.
- They are started as system tasks (run as services), spontaneously. However, they can be controlled by a user via the init process.

**Creation of a Processes in Linux**

- A new process is normally created when an existing process makes an exact copy of itself in memory. The child process will have the same environment as its parent, but only the process ID number is different.
- There are two conventional ways used for creating a new process in Linux:
    - Using The **System()** Function – this method is relatively simple, however, it’s inefficient and has significantly certain security risks.
    - Using **fork()** and **exec()** Function – this technique is a little advanced but offers greater flexibility, speed, together with security.
- **Parent processes** – these are processes that create other processes during run-time.
- **Child processes** – these processes are created by other processes during run-time.

### The Init Process

- Init process is the mother (parent) of all processes on the system, it’s the first program that is executed when the Linux system boots up; it manages all other processes on the system.
-  It is started by the kernel itself, so in principle it does not have a parent process.
- The init process always has process ID of 1
- You can use the `pidof` command to find the ID of a process.

**Starting a Process in Linux**

- Once you run a command or program, it will start a process in the system. You can start a foreground (interactive) process as follows, it will be connected to the terminal and a user can send input it.

- To start a process in the background (non-interactive), use the & symbol, here, the process doesn’t read input from a user until it’s moved to the foreground.

**[Go to Top](#contents)**

### States of a Process in Linux

- **Running** – here it’s either running (it is the current process in the system) or it’s ready to run (it’s waiting to be assigned to one of the CPUs).
- **Waiting** – in this state, a process is waiting for an event to occur or for a system resource. Additionally, the kernel also differentiates between two types of waiting processes; interruptible waiting processes – can be interrupted by signals and uninterruptible waiting processes – are waiting directly on hardware conditions and cannot be interrupted by any event/signal.
- **Stopped** – in this state, a process has been stopped, usually by receiving a signal. For instance, a process that is being debugged.
- **Zombie** – here, a process is dead, it has been halted but it’s still has an entry in the process table.

**The meaning of the STAT code is in the table below :**

|STAT Code| Description|
|---------|------------|
|`R`|Running or runnable (either executing or about to run).|
|`D`|Uninterruptible sleep (waiting) - usually waiting for input or output to complete.|
|`S`|Sleeping. Usually waiting for an event to occur, such as a signal or input to become available.|
|`T`|Stopped. Usually stopped by shell job control or the process is under the control of a debugger.|
|`Z`|Defunct or zombie process.|
|`N`|Low priority task, nice.|
|`W`|Paging.|
|`s`|The process is a session leader.|
|`+`|The process is in the foreground process group.|
|`\|`|The process is multithreaded.|
|`<`|High priority task.|

**List Processes**

- To display your currently active processes, use the `ps` command :

```
dev@tau:~$ ps

PID  TTY         TIME CMD
1723 pts/0   00:00:00 bash   
1769 pts/0   00:00:00 sleep
2155 pts/0   00:00:00 ps

```

- PID : Unique process ID
- TIME: Amount of time that the process has been running
- CMD : The command executed to launch the process

**Verbose List Processes**

- To see an incredibly detailed list of processes, use the `ps aux` command.
    - a -> all users
    - u -> shows the user/owner
    - x -> displays processes not executed in the terminal 

**Kill by PID**

- Inevitably, a process will get hung, and you will need to `kill` it. 
- The more time you spend at the CLI (command line interface), the more likely it is you will need the `kill` command.
- Syntax : `dev@tau:~$ kill <PID> `
- This command sends the **SIGTERM** signal. 
- If you are dealing with a stuck process, add the -9 option.
    - *example :* `dev@tau:~$ kill -9 1769` 

**Kill by name/keyword**

- Use the `killall` command to kill a process by name. 
- This command will kill all processes with the keyword/name that specify.
- Syntax : `dev@tau:~$ killall sleep`

**Resources**

- [Linux Command Basics: 7 commands for process management](https://www.redhat.com/sysadmin/linux-command-basics-7-commands-process-management#:~:text=In%20Linux%2C%20a%20process%20is,you%20have%20created%20a%20process)
- [Processes in Linux/Unix](https://www.geeksforgeeks.org/processes-in-linuxunix/)
- [Understanding the job control commands in Linux – bg, fg and CTRL+Z](https://www.thegeekdiary.com/understanding-the-job-control-commands-in-linux-bg-fg-and-ctrlz/)
- [Process Control Commands in Unix/Linux](https://www.geeksforgeeks.org/process-control-commands-unixlinux/)
- [Linux / Unix: jobs Command Examples](https://www.cyberciti.biz/faq/unix-linux-jobs-command-examples-usage-syntax/)
- [All You Need To Know About Processes in Linux](https://www.tecmint.com/linux-process-management/)
- [LINUX PROCESSES AND SIGNALS](https://www.bogotobogo.com/Linux/linux_process_and_signals.php)
- [Kill All Stopped Jobs Linux](https://linuxhint.com/kill-all-stopped-jobs-linux/)

**[Go to Top](#contents)**

## Signals

- **Signal** is a notification, a message sent by either operating system or some application to our program.
- Signals are a mechanism for one-way asynchronous notifications.
- A signal may be sent from the kernel to a process, from a process to another process, or from a process to itself.
- Signal typically alert a process to some event, such as a segmentation fault, or the user pressing Ctrl-C.
- Linux kernel implements about 30 signals. Each signal identified by a number, from 1 to 31. Signals don't carry any argument and their names are mostly self explanatory.
- Signals can :
    1. accept the default action, which may be to terminate the process, terminate and coredump the process, stop the process, or do nothing, depending on the signal.
    2. Or, processes can elect to explicitly ignore or handle signals.
        - Ignored signals are silently dropped.
        - Handled signals cause the execution of a user-supplied signal handler function. The program jumps to this function as soon as the signal is received, and the control of the program resumes at the previously interrupted instructions.

**Commonly used signals :**

|Signal Name|Signal Number|Description|
|-----------|-------------|-----------|
|SIGHUP|1|Hang up detected on controlling terminal or death of controlling process|
|SIGINT|2|Issued if the user sends an interrupt signal (Ctrl + C)|
|SIGQUIT|3|Issued if the user sends a quit signal (Ctrl + D)|
|SIGFPE|8|Issued if an illegal mathematical operation is attempted|
|SIGKILL|9|If a process gets this signal it must quit immediately and will not perform any clean-up operations|
|SIGALRM|14|Alarm clock signal (used for timers)|
|SIGTERM|15|Software termination signal (sent by kill by default)|

- The term **raise** is used to indicate the generation of a signal, and the term **catch** is used to indicate the receipt of a signal.
- **Signals are raised by error conditions, and they are generated by the shell and terminal handlers to cause interrupts and can also be sent from one process to another to pass information or to modify the behavior.**

**Resources**

- [The Linux System Administrator's Guide](https://tldp.org/LDP/sag/html/sag.html)
- [Unix / Linux - Signals and Traps](https://www.tutorialspoint.com/unix/unix-signals-traps.htm)
- [LINUX PROCESSES AND SIGNALS](https://www.bogotobogo.com/Linux/linux_process_and_signals.php)

**[Go to Top](#contents)**

## Package Management Systems

- **Packages** collect multiple data files together into a single archive file for easier portability and storage, or simply compress files to reduce storage space.
- A **package manager** or **package-management system** is a collection of software tools that automates the process of installing, upgrading, configuring, and removing computer programs for a computer in a consistent manner.
- A package manager deals with packages, distributions of software and data in archive files. Packages contain metadata, such as the software's name, description of its purpose, version number, vendor, checksum, and a list of dependencies necessary for the software to run properly.
- Package managers typically maintain a database of software dependencies and version information to prevent software mismatches and missing prerequisites.
- Package managers are designed to eliminate the need for manual installs and updates.
- Several flavors of Linux have created their own package formats. Some of the most commonly used package formats include:
    - .tar.xz: While it is just a compressed tarball, this is the format that Arch Linux uses.
    - .deb: This package format is used by Debian, Ubuntu, Linux Mint, and several other derivatives. It was the first package type to be created.
    - .rpm: This package format was originally called Red Hat Package Manager. It is used by Red Hat, Fedora, SUSE, and several other smaller distributions.
- A **software repository** is a central place to keep resources that users can pull from when necessary. One example is software repositories for Linux distributions that help to support those who are using this open-source software to run hardware systems :
    - Arch Linux with aurman 
    - CentOS 7 using YUM 
    - Ubuntu using APT 
- **Package managers** are used to interact with **software repositories**.

### RPM-Based Package Managers (Red Hat Package Manager)

- The current versions of **yum** (for enterprise distributions) and **DNF** (for community) combine several open source projects to provide their current functionality.
- Its primary use is to install RPMs, which you have locally, not to search software repositories. The package manager named **up2date** was created to inform users of updates to packages and enable them to search remote repositories and easily install dependencies.
- Yellowdog Updater (YUP) was developed in 1999-2001 by folks at Terra Soft Solutions as a back-end engine for a graphical installer of Yellow Dog Linux. Duke University liked the idea of YUP and decided to improve upon it. They created Yellowdog Updater, Modified (yum) which was eventually adapted to help manage the university's Red Hat Linux systems. Today, almost every distribution of Linux that uses RPMs uses yum for package management.

**[Go to Top](#contents)**

**Zypper**

- Zypper is another package manager meant to help manage RPMs.
- This package manager is most commonly associated with **SUSE** (and openSUSE) but has also seen adoption by **MeeGo**, **Sailfish OS**, and **Tizen**.
- Zypper is used as the back end for the system administration tool YaST and some users find it to be faster than yum.

### Debian-Based Package Managers

- They use .deb packages, which can be managed by a tool called dpkg. dpkg is very similar to rpm in that it was designed to manage packages that are available locally.
- It does no dependency resolution, and has no reliable way to interact with remote repositories. 
- In order to improve the user experience and ease of use, the Debian project commissioned a project called Deity. This codename was eventually abandoned and changed to Advanced Package Tool (APT).
- It makes use of repositories in a similar fashion to RPM-based systems, but instead of individual `.repo` files that `yum` uses, `apt` has historically used `/etc/apt/sources.list` to manage repositories. More recently, it also ingests files from `/etc/apt/sources.d/`.
- Debian does not support PPAs(Personal Package Archive) natively.
- Note that most of the current tutorials for Ubuntu specifically have taken to simply using apt. The single apt command was designed to implement only the most commonly used commands in the APT arsenal.
- Since functionality is split between apt-get, apt-cache, and other commands, apt looks to unify these into a single command.

**[Go to Top](#contents)**

### Arch-Based Package Managers

- Arch Linux uses a package manager called `pacman`. 
- Unlike `.deb` or `.rpm` files, pacman uses a more traditional `tarball` with the LZMA2 compression (.tar.xz). This enables Arch Linux packages to be much smaller than other forms of compressed archives (such as gzip). 
- One of the major benefits of pacman is that it supports the Arch Build System, a system for building packages from source.
-  The build system ingests a file called a PKGBUILD, which contains metadata (such as version numbers, revisions, dependencies, etc.) as well as a shell script with the required flags for compiling a package conforming to the Arch Linux requirements. The resulting binaries are then packaged into the aforementioned .tar.xz file for consumption by pacman.
- This system led to the creation of the Arch User Repository (AUR) which is a community-driven repository containing PKGBUILD files and supporting patches or scripts. This allows for a virtually endless amount of software to be available in Arch.
- Advantage of this system is that if a user wishes to make software available to the public, they do not have to go through official channels to get it accepted in the main repositories.
- The downside is that it relies on community curation similar to Docker Hub, Canonical's Snap packages, or other similar mechanisms.
- There are numerous AUR-specific package managers that can be used to download, compile, and install from the PKGBUILD files in the AUR.

|RPM|YUM|
|---|----|
|- The RPM Package Manager (RPM) is a package management system that runs on RHEL, CentOS, and Fedora. ||
|- building computer software from source into easily distributable packages| - YUM is the primary package management tool for installing, updating, removing, and managing software packages in Red Hat Enterprise Linux.|
|- installing, updating and uninstalling packaged software.|- YUM performs dependency resolution when installing, updating, and removing software packages. |
|- querying detailed information about the packaged software, whether installed or not|- YUM can manage packages from installed repositories in the system or from .rpm packages.|
|- verifying integrity of packaged software and resulting software installation|- The main configuration file for YUM is at `/etc/yum.conf`, and all the repos are at `/etc/yum.repos.d`.|
|- By default, root is required to perform all RPM operations.|**Command of YUM**|
|- However, in certain circumstances and in the event of the required database being prepared, any user can also install or configure RPM.|- `yum install` : Installs the specified packages.|
|- Each RPM has a version number and release information.|- `remove` : Removes the specified packages.|
|- To check if a package exists in the RPM database `rpm -q package_name`|- `search` : Searches package metadata for keywords.|
|- To find the information of which package installed any file on the disk `rpm -df /directory`|- `info` : Lists description.|
|- To access information about rpm packages `rpm -qi package_name`|- `update` : Updates each package to the latest version.|
|- `rpm -qp package_name` : Allows the packet file to be queried.|- `repolist` : Lists repositories.|
|- `rpm -qa package_name` : Makes a list of all packages in the RPM database|- `history` : Displays what has happened in past transactions|
|- `rpm -ql package_name` : Lists the files of the package provided.|**commonly-used options with YUM** |
|- `rpm -qc package_name` : Lists the settings files for the package provided.|- `C` : Runs from system cache|
|- `rpm -qd package_name` : Lists the document files for the package provided|- `--security` : Includes packages that provide a fix for a security issue|
|- `rpm --whatrequires package_name` : Lists which packages or packages are connected to the package provided.|- `-y` : Answers yes to all questions|
|**Package integrity check**|- `--skip-broken` : Skips packages causing problems|
|- RPM stores the information of the files installed on the system. With rmp it is also possible to check if these files have been modified. <br> - Syntax : `rpm -V package_name`|- `-V` : Verbose mod|
|- To query if any file of any package has changed `rpm -Va package_name`|- [Yum Command Cheat Sheet](https://access.redhat.com/articles/yum-cheat-sheet)|
|- `S` : It means the file size has changed.|N/A|
|- `M` : It means that the mode or its permissions have changed.|N/A|
|- `5` : It means checksum changed.|N/A|
|- `D` : It means the device major or minor numbers have changed.|N/A|
|- `L` : It means shortcut changed.|N/A|
|- `U` : It means that user ownership has changed.|N/A|
|- `G` : It means that group ownership has changed.|N/A|
|- `T` : It means that the last edit date has changed.|N/A|
|- `.` : It means that the information does not change.|N/A|
|- The -i parameter is used to install RPM. <br> - Syntax : `rpm -i package_name.rpm` |N/A|
|- RPM does not allow installing identical versions of packages with the same name. However, there may be some exceptions to this. Kernel package is one of them.|N/A|
|**Other parameters that can be used with the -i parameter :**|N/A|
|- `-h` : Hash marking (shows the installation level mark)|N/A|
|- `-v` : Verbose mod|N/A|
|- `--test` : It does the setup experimentally only.|N/A|
|- `--force` : It will continue the installation no matter what.|N/A|
|- `--nodeps` : Installation is done regardless of dependencies.|N/A|
|- `--replacefiles` : If any of the same files are written, replace them with a new one.|N/A|
|- If there are interdependent packages, they can all be installed at the same time to resolve their dependency.|N/A|
|- The -U parameter is used to update the package. <br> - Syntax : `rpm -U package_name.rpm`|N/A|
|- The -v, -h, and –test parameters can also be used with the -U parameter.|N/A|
|- Before updating a software, the relevant software's upgrade/downgrade instructions should be reviewed.|N/A|
|- for prints help `-? \| --help`||
---

**[Go to Top](#contents)**

|DPKG|APT|
|---|----|
|- dpkg(1) is the lowest level tool for the Debian package management. |- Repository based package management operations on the Debian system can be performed by many APT-based package management tools available on the Debian system.|
|**While installing package called "package_name", dpkg process it in the following order.**|- 3 basic package management tools: **apt**, **apt-get** / **apt-cache** and **aptitude**.|
| 1. Unpack the deb file ("ar -x" equivalent) |- The **aptitude** command is not recommended for the release-to-release system upgrade on the stable Debian system after the new release.|
| 2. Execute **"package_name.preinst"** using **debconf**|- The **aptitude** command sometimes suggests mass package removals for the system upgrade on the testing or unstable Debian system.|
| 3. Install the package content to the system ("tar -x" equivalent)|- The **apt** command is a high-level commandline interface for package management.|
| 4. Execute "package_name.postinst" using debconf(1)|- **apt** provides a friendly progress bar when installing packages using apt install.|
|**The notable files created by dpkg**|- **apt** will remove cached .deb packages by default after successful installation of downloaded packages.|
|`/var/lib/dpkg/info/package_name.conffiles` : list of configuration files. (user modifiable)|- Users are recommended to use the new apt(8) command for interactive usage and use the apt-get(8) and apt-cache(8) commands in the shell script.|
|`/var/lib/dpkg/info/package_name.list` : list of files and directories installed by the package|**Basic package management operations with the commandline using apt**|
|`/var/lib/dpkg/info/package_name.md5sums` : list of MD5 hash values for files installed by the package|`apt update` : update package archive metadata|
|`/var/lib/dpkg/info/package_name.preinst` : package script to be run before the package installation|`apt install foo` : 	install candidate version of "foo" package with its dependencies.|
|`/var/lib/dpkg/info/package_name.postinst`: package script to be run after the package installation|`apt upgrade` : 	install candidate version of installed packages without removing any other packages|
|`/var/lib/dpkg/info/package_name.prerm` : package script to be run before the package removal|`apt full-upgrade` : install candidate version of installed packages while removing other packages if needed|
|`/var/lib/dpkg/info/package_name.postrm` : package script to be run after the package removal| `apt remove foo` : remove "foo" package while leaving its configuration files|
|`/var/lib/dpkg/info/package_name.config` : package script for debconf system|`apt autoremove` : remove auto-installed packages which are no longer required|
|`/var/lib/dpkg/alternatives/package_name` : the alternative information used by the update-alternatives command|`apt purge foo` : purge "foo" package with its configuration files|
|`/var/lib/dpkg/available` : the availability information for all the package|`apt clean` : 	clear out the local repository of retrieved package files completely|
|`/var/lib/dpkg/diversions` : the diversions information used by dpkg(1) and set by dpkg-divert|`apt autoclean` : clear out the local repository of retrieved package files for outdated packages|
|`/var/lib/dpkg/statoverride` : the stat override information used by dpkg and set by dpkg-statoverride|`apt show foo` : display detailed information about "foo" package|
|`/var/lib/dpkg/status` : the status information for all the packages|`apt search regex` : search packages which match regex|
|`/var/lib/dpkg/status-old` : the first-generation backup of the "var/lib/dpkg/status" file|N/A|
|`/var/backups/dpkg.status*` : the second-generation backup and older ones of the "var/lib/dpkg/status" file|N/A|
|- The dpkg-statoverride command : Stat overrides provided by the dpkg-statoverride(8) command are a way to tell dpkg(1) to use a different owner or mode for a file when a package is installed.|N/A|
|- The dpkg-divert command : File diversions provided by the dpkg-divert(8) command are a way of forcing dpkg(1) not to install a file into its default location, but to a diverted location. |N/A|
|- `dpkg --configure -a` : configure all partially installed packages with the following command.|N/A|

**[Go to Top](#contents)**

**References**

- [RPM Packaging Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/rpm_packaging_guide/getting-started-with-rpm-packaging)
- [Debian package management](https://www.debian.org/doc/manuals/debian-reference/ch02.en.html#_debian_package_management_prerequisites)
- [Package Management - Ubuntu](https://ubuntu.com/server/docs/package-management)
- [Introduction to Package Management in Linux](https://linuxhint.com/package-management-linux-introduction/)
- [List of software package management systems](https://en.wikipedia.org/wiki/List_of_software_package_management_systems)
- [TAR](https://en.wikipedia.org/wiki/Tar_(computing))
- [Package manager](https://en.wikipedia.org/wiki/Package_manager)
- [The evolution of package managers](https://opensource.com/article/18/7/evolution-package-managers)
- [Software Repository](https://www.techopedia.com/definition/32890/software-repository)
- [RPM Package Manager](https://en.wikipedia.org/wiki/RPM_Package_Manager)
- [Mastering Package Management system with Dpkg](https://www.tutorialspoint.com/mastering-package-management-system-with-dpkg#:~:text=Dpkg%20is%20a%20device%20to,the%20action%20is%20some%20way.)
- [RPM Command in Linux](https://linuxize.com/post/rpm-command-in-linux/)
- [dpkg(1) — Linux manual page](https://man7.org/linux/man-pages/man1/dpkg.1.html)
- [rpm(8) — Linux manual page](https://man7.org/linux/man-pages/man8/rpm.8.html)
- [yum(8) — Linux manual page](https://man7.org/linux/man-pages/man8/yum.8.html#COMMANDS)
- [APT NASIL](https://wiki.ubuntu-tr.net/index.php?title=Apt_nas%C4%B1l-2)

## System Monitoring

- A system monitor is a program or piece of hardware that monitors various aspects of a computer system and then displays information regarding the status of that system. This sort of monitor typically takes the form of a software program provided with an operating system (OS) or used as a standalone program. Hardware system monitors are also available, though these are fairly specialized devices and not as frequently used as software monitors. A system monitor will typically track various aspects of a computer system, including what programs are running, how resources are being used, and certain details regarding the hardware installed on a computer.

### sar

- **sar** command used to collect, report, and save system activity information.
- sar (System Activity Report) is a system utility command used to collect and report different metrics such us system load, CPU activity, memory (sar -r), paging (sar -B), swap (sar -S), disk (sar -d), device load and network. 
- It is extremely useful in analyzing current and recent recorded system performance.
- Most Linux distributions provide sar utility binary in the sysstat package.

**[Go to Top](#contents)**

### atop

- **atop** is a very powerful and an interactive monitor to view the load on a Linux system.

**atop Commands**

- `# atop -a` : To see active processes only
- `# atop -y` : Display individual threads
- `# atop -1` : Display average-per-second i.s.o. total values
- `# atop -m` : Display memory-related process-info
- `# atop -d` : Display disk-related process-info
- `# atop -n` : Display network-related process-info
- `# atop -s`: Display scheduling-related process-info
- `# atop -v` : Display various process-info (ppid, user/group, date/time)
- `# atop -c` : Display command line per process

**[Go to Top](#contents)**

### VmStat - Virtual Memory Statistics

- Linux VmStat command is used to display statistics of virtual memory, kernel threads, disks, system processes, I/O blocks, interrupts, CPU activity, and much more.
- It displays the most critical hardware resources from a performance point of view.
- Shows CPU, memory, disk and network performance.
- It shows which processes are responsible for the indicated load concerning CPU and memory load on a process level.

*Install VmStat in Linux*

```
$ sudo yum install sysstat      [On Older CentOS/RHEL & Fedora]
$ sudo dnf install sysstat      [On CentOS/RHEL/Fedora/Rocky Linux & AlmaLinux]
$ sudo apt-get install sysstat  [On Debian/Ubuntu & Mint]
$ sudo pacman -S sysstat        [On Arch Linux] 
```

**VmStat Commands**

- `$ vmstat -a` : vmstat command (also known as virtual memory statistic tool) shows information about processes, memory, disk, and CPU activity in Linux. If you run vmstat without parameters it will display a summary report since system boot. The most important fields are **free** under memory and **si**, **so** under the swap column. <br> **Free** – Amount of free/idle memory spaces. <br> **si** – Swapped in every second from disk in KiloBytes. <br> **so** – Swapped out every second to disk in KiloBytes.
- `$ vmstat 2 6`: With this command, vmstat execute every two seconds and stop automatically after executing six intervals.
- `$ vmstat -t 1 5` : vmstat command with `-t` parameter shows timestamps with every line printed as shown below.
- `$ vmstat -s` : vmstat command with `-s` switch displays summary of various event counters and memory statistics.
- `$ vmstat -d` : vmstat with `-d` option display all disks statistics of Linux.
- `$ vmstat -S M 1 5` : The vmstat displays memory statistics in kilobytes by default, but you can also display reports with memory sizes in megabytes with the argument `-S M`.

**[Go to Top](#contents)**

### Other Command Line Tools to Monitor Linux Performance (for System and Network)

|Name|Description|
|----|-----------|
|Tcpdump – Network Packet Analyzer|The tcpdump command is one of the most widely used command-line network packet analyzer or packets sniffer programs that is used to capture or filters TCP/IP packets that are received or transferred on a specific interface over a network.|
|Netstat – Network Statistics|The netstat is a command-line tool for monitoring incoming and outgoing network packets statistics as well as interface statistics.|
|iotop – Monitor Linux Disk I/O|iotop is also much similar to top command and htop program, but it has an accounting function to monitor and display real-time Disk I/O and processes.|
|iostat – Input/Output Statistics|iostat is a simple tool that will collect and show system input and output storage device statistics.|
|IPTraf – Real-Time IP LAN Monitoring|IPTraf is an open-source console-based real-time network (IP LAN) monitoring utility for Linux. |
|Psacct or Acct – Monitor User Activity|psacct or acct tools are very useful for monitoring each user’s activity on the system.|
|Monit – Linux Process and Services Monitoring|Monit is a free open source and web-based process supervision utility that automatically monitors and manages system processes, programs, files, directories, permissions, checksums, and file systems.|
|NetHogs – Monitor Per Process Network Bandwidth|NetHogs is an open-source nice small program (similar to Linux top command) that keeps a tab on each process network activity on your system.|
|iftop – Network Bandwidth Monitoring|iftop is another terminal-based free open source system monitoring utility that displays a frequently updated list of network bandwidth utilization (source and destination hosts) that passing through the network interface on your system.|
|Monitorix – System and Network Monitoring|Monitorix is a free lightweight utility that is designed to run and monitor system and network resources as many as possible in Linux/Unix servers.|
|Arpwatch – Ethernet Activity Monitor|Arpwatch is a kind of program that is designed to monitor Address Resolution of (MAC and IP address changes) of Ethernet network traffic on a Linux network.|
|Suricata – Network Security Monitoring|Suricata is a high-performance open-source Network Security and Intrusion Detection and Prevention Monitoring System for Linux, FreeBSD, and Windows.|
|VnStat PHP – Monitoring Network Bandwidth|VnStat PHP is a web-based frontend application for the most popular networking tool called “vnstat“. VnStat PHP monitors network traffic usage in nicely graphical mode.|
|Nagios – Network/Server Monitoring|Nagios is a leading open source powerful monitoring system that enables network/system administrators to identify and resolve server-related problems before they affect major business processes.|
|Nmon: Monitor Linux Performance|Nmon (stands for Nigel’s performance Monitor) tool, which is used to monitor all Linux resources such as CPU, Memory, Disk Usage, Network, Top processes, NFS, Kernel, and much more. This tool comes in two modes: Online Mode and Capture Mode.|

**References**

- [What Is a System Monitor?](https://web.archive.org/web/20101207054610/https://www.wisegeek.com/what-is-a-system-monitor.htm)
- [20 Command Line Tools to Monitor Linux Performance](https://www.tecmint.com/command-line-tools-to-monitor-linux-performance/)
- [16 Top Command Examples in Linux [Monitor Linux Processes]](https://www.tecmint.com/12-top-command-examples-in-linux/)
- [Linux Performance Monitoring with Vmstat and Iostat Commands](https://www.tecmint.com/linux-performance-monitoring-with-vmstat-and-iostat-commands/)
- [10 lsof Command Examples in Linux](https://www.tecmint.com/10-lsof-command-examples-in-linux/)
- [linux server administration/sar](https://en.wikiversity.org/wiki/Linux_server_administration/sar#:~:text=sar%20(System%20Activity%20Report)%20is,and%20recent%20recorded%20system%20performance.)
- [CentOS / RHEL: Install atop (Advanced System & Process Monitor) Utility](https://www.cyberciti.biz/faq/centos-redhat-linux-install-atop-command-using-yum/)

**[Go to Top](#contents)**

## Process Monitoring

### TOP

- Linux Top command is a performance monitoring program that is used frequently by many system administrators to monitor Linux performance and it is available under many Linux/Unix-like operating systems.
- The top command is used to display all the running and active real-time processes in an ordered list and updates it regularly.
- It displays CPU usage, Memory usage, Swap Memory, Cache Size, Buffer Size, Process PID, User, Commands, and much more.
- It also shows high memory and cpu utilization of running processes.

**Top Commands**

- `# top` : To list all running Linux Processes, simply type top on the command line to get the information of running tasks, memory, cpu, and swap. `q` for quit window.
- `M` and `T` keys, to sort all Linux running processes by Process ID and running time.
- `M` and `P` keys, to short all Linux running processes by Memory usage.
- `# top -u user_name` :  To display all user-specific running processes information, use the -u option will list specific User process details.
- `z` option will display the running process in color which may help you to identify the running process easily.
- `c` option in running top command will display the absolute path of the running process.
- By default screen refresh interval is set to 3.0 seconds, the same can be changed by pressing the `d` option in running the top command to set desired interval time.
- To kill a process after finding the PID of the process by pressing the ``k option in running the top command without closing the top window as shown below.
- To sort all running processes by CPU utilization, simply press `Shift+P` key.
- You can use the `r` option to change the priority of the process also called *Renice*.
- To list the load information of your CPU cores, simply press `1` to list the CPU core details.
- `# top -n 1 -b > top-output.txt` : To save the running top command results output to a file `/root/.toprc` use the following command.
- `i` options, to get the list of idle/sleeping processes.
- `h` option to obtain the top command help.

**[Go to Top](#contents)**

### ps

- **ps** command will report a snapshot of the current processes.

**Some ps Commands**

- `# ps -A` : To select all processes use the `-A` or `-e` option.
- `# ps -Al` : Show Long Format Output
- `# ps -AlF` : To turn on extra full mode (it will show command line arguments passed to process)
- `# ps -AlFH` : Display Threads ( LWP and NLWP)
- `# ps -AlLm` : Watch Threads After Processes
- `# ps axu` : Print All Process On The Server

**[Go to Top](#contents)**

### Htop - Linux Process Monitoring

- **htop** is a much advanced interactive and real-time Linux process monitoring tool, which is much similar to Linux top command but it has some rich features like a **user-friendly interface to manage processes**, **shortcut keys**, **vertical and horizontal views of the processes**, and much more.
- **htop** is a third-party tool, which doesn’t come with Linux systems, you need to install it using your system package manager tool.

*Install Htop in Linux*

```
// for Debian
$ sudo apt install htop  

// for Fedora   
$ sudo dnf install htop   

// for CentOS 8/7
$ sudo yum install epel-release
$ sudo yum install htop
```

*Sections of htop*
- Header, where we can see info like **CPU**, **Memory**, **Swap** and also shows tasks, **load average**, and **Up-time**.
- List of processes sorted by **CPU** utilization.
- Footer shows different options like **help**, **setup**, **filter tree kill**, **nice**, **quit**, etc.

**Htop Shortcut and Function Keys**
|Description|Function Keys|Shortcut Keys|
|-----------|-------------|-------------|
|Help|F1|`h`|
|Setup|F2|`s`|
|Search Process|F3|`/`|
|Invert Sort Order|F4|`\|`|
|Tree|F5|`t`|
|Sort By|F6|`>`|
|Nice - (Change Priority)|F7|`[`|
|Nice + (Change Priority)|F8|`]`|
|Kill a Process|F9|`k`|
|Quit|F10|`q`|

**[Go to Top](#contents)**

### Lsof - List Open Files

- The lsof command is used in many Linux/Unix-like systems to display a list of all the open files and the processes.
- The open files included are disk files, network sockets, pipes, devices, and processes.
- One of the main reasons for using this command is when a disk cannot be unmounted and displays the error that files are being used or opened. With this command, you can easily identify which files are in use.

**Lsof Commands**

- `lsof` : it will show a long listing of open files some of them are extracted for better understanding which displays the columns like Command, PID, USER, FD, TYPE, etc. 
    - **FD** – stands for a File descriptor and may see some of the values as:
        - `cwd` current working directory
        - `rtd` root directory
        - `txt` program text (code and data)
        - `mem` memory-mapped file
    - In **FD** column numbers like 1u is actual file descriptor and followed by u,r,w of its mode as:
        - `r` for read access.
        - `w` for write access.
        - `u` for read and write access.
    - **TYPE** – of files and it’s identification.
        - `DIR` – Directory
        - `REG` – Regular file
        - `CHR` – Character special file.
        - `FIFO` – First In First Out
- `lsof -u user_name` : Display the list of all opened files of user **user_name**.
- `lsof -i TCP:22` : To find out all the running Linux processes of a specific port, just use the following command with option `-i`.
- `lsof -i 4` : Shows only IPv4 network files.
- `lsof -i 6` : Shows only IPv6 network files.
- `lsof -i TCP:1-1024` : To list all the running process of open files of TCP Port ranges from 1-1024.
- `lsof -i -u^root` : Exclude the **root** user. You can exclude a particular user using `^` with the command.
- `lsof -i -u user_name` : Shows user **user_name** is using commands like *ping* and */etc* directory.
- `lsof -i` : Shows the list of all network connections ‘LISTENING & ESTABLISHED’.
- `lsof -p 1` : Shows whose PID is 1. 
- `kill -9 "lsof -t -u user_name"` : Kill all the processes of the **user_name** user.

**[Go to Top](#contents)**

### monit

- Monit is a free and open source software that acts as process supervision.
- It comes with the ability to restart services which have failed.
- You can use Systemd, daemontools or any other such tool for the same purpose.

**References**
- [30 Linux System Monitoring Tools Every SysAdmin Should Know](https://www.cyberciti.biz/tips/top-linux-monitoring-tools.html)
- [Show All Running Processes in Linux using ps/htop commands](https://www.cyberciti.biz/faq/show-all-running-processes-in-linux/)
- [20 Command Line Tools to Monitor Linux Performance](https://www.tecmint.com/command-line-tools-to-monitor-linux-performance/)
- [How to install and use Monit on Ubuntu/Debian Linux server as process supervision tool](https://www.cyberciti.biz/faq/how-to-install-and-use-monit-on-ubuntudebian-linux-server/)

**[Go to Top](#contents)**

## Memory: Monitoring Usage and Tuning

- aka. RAM is your physical memory.
- Virtual memory = Swap space available on the disk + Physical memory. The virtual memory contains both user space and kernel space.
- Using either 32-bit or 64-bit system makes a big difference in determining how much memory a process can utilize.
- On a 32-bit system a process can only access a maximum of **4GB** virtual memory. On a 64-bit system there is **no** such limitation.
- The unused RAM will be used as file system cache by the kernel.
- The Linux system will swap when it needs more memory. i.e when it needs more memory than the physical memory. When it swaps, it writes the least used memory pages from the physical memory to the swap space on the disk.
- Lot of swapping can cause performance issues, as the disk is much slower than the physical memory, and it takes time to swap the memory pages from RAM to disk
- [Monitoring Memory Usage with](#vmstat---virtual-memory-statistics) [*vmstat*](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/performance_tuning_guide/sect-red_hat_enterprise_linux-performance_tuning_guide-tool_reference-vmstat)

### Profiling Application Memory Usage with Valgrind

- [Valgrind](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/performance_tuning_guide/sect-red_hat_enterprise_linux-performance_tuning_guide-memory-monitoring_and_diagnosing_performance_problems)
- The valgrind ships with a number of tools that can be used to profile and analyze program performance.
- The valgrind tools outlined in this section can help you to detect memory errors such as uninitialized memory use and improper memory allocation or deallocation.

**Profiling Memory Usage with *Memcheck***

- Memcheck is the default valgrind tool. It detects and reports on a number of memory errors that can be difficult to detect and diagnose, such as:
    - Memory access that should not occur
    - Undefined or uninitialized value use
    - Incorrectly freed heap memory
    - Pointer overlap
    - Memory leaks
- **NOT: Memcheck can only report these errors; it cannot prevent them from occurring.**

**Profiling Cache Usage with *Cachegrind***

- Cachegrind simulates application interaction with a system's cache hierarchy and branch predictor.
- It tracks usage of the simulated first level instruction and data caches to detect poor code interaction with this level of cache, also tracks the last level of cache (second or third level) in order to track memory access. As such, applications executed with cachegrind run twenty to one hundred times slower than usual.
- Cachegrind gathers statistics for the duration of application execution and outputs a summary to the console. 
- Cachegrind also provides the cg_diff tool, which makes it easier to chart program performance before and after a code change.
- The resulting output file can be viewed in greater detail with the cg_annotate tool.
- Cachegrind writes detailed profiling information to a per-process cachegrind.out.pid file, where pid is the process identifier.

**[Go to Top](#contents)**

**Profiling Heap and Stack Space with Massif**

- Massif measures the heap space used by a specified application.
- It measures both useful space and any additional space allocated for bookkeeping and alignment purposes.
- massif helps you understand how you can reduce your application's memory use to increase execution speed and reduce the likelihood that your application will exhaust system swap space. Applications executed with massif run about twenty times slower than usual.
- You can also use the following options to focus massif output on a specific problem.
    - **--heap**
    - **--heap-admin**
    - **--stacks**
    - **--time-unit**
- Massif outputs profiling data to a massif.out.pid file, where pid is the process identifier of the specified application.
- The ms_print tool graphs this profiling data to show memory consumption over the execution of the application, as well as detailed information about the sites responsible for allocation at points of peak memory allocation

**References**

- [Linux Performance Monitoring and Tuning Introduction](https://www.thegeekstuff.com/2011/03/linux-performance-monitoring-intro/)
- [7.2. MONITORING AND DIAGNOSING PERFORMANCE PROBLEMS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/performance_tuning_guide/sect-red_hat_enterprise_linux-performance_tuning_guide-memory-monitoring_and_diagnosing_performance_problems)
- [Performance Monitoring Tools](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/performance_tuning_guide/index)

## I/O Monitoring and Tuning

**Vmstat**

 - [Vmstat](#vmstat---virtual-memory-statistics) reports on processes, memory, paging, block I/O, interrupts, and CPU activity across the entire system. It can help administrators determine whether the I/O subsystem is responsible for any performance issues.
 - The information most relevant to I/O performance is in the following columns:
     - **si** : Swap in, or reads from swap space, in KB.
     - **so** : Swap out, or writes to swap space, in KB.
     - **bi** : Block in, or block write operations, in KB.
     - **bo** : Block out, or block read operations, in KB.
     - **wa** : The portion of the queue that is waiting for I/O operations to complete.
- Swap in and swap out are particularly useful when your swap space and your data are on the same device, and as indicators of memory usage.
- Additionally, the free, buff, and cache columns can help identify write-back frequency. A sudden drop in cache values and an increase in free values indicates that write-back and page cache invalidation has begun.
- If analysis with vmstat shows that the I/O subsystem is responsible for reduced performance, administrators can use iostat to determine the responsible I/O device.

**[Go to Top](#contents)**

**Monitoring I/O Performance with iostat**

- **iostat** is provided by the sysstat package.
- It reports on I/O device load in your system.
- If analysis with vmstat shows that the I/O subsystem is responsible for reduced performance, you can use iostat to determine the I/O device responsible.
- You can focus the output of iostat reports on a specific device by using the parameters defined in the iostat man page.

**Detailed I/O Analysis with blktrace**

- **Blktrace** provides detailed information about how time is spent in the I/O subsystem.
- The companion utility **blkparse** reads the raw output from **blktrace** and produces a human readable summary of input and output operations recorded by **blktrace**.

**Analyzing blktrace Output with btt**

- The btt utility is provided as part of the blktrace package.
- It analyzes **blktrace** output and displays the amount of time that data spends in each area of the I/O stack, making it easier to spot bottlenecks in the I/O subsystem.
- Some of the important events tracked by the **blktrace** mechanism and analyzed by btt are:
    - Queuing of the I/O event (Q)
    - Dispatch of the I/O to the driver event (D)
    - Completion of I/O event (C)
- You can include or exclude factors involved with I/O performance issues by examining combinations of events.
- To inspect the timing of sub-portions of each I/O device, look at the timing between captured blktrace events for the I/O device.
- If the device takes a long time to service a request (D2C), the device may be overloaded, or the workload sent to the device may be sub-optimal.
- If block I/O is queued for a long time before being dispatched to the storage device (Q2G), it may indicate that the storage in use is unable to serve the I/O load. 
- Looking at the timing across adjacent I/O can provide insight into some types of bottleneck situations

**Analyzing blktrace Output with iowatcher**

- The iowatcher tool can use blktrace output to graph I/O over time.
- It focuses on the Logical Block Address (LBA) of disk I/O, throughput in megabytes per second, the number of seeks per second, and I/O operations per second. This can help to identify when you are hitting the operations-per-second limit of a device.

**[Go to Top](#contents)**

**References**

- [MONITORING AND DIAGNOSING PERFORMANCE PROBLEMS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/performance_tuning_guide/sect-red_hat_enterprise_linux-performance_tuning_guide-storage_and_file_systems-monitoring_and_diagnosing_performance_problems)
- [FACTORS AFFECTING I/O AND FILE SYSTEM PERFORMANCE](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/monitoring_and_managing_system_status_and_performance/factors-affecting-i-o-and-file-system-performance_monitoring-and-managing-system-status-and-performance)
- [6 best tools to monitor disk IO performance in Linux](https://www.2daygeek.com/linux-disk-io-performance-monitoring/)

##  I/O Scheduling

- I/O schedulers exist as a way to optimize disk access requests. They traditionally do this by merging I/O requests to similar locations on disk. By grouping requests located at similar sections of disk, the drive doesn't need to "seek" as often, improving the overall response time for disk operations.
- On modern Linux implementations, there are several I/O scheduler options available. Each of these have their own unique method of scheduling disk access requests. 

**CFQ**

- The Complete Fairness Queueing (CFQ) I/O scheduler works by creating a per-process I/O queue.
- The goal of this I/O scheduler is to provide a fair I/O priority to each process. 
- While the CFQ algorithm is complex, the gist of this scheduler is that after ordering the queues to reduce disk seeking, it services these per-process I/O queues in a round-robin fashion. What this means for performance is that the CFQ scheduler tries to provide each process with the same priority for disk access.

**Deadline**

- The Deadline scheduler works by creating two queues: a read queue and a write queue. Each I/O request has a time stamp associated that is used by the kernel for an expiration time.
- While this scheduler also attempts to service the queues based on the most efficient ordering possible, the timeout acts as a "deadline" for each I/O request. When an I/O request reaches its deadline, it is pushed to the highest priority.
- While tunable, the default "deadline" values are **500 ms for Read** operations and **5,000 ms for Write** operations.

**Noop**

- The Noop scheduler is a unique scheduler. 
- Rather than prioritizing specific I/O operations, it simply places all I/O requests into a FIFO (First in, First Out) queue. While this scheduler does try to merge similar requests, that is the extent of the complexity of this scheduler.
- This scheduler is optimized for systems that essentially do not need an I/O scheduler. 
- This scheduler can be used in numerous scenarios such as environments where the underlying disk infrastructure is performing I/O scheduling on Virtual Machines.
- Since a VM is running within a Host Server/OS, that host already may have an I/O scheduler in use. In this scenario, each disk operation is passing through two I/O schedulers: one for the VM and one for the VM Host.

**[Go to Top](#contents)**

**References**

- [Improving Linux System Performance with I/O Scheduler Tuning](https://www.cloudbees.com/blog/linux-io-scheduler-tuning)
- [Linux I/O Scheduler](https://www.thomas-krenn.com/de/wiki/Linux_I/O_Scheduler)
- [Linux I/O Schedulers](https://www.admin-magazine.com/HPC/Articles/Linux-I-O-Schedulers)

## Linux Filesystems and the VFS

**Linux Filesystem**

- Every file in Linux is represented by an inode. An inode is the combination of an identification number and some additional file description data, some of which includes:
    - The file's location on the hard drive
    - The file's creation and modification dates
    - The file's owner
    - The file's size
    - The file's permissions
- Anything other than a process is a file. Examples include regular files (such as a text document), directories, special files (such as input/output devices), links, sockets, pipes and block devices.
- A partition contains the set of all file types. Data partitions host all the files and swap partitions contain expanded computer memory.
- The Virtual File System (also known as the Virtual Filesystem Switch) is the software layer in the kernel that provides the filesystem interface to user space programs. It also provides an abstraction within the kernel which allows different filesystem implementations to coexist.
- VFS system calls open(2), stat(2), read(2), write(2), chmod(2) and so on are called from a process context.

**Directory Entry Cache (dcache)**

- The VFS implements the open(2), stat(2), chmod(2), and similar system calls. The pathname argument that is passed to them is used by the VFS to search through the directory entry cache (also known as the dentry cache or dcache). This provides a very fast look-up mechanism to translate a pathname (filename) into a specific dentry. Dentries live in RAM and are never saved to disc: they exist only for performance.

- The dentry cache is meant to be a view into your entire filespace. As most computers cannot fit all dentries in the RAM at the same time, some bits of the cache are missing. In order to resolve your pathname into a dentry, the VFS may have to resort to creating dentries along the way, and then loading the inode. This is done by looking up the inode.

**The Inode Object**

- An individual dentry usually has a pointer to an inode. Inodes are filesystem objects such as regular files, directories, FIFOs and other beasts. They live either on the disc (for block device filesystems) or in the memory (for pseudo filesystems). Inodes that live on the disc are copied into the memory when required and changes to the inode are written back to disc.
- To look up an inode requires that the VFS calls the lookup() method of the parent directory inode. This method is installed by the specific filesystem implementation that the inode lives in. Once the VFS has the required dentry (and hence the inode), we can do all those boring things like open(2) the file, or stat(2) it to peek at the inode data. The stat(2) operation is fairly simple: once the VFS has the dentry, it peeks at the inode data and passes some of it back to userspace.

**The File Object**

- Opening a file requires another operation: allocation of a file structure (this is the kernel-side implementation of file descriptors). The freshly allocated file structure is initialized with a pointer to the dentry and a set of file operation member functions. These are taken from the inode data. The open() file method is then called so the specific filesystem implementation can do its work. You can see that this is another switch performed by the VFS. The file structure is placed into the file descriptor table for the process.
- Reading, writing and closing files (and other assorted VFS operations) is done by using the userspace file descriptor to grab the appropriate file structure, and then calling the required file structure method to do whatever is required. For as long as the file is open, it keeps the dentry in use, which in turn means that the VFS inode is still in use.

**[Go to Top](#contents)**

**References**

- [Overview of the Linux Virtual File System](https://www.kernel.org/doc/html/latest/filesystems/vfs.html)

## Disk Partitioning

- Disk Partitioning is the process of dividing a disk into one or more logical areas, often known as partitions, on which the user can work separately. It is one step of disk formatting.
-  If a partition is created, the disk will store the information about the location and size of partitions in the partition table. With the partition table, each partition can appear to the operating system as a logical disk, and users can read and write data on those disks.
-  The main advantage of disk partitioning is that each partition can be managed separately.
- We need disk partitioning to upgrade Hard Disk (to incorporate new Hard Disk to the system), dual Booting (Multiple Operating Systems on the same system), efficient disk management, ensure backup and security and work with different File Systems using the same system.

- In order to successfully partition a disk and to make it useful, we need to ensure that, we have completed the below four steps, regardless of the Operating system and Hardware of the system.
    - Attach disk on the proper port
    - Create partitions in the disk
    - Create a file system on the partition
    - Mounting the file systems
- To view the available Hard Disks in our system, use the command `lsblk`.
- We can use the `fdisk -l` command to find out where the Hard Disk we are going to partition is located.
- While partitioning, we should be aware of certain factors. 
    - On a disk, we can have a maximum of four partitions
    - The partitions are of two types
        - Primary
        - Extended
    - Extended partitions can have logical partitions inside it
    - Among the four possible partitions, the possible combinations are
        - All 4 primary partitions
        - 3 primary partitions and 1 extended partition
- Common `fdisk` flags.

```
m  -> help
p  -> print partition table
n  -> create new partition
d  -> delete partition
q  -> quit without writing
w  -> write to disk
```

**References**

- [Disk Partitioning in Linux](https://www.geeksforgeeks.org/disk-partitioning-in-linux/)

## Th ext2/ext3/ext4 Filesystems

**The ext2 Filesystem**

- The ext2 filesystem (second extended filesystem) was created in 1993. It was based on the Minix filesystem created in 1987, and the ext filesystem created in 1992. These original filesystems were good starting points, but they soon ran into limitations as Linux grew in popularity and usage. Some of the advantages of ext2 are:
    - ext2 is suitable for flash drives and USB drives.
    - A file can be between 16 GB and 2 TB in size.
    - An ext2 filesystem can be between 2 TB and 32 TB in size.
- There are also some disadvantages that come with using the ext2 filesystem:
    - ext2 filesystems are likely to become corrupt during power failures and computer crashes when data is being saved to the disk.
    - ext2 filesystems face data fragmentation issues which hinder performance.

**The ext3 Filesystem**

- The ext3 filesystem (third extended filesystem) was introduced in November of 2001. While file sizes and total filesystem size remained the same as with ext2, there were also many improvements:
    - ext3 allows journaling. Journaling creates a separate area of the filesystem where all file changes are tracked. The Journal can then be used in case of a power failure or system crash to restore data.
    - A directory in ext3 can have up to 32,000 subdirectories.

**The ext4 Filesystem**

- The ext4 filesystem (fourth extended Filesystem) was introduced in 2008. Although considered an interim filesystem until a new filesystem could be developed, ext4 brought the following benefits:
    - A directory can have up to 64,000 subdirectories.
    - A file can be up to 16 TB in size.
    - An ext4 filesystem can be up to 1 EB (Exabyte) in size, although most Linux distributions recommend a maximum filesystem size of 100 Tb.
    - ext4 reduces fragmentation issues and increases performance.
    - ext4 provides an option to turn off the journaling feature.

**[Go to Top](#contents)**

**References**

- [Linux Filesystems: Types & Features](https://study.com/academy/lesson/linux-filesystems-types-features.html#lesson)