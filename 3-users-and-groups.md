## 3. User and Groups

<details>
<summary><strong>What is the difference between writing an export variable in .bashrc, /etc/environment, or /etc/profile.d?</strong></summary>

* **.bashrc** applies only to a single user (it's sourced for that user's interactive non-login shells).  
* **/etc/environment** is for system‑wide, <strong><em>static</em></strong> environment variables (applied to all users, typically at login, but it doesn't support shell expansions or complex logic).  
* **/etc/profile.d/** is a directory where you can place <strong><em>scripts</em></strong> that set environment variables or other settings for login shells system‑wide.
</details>

<details>
<summary><strong>You can limit the user options. What is the difference between a hard limit and a soft limit?</strong></summary>

* A **soft limit** is the current effective limit that can be changed by the user up to the hard limit.  
* A **hard limit** is the maximum value that even root can only raise with special configuration. Users cannot increase their soft limit beyond the hard limit.
</details>

<details>
<summary><strong>What is LDAP?</strong></summary>

**Lightweight Directory Access Protocol** (LDAP) is a protocol used to access and maintain distributed directory information services.  
It is commonly used to store user accounts, organizational data, and to authenticate users against a central directory.

In many environments, it serves as the backbone for authentication and authorization, allowing multiple systems and applications to verify user credentials and permissions against a single, centralized directory.
</details>

<details>
<summary><strong>Create a system user sysuser without a home directory and with shell /usr/sbin/nologin or /bin/false.</strong></summary>

```bash
useradd --system --shell /usr/sbin/nologin sysuser
```
</details>

<details>
<summary><strong>Confirm the user creation with and check it is marked as system user (UID typically < 1000 or per distro policy).</strong></summary>

```bash
getent passwd | grep sysuser
```
</details>

<details>
<summary><strong>Add a global environment variable APP_ENV=production. Add a session environment variable SES_ENV=session. Verify variable availability after login.</strong></summary>

```bash
vi /etc/environment  
APP_ENV=production  
source /etc/environment  
echo $APP_ENV

vi /etc/profile.d/lastlogin.sh  
SES_ENV=session  
source /etc/profile.d/lastlogin.sh  
echo $SES_ENV
```
</details>

<details>
<summary><strong>Set soft and hard limits for nofile (open files) for testuser. If needed, create a testuser.</strong><br><strong>Verify limits when logged in as testuser.</strong></summary>

```bash
vi /etc/security/limits.conf  
testuser   soft  nofile 10  
testuser   hard  nofile 20  
ulimit -a
```
</details>

<details>
<summary><strong>Create a sudo user adminuser who can execute all commands without password prompt. Verify sudo privileges.</strong></summary>

```bash
vi /etc/sudoers.d/adminuser  
adminuser ALL=(ALL)  NOPASSWD: ALL  
su - adminuser  
sudo apt install nmap -y
```
</details>

<details>
<summary><strong>Install and configure LDAP client authentication</strong></summary>

Install LDAP client tools (sssd or libnss-ldap).  
```bash
apt install libnss-ldap
```

Configure /etc/nsswitch.conf and /etc/sssd/sssd.conf or /etc/ldap.conf to authenticate users via LDAP server. Validate LDAP users with getent passwd and id username.
</details>