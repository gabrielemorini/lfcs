## **2. Service Management**

<details>
<summary><strong>What is systemd?</strong></summary>

Systemd is an <em>init system</em> and <em>service manager</em> for Linux. It is responsible for booting the system, starting and managing services (daemons), and handling system resources.  
It replaces older init systems (like SysVinit) and provides parallel startup, on‑demand service activation, and logging through journald.
</details>

<details>
<summary><strong>What are the units in systemd?</strong></summary>

Units are the basic objects that systemd manages.  
Each unit has a type, such as:

* service (for system services)  
* socket (for socket activation)  
* target (for grouping services, like runlevels)  
* timer (for scheduled tasks)  
* mount (for filesystems)  
   …and others.  
   Each unit is defined in a configuration file with a .service, .socket, .target, etc. extension.
</details>

<details>
<summary><strong>What is "Wants" in systemd?</strong></summary>

In systemd, **Wants** define which units should be pulled in when a target starts.  
Enabling a unit with systemctl enable creates a symlink in /etc/systemd/system/&lt;target&gt;.wants/ pointing to that service.
</details>

<details>
<summary><strong>List all services.</strong></summary>

```bash
systemctl list-unit-files --type=service
```
</details>

<details>
<summary><strong>Show the list of dependencies</strong></summary>

```bash
systemctl list-dependencies
```
</details>

<details>
<summary><strong>Power off or reboot the system.</strong></summary>

```bash
systemctl reboot
```
</details>

<details>
<summary><strong>Get the default target</strong></summary>

```bash
systemctl get-default
```
</details>

<details>
<summary><strong>Change temporarily the default target</strong></summary>

```bash
systemctl set-default multi-user.target
```
</details>

<details>
<summary><strong>Start and enable ssh service</strong></summary>

```bash
systemctl status ssh  
systemctl start ssh  
systemctl enable --now ssh
```
</details>

<details>
<summary><strong>Stop ssh service and mask it. Try to start the service and then unmask it.</strong></summary>

```bash
systemctl stop ssh  
systemctl mask ssh  
systemctl status ssh  
systemctl start ssh  
systemctl unmask ssh  
systemctl start ssh  
systemctl enable --now ssh
```
</details>

<details>
<summary><strong>Which directory contains all system services? Write the path.</strong></summary>

System service unit files provided by packages are stored in: <strong>/lib/systemd/system/</strong> (on most distributions). (On some, /usr/lib/systemd/system/ is used instead.)
</details>

<details>
<summary><strong>Where should custom services be created?</strong></summary>

Custom or user‑defined services should be placed in: <strong>/etc/systemd/system/</strong>. This directory overrides the default units and is preserved across package upgrades.
</details>

<details>
<summary><strong>What is the difference between /etc/systemd/system and /lib/systemd/system?</strong></summary>

<strong>- /lib/systemd/system</strong> (or /usr/lib/systemd/system): contains default unit files installed by the operating system or packages.  
<strong>- /etc/systemd/system</strong>: contains custom or overridden unit files created by the administrator.  
Systemd always gives priority to unit files in /etc/systemd/system.
</details>

<details>
<summary><strong>How do you reload systemd without rebooting?</strong></summary>

```bash
sudo systemctl daemon-reload
```
</details>

<details>
<summary><strong>Create a new system service with a script. The script content is:</strong></summary>

<em>echo "My app Started" | systemd-cat -t Myapp -p info</em>  
<em>sleep 5</em>  
<em>echo "My app crashed" | systemd-cat -t Myapp -p info</em>

Create the custom service in the correct folder. Try rebooting, upgrading, and updating the system.

```bash
sudo vim /usr/local/bin/script.sh (paste code here)  
cp /etc/systemd/system/sshd.service  /etc/systemd/system/myservice.service # create basic configuration and replace the content
sudo systemctl daemon-reload  
sudo systemctl start myservice.service  
sudo systemctl status myservice.service
```
</details>

---

<details>
<summary><strong>What is a process?</strong></summary>

A **process** is a running instance of a program. Each process has its own PID (process ID) and can be managed by the operating system.

You can list processes with the ps command. For example:

```bash
ps aux                      # show all processes
ps aux --sort=%cpu          # show all processes sorted by CPU usage
lsof -p <PID>               # list files opened by a specific process
```
</details>

<details>
<summary><strong>Which processes run without a terminal?</strong></summary>

