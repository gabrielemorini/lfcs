## 1. Essentials Commands

<details>
<summary><strong>Upgrade the Manual</strong></summary>
mandb
</details>

<details>
<summary><strong>What is the difference between an absolute path and a relative path?</strong></summary>
The absolute path starts to root ('/'). The relative path depends on the current directory.
</details>

<details>
<summary><strong>Create file1 using an absolute path and file2 using a relative path.</strong></summary>

```bash
touch /home/user/file1
touch ./file2
```
</details>

<details>
<summary><strong>What is the difference between alias, internal commands, and external commands?</strong></summary>

* **Alias**: An alias is a shortcut that maps a custom word or phrase to a longer command or a command with options.
* **Internal commands**: These are built into the shell itself (e.g. cd, echo, export). They do not require starting a separate process.
* **External commands**: These are standalone programs stored on disk (e.g. /bin/ls, /usr/bin/grep). They are executed by launching a separate process.

You can use **type**, **which**, and check your **$PATH** to determine how a command is resolved.
</details>

<details>
<summary><strong>What is the difference between a shell and a subshell?</strong></summary>

A **shell** is your main command interpreter session.  
A **subshell** is a child shell process started from the main shell (for example, when you run a script or use parentheses `(...)` ). It has its own environment, and changes (like cd) do not affect the parent shell.
</details>

<details>
<summary><strong>What is a hard link? What is a soft link?</strong></summary>

Hard link can be linked just the same filesystem and is an identical copy of the original file. It is a point to inode, they are effectively the same file.
Soft link (or symbolic link) is a link with different inode. It exists just if the original file exists.
</details>

<details>
<summary><strong>Create a hard link to <code>file1</code> and a soft link to <code>file2</code>. Show the differences using the <code>stat</code> and <code>file</code> commands.</strong></summary>

```bash
ln ./file1 hard-file1
ln -s ./file2 soft-file2
```
</details>

<details>
<summary><strong>What is SUID? Give an example.</strong></summary>

Set User ID is a special permission that allows the user to run an executable with the permissions of the executable's owner.

<em>example</em>: ls -l /usr/bin/passwd
</details>

<details>
<summary><strong>What is SGID?</strong></summary>

Set Group ID is a special permission. In the directory, normally, a directory takes the primary group of the user who creates it. With SGID set, all files and subdirectories inherit the group of the directory itself.
</details>

<details>
<summary><strong>What is the Sticky Bit? Give an example.</strong></summary>

The sticky bit is a special permission set on a directory that restricts file deletion and renaming. When the sticky bit is enabled, only the file's owner, the directory's owner, or the root user can delete or rename files within that directory, even if other users have write permissions on it.

<em>example</em>: ls -ld /tmp
</details>

<details>
<summary><strong>Create three different files (or directories if needed) with SUID, SGID, and the Sticky Bit set.</strong></summary>

```bash
touch suid  
chmod +4644 (or u+s)
mkdir sgid  
chmod +2644 (or g+s)
mkdir sticky  
chmod +1644 (or +t)
```
</details>

---

<details>
<summary><strong>How can you find a file on the system?</strong></summary>
</details>

<details>
<summary><strong>Find files with <code>755</code> permissions in the <code>/</code> directory and copy the list to <code>lfcs.txt</code>.</strong></summary>

```bash
find / -type f -perm 755 -exec ls {} lfcs.txt \;
```
</details>

<details>
<summary><strong>Find all <code>.conf</code> files in the <code>/etc</code> directory and list them with their full path.</strong></summary>

```bash
find /etc -type f -name *.conf
```
</details>

<details>
<summary><strong>What are the commands for text manipulation?</strong></summary>
</details>

<details>
<summary><strong>Use <code>cat</code> and <code>tac</code> to display <code>lfcs.txt</code>.</strong></summary>

```bash
cat lfcs.txt
tac lfcs.txt
```
</details>

<details>
<summary><strong>Use tail and head to show the first and the last 5 lines of lfcs.txt.</strong></summary>

```bash
tail -n 5 lfcs.txt
head -n 5 lfcs.txt
```
</details>

<details>
<summary><strong>Use sed to replace .conf with .lfcs.</strong></summary>

```bash
sed -i "s/\.conf/\.lfcs/g" lfcs.txt
```
</details>

<details>
<summary><strong>Use less and more to view the results.</strong></summary>

```bash
less lfcs.txt
more lfcs.txt
```
</details>

<details>
<summary><strong>Use grep to search for the word password in /etc/ssh/sshd_config.</strong></summary>

```bash
grep -i 'password' /etc/ssh/sshd_config
```
</details>

<details>
<summary><strong>What are STDIN, STDOUT, and STDERR? Explain the concepts.</strong></summary>

STDIN means <em>standard input</em>. It is the data provided to a command as input.  
STDOUT means <em>standard output</em>. It is the normal output produced by a command after it runs successfully.  
STDERR means <em>standard error</em>. It is the output stream used to display error messages produced by a command.
</details>

