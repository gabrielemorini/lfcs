<details>
<summary><strong>What is firmware?</strong></summary>
<strong>Firmware</strong> is a special class of software that is stored on non-volatile memory (such as ROM, flash memory, or EEPROM) on a hardware device.<br>
It provides the low-level instructions that allow hardware to start up, operate, and communicate with higher-level software (like the operating system).

Examples:
<ul>
<li><strong>BIOS</strong> and <strong>UEFI</strong> on a motherboard, which initialize hardware and load the bootloader.</li>
<li>Firmware inside network cards, SSDs, printers, routers, or embedded devices that controls their basic functions.</li>
</ul>

Unlike normal software, firmware is tightly coupled to the hardware and usually updated less frequently (via a "firmware update").
</details>

<details>
<summary><strong>What are BIOS and UEFI?</strong></summary>
<ul>
<li><strong>BIOS: Basic Input/Output System</strong> - Legacy firmware interface that initializes hardware and boots the system.</li>
<li><strong>UEFI: Unified Extended Firmware Interface</strong> - Modern replacement for BIOS with support for larger disks, secure boot, and a more flexible pre-boot environment.</li>
</ul>
</details>

<details>
<summary><strong>What is a bootloader?</strong></summary>
A <strong>bootloader</strong> is software that loads the operating system kernel into memory and starts it after the firmware (BIOS/UEFI) has done hardware initialization.
</details>

<details>
<summary><strong>What is GRUB2? What is its configuration file?</strong></summary>
<strong>GRUB2 (GRand Unified Bootloader v2):</strong> A modern bootloader used by most Linux distributions.<br>
Main configuration file: /etc/default/grub (after editing, regenerate with grub2-mkconfig).
</details>

<details>
<summary><strong>What is chroot?</strong></summary>
<strong>chroot</strong> changes the apparent root directory for a running process, isolating it in a limited portion of the filesystem (often used for recovery or testing).
</details>

<details>
<summary><strong>Can you describe the key steps a system goes through from the moment it powers on to when the login screen appears?</strong></summary>
The following steps summarize how the boot procedure happens on Linux:

<ol>
<li><strong>Performing POST:</strong><br>
The machine is powered on. From the system firmware, which can be the modern Universal Extended Firmware Interface (UEFI) or the classical Basic Input/Output System (BIOS), the <strong>Power-On Self-Test (POST)</strong> is executed, and the hardware that is required to start the system is initialized.</li>

<li><strong>Selecting the bootable device:</strong><br>
Either from the <strong>UEFI</strong> boot firmware or from the <strong>BIOS</strong>, a bootable device is located.</li>

<li><strong>Loading the boot loader:</strong><br>
From the bootable device, a boot loader is located. On RHEL, this is usually <strong>GRUB 2</strong>.</li>

<li><strong>Loading the kernel:</strong><br>
The boot loader may present a boot menu to the user or can be configured to automatically start a default operating system. To load Linux, the kernel is loaded together with the initramfs. The <strong>initramfs</strong> contains kernel modules for all hardware that is required to boot, as well as the initial scripts required to proceed to the next stage of booting. On RHEL 9, the initramfs contains a complete operational system (which may be used for troubleshooting purposes).</li>

<li><strong>Starting /sbin/init:</strong><br>
Once the kernel is loaded into memory, the first of all processes is loaded, but still from the initramfs. This is the <strong>/sbin/init process</strong>, which on RHEL is linked to Systemd. The <strong>systemd-udevd daemon</strong> is loaded as well to take care of further hardware initialization. All this is still happening from the initramfs image.</li>

<li><strong>Processing initrd.target:</strong><br>
The <strong>Systemd</strong> process executes all units from the initrd.target, which prepares a minimal operating environment, where the root file system on disk is mounted on the <strong>/sysroot</strong> directory. At this point, enough is loaded to pass to the system installation that was written to the <strong>hard drive</strong>.</li>

<li><strong>Switching to the root file system:</strong><br>
The system switches to the <strong>root file system</strong> that is on disk and at this point can load the Systemd process from disk as well.</li>

<li><strong>Running the default target:</strong><br>
Systemd looks for the <strong>default target</strong> to execute and runs all of its units. In this process, a login screen is presented, and the user can authenticate. Note that the login prompt can be prompted before all Systemd unit files have been loaded successfully. So, seeing a login prompt does not necessarily mean that your server is fully operational yet; services may still be loaded in the background.</li>
</ol>
</details>