Processes that are not attached to a terminal are called **daemons**.  
They usually run in the background and provide services to the system or other programs.

You can identify many daemons or kernel threads in ps output because their names appear in square brackets, for example [kworker/0:1H]
</details>

<details>
<summary><strong>What are Zombies? How to handle them?</strong></summary>

A **zombie process** is a process that has finished execution but still has an entry in the process table because its parent has not read its exit status.  
You can check with:

```bash
ps aux | grep defunct
```

To clean zombies, you cannot kill them directly; you must kill or fix their **parent process** (e.g., kill -9 &lt;parent_PID&gt;).
</details>

<details>
<summary><strong>Which process states exist in Linux?</strong></summary>

* **R (Running):** Active or ready to run.
* **S (Sleeping):** Waiting for an event.
* **D (Uninterruptible sleep):** Waiting for I/O; cannot be interrupted.
* **T (Stopped):** Stopped, often by `Ctrl-Z`.
* **Z (Zombie):** Completed but not yet reaped by the parent process.
</details>

<details>
<summary><strong>What is a nice value? Where can it be seen?</strong></summary>

The **nice value** determines the scheduling priority of a process.

* Range: from **-20** (highest priority) to **+19** (lowest priority).
* A lower nice value means the process gets more CPU time.

You can see the nice value with commands like:

```bash
top        # look at the NI column
ps -o pid,comm,ni,pri
```
</details>

---

<details>
<summary><strong>What are jobs?</strong></summary>

A **job** is a process (or a group of processes) that is managed by the current shell session.  
Jobs can run in the foreground or in the background:

* **Foreground:** runs interactively and occupies the terminal.
* **Background:** runs without blocking the terminal.

<em>Example:</em>

```bash
sleep 2000 &   # run in background with ampersand &
jobs           # list jobs
bg %1          # resume job 1 in background
fg %1          # bring job 1 to foreground
Ctrl+C         # stop the foreground job
```
</details>

<details>
<summary><strong>What is the difference between shell jobs, daemons, and kernel jobs?</strong></summary>

* **Shell jobs**: Processes started by your shell session (foreground or background).
* **Daemons**: Long-running background services started at boot or on demand, not tied to a terminal.
* **Kernel jobs**: Internal kernel threads performing low-level tasks, not directly visible as user-level jobs.
</details>

<details>
<summary><strong>What is the difference between Ctrl‑Z, Ctrl‑C, and Ctrl‑D?</strong></summary>

* **Ctrl‑Z**: Suspend (pause) the current foreground job and put it in the background as "stopped".
* **Ctrl‑C**: Interrupt and terminate the current foreground job immediately.
* **Ctrl‑D**: Send an end‑of‑file (EOF) to the current input; in a shell, this usually logs you out if typed at an empty prompt.
</details>

---

<details>
<summary><strong>What are signals?</strong></summary>

**Signals** are software interrupts sent to processes by the operating system or other processes. They are used to notify a process to stop, pause, continue, or perform some specific action.

Common examples:

* SIGTERM (15): politely ask a process to terminate.  
* SIGKILL (9): forcefully stop a process.  
* SIGHUP (1): hang up, often used to reload configuration.

You can send signals with commands like:

```bash
kill -SIGTERM <PID>  
kill -9 <PID>
```
</details>

<details>
<summary><strong>Show processes with user information, processes without terminal, in long and full format.</strong></summary>

```bash
ps auxfi
```
</details>

<details>
<summary><strong>Show the list of files opened by a process.</strong></summary>

```bash
lsof -p <PID>
```
</details>

<details>
<summary><strong>Launch sleep 2000 & and renice it to 18.</strong></summary>

```bash
sleep 2000 &  
top -p <PID>
renice -n 18 <PID>  
top -p <PID>
```
</details>

<details>
<summary><strong>Show jobs and move sleep 2000 & process in foreground.</strong></summary>

```bash
jobs  
fg %1
```
</details>

<details>
<summary><strong>Launch sleep 2000 & again and renice the previous process (sleep 2000 &) to 10.</strong></summary>

```bash
renice -n 10 <PID>
```
</details>

<details>
<summary><strong>Show all signals and use kill to terminate the previous process.</strong></summary>

```bash
kill -l  
kill -9 <PID>
```
</details>

---

<details>
<summary><strong>What is rsyslog?</strong></summary>

Rsyslog (**Rocket‑fast System Log**) is the modern logging service. It collects, filters, and stores log messages, usually under the /var/log/ directory.
</details>

