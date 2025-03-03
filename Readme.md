<!-- What is Virtualization ? -->
# What is Virtualization ?
Virtualization is a technology that allows you to create a virtual version of a computer system within a single physical machine. This virtual system, called a "virtual machine" (VM), acts like a separate computer with its own operating system, applications, and resources, even though it shares the physical resources (like CPU, memory, and storage) of the host machine.

## What is virtual machine (VMs)?
These are isolated environments on the host system. Each VM has its own OS and runs independently,

## What is Hypervisor ?
A key component in virtualization, a hypervisor is the software layer that enables multiple VMs to run on a single physical host. The hypervisor manages the resources each VM uses, like CPU, memory, and storage. Examples of hypervisors include VirtualBox, VMware, and Hyper-V.

### Why Virtualization?
    Cost-effective: Allows multiple virtual servers to run on a single physical server, saving on hardware costs.
    Flexibility: You can easily create, delete, or modify VMs, which is great for testing and development.
    Isolation: Different environments can run on the same hardware without interfering with each other.
    Scalability: Resources can be dynamically allocated to VMs as needed.

## How does a virtual machine work and what is its purpose?
A virtual machine (VM) works by simulating a complete computer system within software, enabling it to function as an independent computer with its own operating system and applications. The VM relies on a layer of software called a hypervisor to manage and allocate the physical resources of the host machine, such as CPU, memory, and storage, across multiple virtual environments.

## Purpose of Virtual Machines
- Testing and Development
- Running Multiple OSs on One Machine
- Enhanced Security and Isolation
- Efficient Use of Resources
- Education and Training




<!-- Operating System Comparison: Debian vs Rocky Linux -->
# Operating System Comparison: Debian vs Rocky Linux

`Basic Differences Between Rocky Linux and Debian`
| Feature                                   | Debian                                                    | Rocky Linux                                                     |
| ----------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------|
| Origin                                    | Community-driven, created in 1993.                        | Fork of CentOS, created in 2021.                                |
| Base System                               | Based on Debian GNU/Linux.                                | Derived from Red Hat Enterprise Linux (RHEL).                   |
| Target Users                              | General-purpose, for servers and desktops.                | Primarily enterprise-level servers.                             |
| Package Management                        | Uses apt and .deb packages.                               | Uses dnf/yum and .rpm packages.                                 |
| Stability	Three branches:                 | Stable, Testing, and Unstable. Stable is highly reliable. | Extremely stable, mirroring RHEL releases.                      |
| Security                                  | Utilizes AppArmor for security.                           | Uses SELinux, which is enabled and enforced by default.         |
| Community                                 | Large and highly active.                                  | Growing community after the shift from CentOS to CentOS Stream. |

### Recommendation
- For those new to system administration, Debian is recommended. It offers:
- User-Friendliness: Simplified configurations, allowing beginners to focus on core concepts.
- Robust Community Support: Extensive documentation and community resources.
- Security and Stability: Reliable for server setup without the complex security frameworks of Rocky Linux.

<i style="color:yellow">Rocky Linux, while highly stable and suited for enterprise environments, is better suited for users with familiarity in RHEL-based systems and a need for advanced security configurations such as SELinux.</i>




<!-- What is LVM? -->
# What is LVM?

LVM stands for Logical Volume Manager. It is a storage management system used in Linux that provides a flexible and efficient way to manage disk storage. Instead of working directly with physical disks or partitions, LVM abstracts these physical storage devices and allows you to manage them in a more dynamic and user-friendly way.

## Key Concepts of LVM

    1. Physical Volumes (PVs):
        These are physical storage devices like hard drives or partitions (e.g., /dev/sda1).
        PVs form the foundation for LVM.

    2. Volume Groups (VGs):
        A Volume Group is a pool of storage created by combining multiple Physical Volumes.
        You can think of it as a "storage pool" from which you can allocate space dynamically.

    3. Logical Volumes (LVs):
        Logical Volumes are the "virtual partitions" that are created from the space in a Volume Group.
        These are what you format with a file system (like ext4, xfs) and mount for use.

## What is Encrypted Storage?

Encrypted storage refers to a mechanism where data stored on a device or partition is encoded using cryptographic algorithms, making it unreadable to unauthorized users. Only users with the proper decryption key (e.g., a password or a certificate) can access the data.




<!-- AppArmor vs SELinux: A Comparison -->
# AppArmor vs SELinux: A Comparison

Here is a detailed comparison table highlighting the differences between **AppArmor** and **SELinux**:

