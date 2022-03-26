# Linux Notes

*This content is based on the [Essentials of Linux System Administration - LFS201](https://training.linuxfoundation.org/training/essentials-of-linux-system-administration/) course content.*

**Creation Date :** 23.03.2022 - **Author :** Hasan Guzelmansur

---

## Contents

1. [Linux Filesystem Tree Layout](#linux-filesystem-tree-layout)
2. [Processes](#processes)
    - [Background Process](#background-process)
    - [Foreground Process](#foreground-process)
    - [Daemons](#daemons)
    - [The Init Process](#the-init-process)
    - [States of a Process in Linux](#states-of-a-process-in-linux)
3. [Signals](#signals)
4. [Package Management Systems](#package-management-systems)
    - [RPM-Based Package Managers](#rpm-based-package-managers)
    - [Debian-Based Package Managers](#debian-based-package-managers)
    - [Arch-Based Package Managers](#arch-based-package-managers)
    - []()
    - []()

---

## Linux Filesystem Tree Layout

- Linux file system follows a tree-like hierarchical structure starting at the root. This structure follows a standard layout recommended by Filesystem Hierarchy Standard (FHS), which is a standard maintained by the Linux Foundation.
- Tree-like structure of the root directory *command :* `tree -d -L 1`
- Unix-like systems intend to keep things simple and treat every thing as a sequence of bytes. These sequence of bytes are known as files to the OS.
- Hardware devices are also files.
- Unlike Windows which has multiple roots, the Linux only allows one root.
- The root directory is where all other directories and files on the system reside and is designated by a forward slash /.
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
- [All You Need To Know About Processes in Linux](https://www.tecmint.com/linux-process-management/)
- [LINUX PROCESSES AND SIGNALS](https://www.bogotobogo.com/Linux/linux_process_and_signals.php)

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

### Arch-Based Package Managers

- Arch Linux uses a package manager called `pacman`. 
- Unlike `.deb` or `.rpm` files, pacman uses a more traditional `tarball` with the LZMA2 compression (.tar.xz). This enables Arch Linux packages to be much smaller than other forms of compressed archives (such as gzip). 
- One of the major benefits of pacman is that it supports the Arch Build System, a system for building packages from source.
-  The build system ingests a file called a PKGBUILD, which contains metadata (such as version numbers, revisions, dependencies, etc.) as well as a shell script with the required flags for compiling a package conforming to the Arch Linux requirements. The resulting binaries are then packaged into the aforementioned .tar.xz file for consumption by pacman.
- This system led to the creation of the Arch User Repository (AUR) which is a community-driven repository containing PKGBUILD files and supporting patches or scripts. This allows for a virtually endless amount of software to be available in Arch.
- Advantage of this system is that if a user wishes to make software available to the public, they do not have to go through official channels to get it accepted in the main repositories.
- The downside is that it relies on community curation similar to Docker Hub, Canonical's Snap packages, or other similar mechanisms.
- There are numerous AUR-specific package managers that can be used to download, compile, and install from the PKGBUILD files in the AUR.

**References**

- [Package Management - Ubuntu](https://ubuntu.com/server/docs/package-management)
- [Introduction to Package Management in Linux](https://linuxhint.com/package-management-linux-introduction/)
- [List of software package management systems](https://en.wikipedia.org/wiki/List_of_software_package_management_systems)
- [TAR](https://en.wikipedia.org/wiki/Tar_(computing))
- [Package manager](https://en.wikipedia.org/wiki/Package_manager)
- [The evolution of package managers](https://opensource.com/article/18/7/evolution-package-managers)
- [Software Repository](https://www.techopedia.com/definition/32890/software-repository)
- [RPM Package Manager](https://en.wikipedia.org/wiki/RPM_Package_Manager)