<details>
<summary><strong>What are the common log files?</strong></summary>

```
/var/log/syslog  
/var/log/auth.log  
/var/log/messages  
/var/log/kern.log
```
</details>

<details>
<summary><strong>How to monitor security events in real time?</strong></summary>

Use: 
```bash
tail -f /var/log/secure
```
</details>

<details>
<summary><strong>How to force the machine to reset?</strong></summary>

From a root shell, run: 
```bash
echo b > /proc/sysrq-trigger
```

This immediately forces a reboot without syncing or unmounting filesystems (use with caution).
</details>

<details>
<summary><strong>What is the difference between sudo -i and sudo `-`?</strong></summary>

* su -  →  switches to the root user by asking for the **root password** and starts a login shell as root.
* sudo -i → gives an **authorized user** a root login shell without asking for the root password. This is generally considered more secure because you only need your own password and you need to be in the sudoers list.
</details>

<details>
<summary><strong>Display the last 100 lines of the system log (for example /var/log/syslog)</strong></summary>

```bash
tail -n 100 /var/log/syslog
```
</details>

<details>
<summary><strong>Monitor log updates in <em>real time</em> (for example /var/log/auth.log or /var/log/kern.log)</strong></summary>

```bash
journalctl -f
```
</details>

<details>
<summary><strong>Search for failed login attempts in logs (for example /var/log/auth.log)</strong></summary>

```bash
journalctl -xef -p err
```
</details>

<details>
<summary><strong>Show logs for a specific service (for example sshd)</strong></summary>

```bash
journalctl -xef -u ssh
```
</details>

<details>
<summary><strong>Display login history</strong></summary>

```bash
last
```
</details>

<details>
<summary><strong>Show the last login of all users</strong></summary>

```bash
lastlog
```
</details>

---

<details>
<summary><strong>What is a CPU?</strong></summary>

The CPU (**Central Processing Unit**) is the main processing unit of a computer.  
It is the key component that executes program instructions, performs logical and mathematical operations, and manages the flow of data between memory, input/output devices, and other parts of the system.  
In other words, it is the "brain" of the computer: it interprets software instructions and coordinates all operations.
</details>

<details>
<summary><strong>What are PCI devices?</strong></summary>

PCI devices (**Peripheral Component Interconnect**) are hardware components connected to the motherboard through the PCI standard. The PCI bus allows the CPU and other components to communicate with these devices.  
Examples of PCI devices:

* Graphics cards (GPUs)  
* Network cards (Ethernet, Wi‑Fi)  
* Sound cards  
* RAID or SCSI controllers  
* Expansion cards for additional ports
</details>

<details>
<summary><strong>Display disk free space.</strong></summary>

```bash
df -h
```
</details>

<details>
<summary><strong>Display disk usage.</strong></summary>

```bash
du -h
```
</details>

<details>
<summary><strong>Check available RAM and buffer usage.</strong></summary>

```bash
free -m
```
</details>

<details>
<summary><strong>Display CPU architecture.</strong></summary>

```bash
lscpu
```
</details>

<details>
<summary><strong>List PCI devices.</strong></summary>

```bash
lspci
```
</details>

---

<details>
<summary><strong>Which commands are used to schedule tasks?</strong></summary>

The main commands and tools used to schedule tasks on Linux are:

* cron – for recurring tasks.
* anacron – for recurring tasks on systems that may not run continuously.
* at – for one-time tasks scheduled at a specific time.
</details>

<details>
<summary><strong>What is the difference between cron and anacron?</strong></summary>

* **cron** is used to schedule repetitive tasks that run at specific times or intervals.  
  However, if the system is powered off when a cron job is scheduled to run, that job will be skipped.

* **anacron** is similar to cron but does not assume the system is running all the time.  
  If the system was off during a scheduled time, anacron will run the missed jobs as soon as possible after the system starts.

  Normally you don't configure it directly; it complements cron and runs scripts in /etc/cron.daily, /etc/cron.weekly, etc.
</details>

<details>
<summary><strong>What is the system-wide cron configuration file?</strong></summary>

The **system-wide** cron configuration file is: /etc/crontab

Each user can also have their own crontab, edited with: <em>crontab -e</em>. This edits the current user's crontab, not the system-wide configuration.
</details>

<details>
<summary><strong>Create a cron job that runs a script every day at 2 AM (script: ~/daily_backup.sh).</strong></summary>