| Feature                        | **AppArmor**                                   | **SELinux**                                                                                                                       |    
|--------------------------------|------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|    
| **Basic Approach**             | Path-based access control.                     | Label-based access control.                                                                                                       |   
| **Configuration**              | Rules are applied to specific file paths.      | Rules are applied to objects with security labels.                                                                                |   
| **Ease of Use**                | Simpler to set up and manage.                  | More complex, with a steeper learning curve.                                                                                      |    
| **Granularity**                | Coarser control; applies to file paths and binaries. | Finer control; applies to every object in the system (files, processes, sockets, etc.).                                     |
| **Policy Writing**             | Easier and more intuitive to write policies.   | Requires understanding of security contexts and labels.                                                                           |
| **Default State**              | Defaults to "complain mode," which logs violations without enforcing rules. | Defaults to "enforcing mode," which actively blocks violations.                                      |
| **Integration**                | Integrated into the Linux kernel with Ubuntu and Debian as primary users. | Integrated into the Linux kernel, heavily used in Red Hat-based systems (e.g., Rocky, Fedora, CentOS). |
| **Performance Impact**         | Minimal impact due to simpler implementation.  | Slightly higher due to label checks on every access.                                                                              |  
| **Flexibility**                | Less flexible compared to SELinux.             | Highly flexible with multi-layered policies.                                                                                      |  
| **Community & Documentation**  | Well-documented for Debian/Ubuntu users.       | Comprehensive documentation for RHEL-based systems.                                                                               |  
| **Security Goals**             | Primarily designed to restrict applications.   | Provides comprehensive system-wide access control.                                                                                |

### Example Scenario
Process: A web server (e.g., Apache) wants to read a configuration file, /etc/httpd/conf/httpd.conf.
Kernel Interaction: The process sends a request to the kernel to open the file for reading.
Security Systems:
- AppArmor: Uses path-based security policies.
- SELinux: Uses label-based security policies.

[Core Differences Between SELinux and AppArmo](https://www.baeldung.com/linux/selinux-vs-apparmor)


# What is LVM?

LVM (Logical Volume Manager) is an abstraction layer between a storage device and a file system. We get many advantages from using LVM, but the main advantage is that we have much more flexibility when it comes to managing partitions. Suppose we create four partitions on our storage disk. If for any reason we need to expand the storage of the first three partitions, we will not be able to because there is no space available next to them. In case we want to extend the last partition, we will always have the limit imposed by the disk. In other words, we will not be able to manipulate partitions in a friendly way. Thanks to LVM, all these problems are solved.
By using LVM, we can expand the storage of any partition (now known as a logical volume) whenever we want without worrying about the contiguous space available on each logical volume. We can do this with available storage located on different physical disks (which we cannot do with traditional partitions). We can also move different logical volumes between physical devices. Of course, services and processes will work the same way they always have. But to understand all this, we have to know:

- Physical Volume (PV): physical storage device. It can be a hard disk, an SD card, a floppy disk, etc. This device provides us with storage available to use.
- Volume Group (VG): to use the space provided by a PV, it must be allocated in a volume group. It is like a virtual storage disk that will be used by logical volumes. VGs can grow over time by adding new VPs.
- Logical volume (LV): these devices will be the ones we will use to create file systems, swaps, virtual machines, etc. If the VG is the storage disk, the LV are the partitions that are made on this disk.

```plaintext
+-----------------------------------------------------+
|                  Physical Volume (PV)               |
|   /dev/sdX (e.g., hard disk)                        |
|                                                     |
|   +---------------------------------------------+   |
|   |               Volume Group (VG)             |   |
|   |    VG Name: my_vg                           |   |
|   |                                             |   |
|   |  +-------------+   +---------------------+  |   |
|   |  | Logical Vol |   |    Logical Vol      |  |   |
|   |  | Name: root  |   | Name: home          |  |   |
|   |  | Size: 20GB  |   | Size: 10GB          |  |   |
|   |  +-------------+   +---------------------+  |   |
|   +---------------------------------------------+   |
+-----------------------------------------------------+
```



To see later not now:

# TTY mode
When requiretty is set, sudo must be run from a logged-in terminal session (a tty). This prevents sudo from being used from daemons or other detached processes like cronjobs or webserver plugins. It also means you can't run it directly from an ssh call without setting up a terminal session.

This can prevent certain kinds of escalation attacks. For example, if I have a way to modify the crontab for a user who has NOPASSWD sudo permissions, I could use that to kick off a job as root. With requiretty, I can't do that..

# Cron Job (crontab)
[`crontab` in Linux with Examples](https://www.geeksforgeeks.org/crontab-in-linux-with-examples/)


> [!TIP]
> - **Google**: `You can do your own search in medium, github, gitlab, youtube, ... xd`