<details>
<summary><strong>Can you list and explain the main troubleshooting scenarios you might face during the Linux boot process—such as issues with GRUB2, initramfs, filesystems, kernel panic, or login failure—and describe how you would resolve them using tools like rescue.target, emergency.target, or dracut?</strong></summary>

<strong>1. Corrupted or missing initramfs</strong><br>
Typical causes: interrupted kernel updates, full /boot partition, misconfigured kernel version.

<em>Resolution:</em><br>
Boot from installation media and select rescue mode.<br>
Choose to mount your installed system under <strong>/mnt/sysimage</strong>.<br>
Enter chroot environment: <strong>chroot /mnt/sysimage</strong><br>
Rebuild initramfs: <strong>dracut -f /boot/initramfs-$(uname -r).img $(uname -r)</strong><br>
Reboot the system.

<strong>2. GRUB2 corruption or misconfiguration</strong><br>
Causes: manual misediting, broken updates, missing GRUB files.

<em>Resolution:</em><br>
Boot from a live ISO or rescue disk.<br>
Mount root and bind necessary filesystems:
```bash
mount /dev/sdXn /mnt
mount --bind /dev /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys /mnt/sys
chroot /mnt
```
Reinstall and regenerate GRUB:
```bash
grub2-install /dev/sdX
grub2-mkconfig -o /boot/grub2/grub.cfg
```
Reboot.

<strong>3. Damaged or missing filesystem</strong><br>
Symptoms: "cannot mount root fs", system drops to initramfs shell.<br>
<em>Resolution:</em><br>
Boot into rescue mode or emergency mode.<br>
Use <strong>lsblk</strong>, <strong>blkid</strong>, and <strong>cat /etc/fstab</strong> to identify issues.<br>
Run filesystem check: <strong>fsck -f /dev/sdXn</strong><br>
Mount partitions manually if needed.<br>
Correct <strong>/etc/fstab</strong> if UUIDs or paths are incorrect.

<strong>4. Using rescue.target and emergency.target</strong><br>
When to use:<br>
<strong>emergency.target</strong>: minimal shell with read-only root, no services (useful for critical recovery).<br>
<strong>rescue.target</strong>: read-write root, basic services, useful for debugging without full boot.

How to boot into them:<br>
From the GRUB menu, press <strong>e</strong>, append to the kernel line:<br>
<strong>systemd.unit=emergency.target</strong><br>
or<br>
<strong>systemd.unit=rescue.target</strong><br>
Press <strong>Ctrl+X</strong> to boot.

<strong>5. Kernel panic</strong><br>
Symptoms: "VFS: Unable to mount root fs", system halts with stack trace.<br>
Causes: missing modules, broken initramfs, root device not found, hardware failure.

<em>Resolution:</em><br>
Boot into rescue mode or live system.<br>
Check /boot contents and kernel/initramfs versions.<br>
Enter chroot: <strong>chroot /mnt/sysimage</strong><br>
Rebuild initramfs: <strong>dracut -f /boot/initramfs-.img</strong><br>
Verify disk health with <strong>smartctl</strong>, <strong>memtest</strong>, or <strong>badblocks</strong><br>
Check for missing kernel modules.

<strong>6. Root password reset</strong><br>
<em>Method 1 – using rd.break:</em>

From GRUB, press <strong>e</strong>, add <strong>rd.break</strong> to the end of the kernel line.<br>
Press <strong>Ctrl+X</strong> to boot.<br>
In the initramfs shell:
```bash
mount -o remount,rw /sysroot
chroot /sysroot
passwd
touch /.autorelabel
exit
exit
```
System reboots and re-labels SELinux.

<em>Method 2 – using init=/bin/bash:</em><br>
At the GRUB screen, press <strong>e</strong> to edit.<br>
On the kernel line, replace <strong>rhgb quiet</strong> with <strong>init=/bin/bash</strong><br>
Boot with <strong>Ctrl+X</strong><br>
At the bash shell:
```bash
mount -o remount,rw /
passwd
touch /.autorelabel
exec /sbin/init
```

<strong>7. Boot fails, system hangs (e.g. black screen or no login)</strong><br>
Possible causes: failed systemd services, corrupted display manager (GDM), broken graphical drivers.

<em>Resolution:</em><br>
Switch to TTY: <strong>Ctrl+Alt+F3</strong><br>
Check failed units: <strong>systemctl --failed</strong>, <strong>journalctl -xb</strong><br>
If it's a graphical issue:
```bash
systemctl isolate multi-user.target
dnf reinstall gdm
```
Set correct default target if needed:
```bash
systemctl get-default
systemctl set-default graphical.target
```
</details>