```bash
vi /etc/crontab  
0 2 * * * ~/daily_backup.sh
```
</details>

<details>
<summary><strong>Create a job using at command that runs echo 'hello world' $(date) >> test.txt in 2 minutes.</strong></summary>

```bash
at '15:12'  
  echo 'hello world' $(date) >> test.txt  
  ctrl+d  
atq  
cat test.txt
```
</details>

<details>
<summary><strong>View and edit the current user's crontab. Create a cron job that runs date and save the output in cron.txt. The cron runs every five minutes, every day.</strong></summary>

```bash
crontab -l -u user  
crontab -e -u user  
*/5 * * * *
```
</details>

<details>
<summary><strong>Create a system-wide cron job in /etc/cron.d. Create a cron job that runs at 6 PM only on the first day of the month.</strong></summary>

```bash
vi /etc/cron.d/daily-backup
0 6 1 * * root /usr/local/bin/backup.sh
```
</details>

---

<details>
<summary><strong>What is dpkg?</strong></summary>

dpkg is the low‑level package manager for Debian‑based systems.

It is used to install, remove, and provide information about individual .deb packages, but <strong><em>it does not handle dependencies automatically.</em></strong>
</details>

<details>
<summary><strong>What is apt?</strong></summary>

APT (**Advanced Package Tool**) is a higher‑level package management system for Debian‑based systems. It works on top of dpkg and automatically handles package dependencies, updates, and repository management.
</details>

<details>
<summary><strong>Why update and upgrade the system at the start of the session?</strong></summary>

Updating and upgrading at the start ensures that the package lists and installed software are current. It provides the latest security patches, bug fixes, and feature improvements before you start working.
</details>

<details>
<summary><strong>How can I configure repositories? Write the path</strong></summary>

Repository configuration is stored in:  
/etc/apt/sources.list  
and in additional files under:  
/etc/apt/sources.list.d/
</details>

<details>
<summary><strong>What is a PPA? What is it used for?</strong></summary>

A PPA (**Personal Package Archive**) is a software repository hosted on Launchpad (primarily for Ubuntu) that allows developers to distribute their own packages.  
It is used to install software or newer versions of software that are not available in the official repositories.
</details>

<details>
<summary><strong>Check if git is installed with dpkg.</strong></summary>

```bash
dpkg -l git
```
</details>

<details>
<summary><strong>Update and upgrade packages.</strong></summary>

```bash
sudo apt upgrade -y && sudo apt update -y
```
</details>

<details>
<summary><strong>Install nginx.</strong></summary>

```bash
sudo apt install nginx -y
```
</details>

<details>
<summary><strong>Remove packages and their unused dependencies.</strong></summary>

```bash
sudo apt autoremove
```
</details>

<details>
<summary><strong>Search for Apache with the string 'Apache HTTP Server'.</strong></summary>

```bash
sudo apt search 'Apache HTTP Server'
```
</details>

<details>
<summary><strong>Show the policy of nginx.</strong></summary>

```bash
sudo apt-cache policy nginx
```
</details>

<details>
<summary><strong>Install software by compiling source code: clone the repository https://github.com/htop-dev/htop.git and install htop.</strong></summary>

```bash
git clone https://github.com/htop-dev/htop.git
cd htop  
cat README.md (follow the readme instructions)
```
</details>

<details>
<summary><strong>Add PPA: graphics-drivers/ppa. See the list of all repositories and then remove the ppa.</strong></summary>

```bash
sudo add-apt-repository ppa:graphics-drivers/ppa  
sudo add-apt-repository --list  
sudo add-apt-repository --remove ppa:graphics-drivers/ppa
```
</details>

---

<details>
<summary><strong>What is the Kernel?</strong></summary>

The kernel is the core part of an operating system. It acts as a **bridge between the hardware and software**, managing system resources such as CPU, memory, devices, and system calls from applications.

In Linux, the **kernel is monolithic**, meaning it contains core subsystems like process management, memory management, and device drivers in a single binary, with support for dynamically loadable modules.
</details>

<details>
<summary><strong>What are Kernel Modules?</strong></summary>

Kernel modules are pieces of code (often device drivers or system extensions) that can be loaded into or unloaded from the running kernel as needed, <strong><em>without requiring a system reboot</em></strong>.

For example, when you plug in a new hardware device, the appropriate module (driver) can be inserted into the kernel dynamically.