<details>
<summary><strong>Redirect output to write Hello World into redirect.txt.</strong></summary>

```bash
echo 'Hello World' > redirect.txt
```
</details>

<details>
<summary><strong>Append Linux Exam to redirect.txt.</strong></summary>

```bash
echo 'Linux Exam' >> redirect.txt
```
</details>

<details>
<summary><strong>Redirect both STDOUT and STDERR.</strong></summary>

```bash
echo "both" > redirect.txt 2>&1
```

in modern shell:  
```bash
echo "both" &> redirect.txt
```
</details>

<details>
<summary><strong>Redirect a command's input using STDIN.</strong></summary>

```bash
cat < redirect.txt
```
</details>

---

<details>
<summary><strong>What is tar? What is a tarball?</strong></summary>

**Tar** stands for <em>Tape Archive</em>. It is a Linux/Unix command used to **combine multiple files and directories into a single archive file** (without compression by itself).  
The resulting archive file is commonly called a **tarball**, usually with the <em>.tar</em> extension.
</details>

<details>
<summary><strong>What is compression? Which commands are used to compress a tarball? Which commands are used to uncompress it?</strong></summary>

**Compression** is the process of **reducing the size of a file** by encoding its data more efficiently. A tarball created with **tar** can then be compressed to save space.

Common commands for compression and decompression: gzip, bzip2, xz

**Workflow:**  
<em>Create an archive</em> with **tar** → <em>Compress it</em> with **gzip**, **bzip2**, or **xz** → <em>Store or back up</em> the compressed file.
</details>

<details>
<summary><strong>Compress a directory into a <code>.tar.gz</code> archive (for example <code>/tmp/</code>). Check the file in the archive.</strong></summary>

```bash
tar cvzf tmp.tar.gz /tmp
tar tf tmp.tar.gz
```
</details>

<details>
<summary><strong>Compress a directory using <code>gzip</code>, <code>bzip2</code>, and <code>xz</code>.</strong></summary>

```bash
tar cvf tmp.tar /tmp
gzip tmp.tar  
bzip2 tmp.tar  
xz tmp.tar
```

Check:  
```bash
file tmp.tar.gz  
file tmp.tar.bz  
file tmp.tar.xz
```
</details>

<details>
<summary><strong>Extract an archive into a specific directory (use the <code>.tar.gz</code> file you created).</strong></summary>

```bash
mkdir lfcs  
tar xvf tmp.tar.gz -C lfcs
```
</details>

<details>
<summary><strong>What is a full backup?</strong></summary>

A full backup is a complete copy of all selected data. Every file and folder is backed up, regardless of whether it has changed since the last backup.  
It provides the fastest restore process but requires the most storage space and time to create.
</details>

<details>
<summary><strong>What is an incremental backup?</strong></summary>

An incremental backup copies only the data that has changed since the last backup (whether full or incremental). It saves storage space and backup time, but restoring requires the last full backup and all subsequent incremental backups.
</details>

<details>
<summary><strong>What is a differential backup?</strong></summary>

A differential backup copies all data that has changed since the last full backup.  
It grows in size with each run until the next full backup, but the restore process requires only the last full backup and the latest differential backup.
</details>

<details>
<summary><strong>Write a backup strategy</strong></summary>

A common and effective backup strategy is the 3-2-1 rule:
* Keep 3 copies of your data (1 primary and 2 backups).
* Store the copies on 2 different types of media (for example, internal drive and external drive or cloud storage).
* Keep 1 copy off-site (such as in the cloud or in a different physical location) to protect against local disasters.
</details>

<details>
<summary><strong>Perform an incremental backup excluding specific files.</strong></summary>

**First full backup**:
```bash
sudo tar cvzf full-backup.tar.gz --listed-incremental=backup.snar --exclude=secret.txt /path/to/data
```

**Later incremental backup**:  
Run again with the same snapshot file:
```bash
sudo tar cvzf incr-backup.tar.gz --listed-incremental=backup.snar --exclude=secret.txt /path/to/data
```

hello-backup.tar.gz = your archive  
hello.snar = snapshot file for incremental tracking  
--exclude=secret.txt = file you don't want in the backup
</details>

<details>
<summary><strong>What is remote synchronization?</strong></summary>
</details>

<details>
<summary><strong>Synchronize two local directories (for example  <code>/tmp/</code> and  <code>backup/</code>).</strong></summary>

```bash
rsync -r /tmp backup/
```
</details>

<details>
<summary><strong>Use <code>rsync</code> to synchronize a folder between two different virtual machines.</strong></summary>

```bash
sudo rsync ubuntu@192.168.1.155:/tmp /home/user/lfcs
```
</details>

<details>
<summary><strong>What is disk imaging? What do <code>if</code> and <code>of</code> mean in the <code>dd</code> command?</strong></summary>

Disk imaging is a process of creating an exact, byte-for-byte copy of a computer's hard drive or other storage device, including the operating system, applications, settings, and all data

if = input file  
of = output file
</details>