Modules can be managed with commands like:  
```bash
lsmod         # list loaded modules
modprobe      # load/unload modules
rmmod         # remove a module
```
</details>

<details>
<summary><strong>What are Kernel Runtime Parameters?</strong></summary>

Kernel runtime parameters are <em>configurable settings</em> that control kernel <em>behavior</em> while the system is running.

They can adjust networking, memory usage, process limits, and more—<strong><em>without recompiling or rebooting the kernel</em></strong>.

These parameters are typically managed through the <strong>/proc/sys/</strong> interface or using the sysctl command.

For example:  
sysctl -a (list all parameters),  
sysctl net.ipv4.ip_forward=1 (set a parameter temporarily),  or by editing /etc/sysctl.conf for persistent changes.
</details>

<details>
<summary><strong>List currently loaded kernel modules.</strong></summary>

```bash
lsmod
```
</details>

<details>
<summary><strong>View and modify kernel parameters temporarily (example: change net.ipv4.ip_forward).</strong></summary>

```bash
sysctl -a  
sysctl -a | grep net.ipv4.ip_forward  
sysctl -w net.ipv4.ip_forward=1
```
</details>

<details>
<summary><strong>Make kernel parameter changes persistent. Apply changes without rebooting.</strong></summary>

```bash
vi /etc/sysctl.d/ipv4-ip-forward.conf  
net.ipv4.ip_forward=1
sysctl -p /etc/sysctl.d/ipv4-ip-forward.conf
```
</details>

---

<details>
<summary><strong>What is SELinux?</strong></summary>

SELinux (**Security-Enhanced Linux**) is a **Linux kernel security module** that provides **mandatory access control (MAC)**.  
It enforces strict rules on processes and users to control access to files, devices, and resources, reducing the risk of privilege escalation or system compromise.

If using Ubuntu (AppArmor by default), these tasks may be tested conceptually or on Red Hat images.
</details>

<details>
<summary><strong>Check SELinux status and mode.</strong></summary>

```bash
sestatus
```
</details>

<details>
<summary><strong>Change SELinux mode from enforcing to permissive (or vice versa).</strong></summary>

```bash
getenforce  
setenforce 1
```
</details>

<details>
<summary><strong>View and manage SELinux file contexts</strong></summary>

Create a directory named selinux and create 10 empty files inside it named file1 through file10. Display the current SELinux contexts of the files in that directory.

List all currently defined SELinux file context rules on the system. Add a rule so that all files inside the selinux directory will be assigned the httpd_sys_rw_content_t context.

Apply the changes so that the new context is set on the files. Verify that the files in the selinux directory have the new SELinux context.

```bash
mkdir selinux  
touch selinux/file{1..10}  
ls -Z selinux/  
semanage -l fcontext   
semanage fcontext -a -t httpd_sys_rw_content_t /home/user/selinux/ # important! The path must be absolute
restorecon -vR /home/user/selinux/
```
</details>

<details>
<summary><strong>Manage SELinux port contexts</strong></summary>

Add a rule to allow the http_port_t type to be associated with TCP port 82.  
List all SELinux port context rules and filter the output to show only entries related to HTTP.

```bash
semanage port -a -t http_port_t -p tcp 82  
semanage port -l | grep http
```
</details>

<details>
<summary><strong>Change boolean value to httpd_can_connect_zabbix.</strong></summary>

```bash
semanage boolean -l | grep  httpd_can_connect_zabbix  
getsebool httpd_can_connect_zabbix  
setsebool httpd_can_connect_zabbix=1
```
</details>

---

<details>
<summary><strong>What is QEMU-KVM?</strong></summary>

**QEMU** is an open-source machine emulator and virtualizer.  
**KVM** (Kernel-based Virtual Machine) is a Linux kernel module that allows the host to use hardware virtualization.  
Together, **QEMU-KVM** enables efficient full virtualization on x86 hardware, allowing you to run virtual machines with near-native performance.
</details>

<details>
<summary><strong>What is virsh?</strong></summary>

virsh is a command-line interface to **manage virtual machines** using libvirt.  
You can use it to start, stop, create, delete, and configure VMs, as well as manage storage and networking.
</details>

<details>
<summary><strong>List available virtual machines.</strong></summary>

```bash
virsh list --all
```
</details>

<details>
<summary><strong>Create a virtual machine using virt-install (or a graphical tool such as virt-manager).</strong></summary>