<details>
<summary><strong>Create and restore an image of a file or disk (for example /dev/sr0).</strong></summary>

```bash
dd if=/dev/sr0 of=/dev/sr1 status=progress
```
</details>

---

<details>
<summary><strong>What are SSL and TLS?</strong></summary>

SSL (**Socket Security Layer**) is a security protocol originally developed by Netscape in the 1990s to enable encrypted communication over the internet.

It provides:  
- <strong><em>Encryption</em></strong> (hides data so others can't read it),  
- <strong><em>Authentication</em></strong> (verifies you're talking to the real server),  
- <strong><em>Integrity</em></strong> (ensures data isn't tampered with in transit).  

SSL was widely used in early versions (SSL 2.0, SSL 3.0), but over time vulnerabilities were discovered, and it is now deprecated.

TLS (**Transport Security Layer**) is the modern, more secure successor to SSL.  
It's essentially SSL 3.1 and beyond, standardized by the IETF, with better algorithms and stronger security. Current versions include TLS 1.2 and TLS 1.3 (most widely used today). 

<strong><em>Relationship between SSL and TLS</em></strong>

People often still say "SSL certificates," but in reality almost everyone uses TLS today.  
TLS is the protocol, while an "SSL certificate" is really just an X.509 certificate used in both SSL and TLS handshakes.
</details>

<details>
<summary><strong>What is a certificate?</strong></summary>

A **digital certificate** is like a digital ID card used on computer networks to prove identity and enable secure communication.

It's a file (usually in formats like .crt, .pem, .der) that contains:
- Who you are (for example, www.example.com)  
- Your public key (used for encryption)  
- Who issued it (a trusted Certificate Authority)  
- Validity period (start date, expiration date)  
- A digital signature from the issuer (proves it hasn't been tampered with)
</details>

<details>
<summary><strong>What is a self-signed certificate?</strong></summary>

A self‑signed certificate is a certificate that is **signed by the same entity that created it**, instead of by a trusted Certification Authority (CA).

It is not trusted by default in browsers or operating systems, but it is commonly used in private networks, internal services, or testing environments where external trust is not required.
</details>

<details>
<summary><strong>What is a Certificate Signing Request (CSR)?</strong></summary>

A CSR is a file containing your public key and identifying information, signed with your private key, that you send to a **Certificate Authority (CA**) ( es. Let's Encrypt, DigiCert ) to request a digital certificate.

How authentication with this certificate works:  
After the CA issues the certificate:
* The server installs the certificate (public) and keeps its private key secure.
* During a client connection (e.g., HTTPS):
  1. The server presents the certificate.
  2. The client verifies it is valid and signed by a trusted CA.
  3. The client and server establish an encrypted session using the keys.
</details>

<details>
<summary><strong>Create a self-signed certificate</strong></summary>

```bash
openssl req -x509 -newkey rsa:2048
```
</details>

<details>
<summary><strong>Create a Certificate Signing Request (CSR).</strong></summary>

```bash
openssl req -new -newkey rsa:2048 -nodes \   
  -keyout /etc/ssl/private/example.com.key \   
  -out /etc/ssl/certs/example.com.csr
```
</details>

<details>
<summary><strong>(EXTRA) Create a Certificate Signing Request (CSR) and use CA to trust it. Getting started: https://letsencrypt.org/getting-started/</strong></summary>
</details>

---

<details>
<summary><strong>What is Git?</strong></summary>
</details>

<details>
<summary><strong>Which is the difference between merge and rebase?</strong></summary>
</details>

<details>
<summary><strong>Initialize a new local repository.</strong></summary>

```bash
mkdir local  
git init
```
</details>

<details>
<summary><strong>Commit changes with a message. Before, If needed, configure git.</strong></summary>

```bash
touch README.md  
git status  
git add .  
git commit -m 'first message'  
git log
```
</details>

<details>
<summary><strong>Create a new branch, work on it, and merge it into the main branch.</strong></summary>

```bash
git checkout -b 'new-branch'  
touch lfcs.txt  
git add .  
git commit -m "Added lfcs.txt"  
git checkout master (or main)  
git log --one-line  
git merge new-branch  
git log --graph --decorate  
git branch -d new-branch
```
</details>

<details>
<summary><strong>Use stash to save temporary changes.</strong></summary>

```bash
git checkout -m "second-branch"
touch rhcsa.txt  
git add .  
git stash  
git stash list  
ls  
git stash pop
touch rhce.txt  
git stash -u  
git stash list  
ls  
git stash pop
```
</details>

<details>
<summary><strong>Add a remote and push (create a Gitea account).</strong></summary>

```bash
git remote -v  
git remote add origin https://gitea.com/name-of-user-change-this/local.git 
git push master origin
```
</details>

<details>
<summary><strong>Clone a repository from GitHub. Use https://github.com/htop-dev/htop.git</strong></summary>

```bash
git clone https://github.com/htop-dev/htop.git
```
</details>

<details>
<summary><strong>Show the commit history with a compact graph.</strong></summary>

```bash
git log --graph --decorate
```
</details>