Name the virtual machine lfcsa-vm1.  
Allocate 2 GB of RAM and 2 virtual CPUs.  
Create a new 10 GB disk image in /var/lib/libvirt/images/lfcsa-vm1.qcow2.  
Use the installation ISO located at /var/lib/libvirt/boot/CentOS-Stream-9.iso.  
Connect the VM to the default network (default).  
Configure it for a paravirtualized disk and network.

```bash
virt-install \
  --name lfcsa-vm1 \
  --memory 2048 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/lfcsa-vm1.qcow2,size=20,format=qcow2,bus=virtio \
  --cdrom /var/lib/libvirt/boot/CentOS-Stream-9.iso \
  --os-variant centos-stream9 \
  --network network=default,model=virtio \
  --graphics vnc,listen=0.0.0.0 \
  --noautoconsole
```
</details>

<details>
<summary><strong>Start, stop, and destroy a virtual machine.</strong></summary>

Using virsh:

* Start a VM: virsh start &lt;vm-name&gt;  
* Shutdown a VM gracefully: virsh shutdown &lt;vm-name&gt;  
* Force stop a VM: virsh destroy &lt;vm-name&gt;
</details>

<details>
<summary><strong>Attach an additional disk to a VM and verify from the guest.</strong></summary>

1. Use virsh attach-disk to attach a new virtual disk to the VM:  
    ```bash
    virsh attach-disk <vm-name> /path/to/disk.img vdb --persistent
    ```

2. From inside the guest OS, check for the new disk with lsblk or fdisk -l.
</details>

<details>
<summary><strong>Ensure virtualization is enabled in your system BIOS and KVM modules are loaded.</strong></summary>

1. Check CPU virtualization support:  
    ```bash
    egrep -c '(vmx|svm)' /proc/cpuinfo (output > 0 means it's supported)
    ```

2. Verify KVM modules are loaded:  
    ```bash
    lsmod | grep kvm
    ```
    You should see kvm_intel or kvm_amd depending on your CPU.

3. If virtualization is disabled, reboot and enable VT-x/AMD-V in BIOS/UEFI settings.
</details>

---

<details>
<summary><strong>What is Docker?</strong></summary>
</details>

<details>
<summary><strong>What are the three Linux features that are needed in container environments?</strong></summary>

* **Namespaces** – isolate processes, networking, filesystems, etc.  
* **Control groups (cgroups)** – limit and monitor resources (CPU, RAM, I/O) for containers.  
* **Union file systems (copy‑on‑write layered filesystems)** – allow sharing and layering of the container image.
</details>

<details>
<summary><strong>Which file defines the registries that are currently used?</strong></summary>

/etc/containers/registries.conf  
(You can also have additional configurations in /etc/containers/registries.d/.)
</details>

<details>
<summary><strong>[EXTRA] What does "rootless" mean in Podman?</strong></summary>

Running containers **rootless** means the containers are run by an unprivileged user without root permissions. Podman uses user namespaces to allow this, improving security.
</details>

<details>
<summary><strong>[EXTRA] Which are the Podman advantages?</strong></summary>

* **Rootless**: containers can be started by ordinary users that do not need any elevated privileges.  
* **Isolate environment**: When users start containers, the containers run in a user namespace where they are strictly isolated and not accessible to other users.  
* **Daemonless**: Podman containers run on top of the lightweight CRI-o container runtime, without needing any daemon to do their work.
</details>

<details>
<summary>Pull the nginx image.</summary>

```bash
docker pull nginx
```
</details>

<details>
<summary>Show all images.</summary>

```bash
docker image ls
```
</details>

<details>
<summary>Run a container in detached mode.</summary>

```bash
docker run -d -p 8080:80 --name myserver nginx
```
</details>

<details>
<summary>Show all running containers.</summary>

```bash
docker ps -a
```
</details>

<details>
<summary>Stop and remove the container. Remove the nginx image.</summary>

```bash
docker stop <id-container>  
docker remove <id-container>  
docker rm nginx
```
</details>

<details>
<summary>Create a basic Dockerfile to install nginx.</summary>

```bash
touch Dockerfile  
vi Dockerfile
```

```dockerfile
FROM nginx  
COPY index.html /usr/share/nginx/html/index.html
```
</details>

<details>
<summary>Build an image from the Dockerfile.</summary>

```bash
docker build -t custom-nginx:1.0 -f Dockerfile .
docker run -d -p 8080:80 --name myserver custom-nginx:1.0
```

</details>

