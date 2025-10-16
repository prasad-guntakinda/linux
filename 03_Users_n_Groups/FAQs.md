# FAQs on Linux Users and Groups


---

<details>
<summary> What is the difference b/w System Account and Service Accounts?</summary>

- In Linux, the terms "system account" and "service account" are often used interchangeably, as both are non-human accounts for running programs and services rather than interactive users. 
- The main distinction is that a system account is typically for core operating system functions and is created during installation, while a service account is for a specific application or service, like a web server, and can be created at any time. 
- Both are typically associated with a low User ID (UID) to separate them from regular users and often lack a valid login shell and a home directory in /home. 


| Feature         | System Accounts                                                              | Service Accounts                                                                                |
| --------------- | ---------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| Primary Purpose | Running core OS processes and services                                       | Running specific applications or services, like a web server or backup process                  |
| Creation        | Usually created during the OS installation                                   | Created for individual applications or services                                                 |
| User ID (UID)   | Often have very low UIDs, sometimes below 100, to be reserved for system use | Also typically have low UIDs, sometimes in the 100-499 range, to be distinct from regular users |
| Login Shell     | May or may not have a login shell, depending on their function               | Usually do not have a valid login shell                                                         |
| Home Directory  | Typically do not have a home directory, especially in /home                  | Less likely to have a home directory in /home, though some may have a designated one            |

### Key differences 

- __Scope:__ System accounts are for the OS itself, while service accounts are for individual applications and are more granular.

- __Management:__ System accounts are managed by the OS, whereas service accounts are created and managed specifically for the applications they serve. 
  
- __Usage:__ System accounts perform essential system tasks, while service accounts are used to grant an application identity and permissions to access resources without a human user's credentials.  

</details>

---


<details>
<summary> What is sudo ? </summary>

- **sudo** (Super User DO or substitute user do) is a command-line utility in Linux and other Unix-like operating systems that allows a permitted user to execute a command with the security privileges of another user, by default the superuser (root).


### Key aspects of sudo: 

- **Privilege Elevation:** It grants temporary elevated privileges, typically root access, to perform tasks that require administrative permissions, such as installing software, modifying system files, or managing services.


- **Security:** Instead of logging in directly as the root user, sudo allows specific users to run individual commands with root privileges, reducing the risk of accidental damage to the system. 


- **Authentication:** When a user invokes sudo, they are typically prompted to enter their own password for authentication, not the root password. This ensures that only authorized users can leverage the sudo command. 


- **sudoers file:** The ``/etc/sudoers`` file defines which users or groups are allowed to use sudo and what commands they can execute. This file is managed using the visudo command to prevent syntax errors. 


- **Audit Trail:** sudo logs all commands executed with elevated privileges, providing a valuable audit trail for system administrators to monitor user activity and troubleshoot issues. 

### Example Usage: 

````shell

# Adding new user
sudo useradd -m ram

#To update system packages, which requires root privileges: 
sudo apt update

#To install a new software package: 
sudo apt install nginx

#To edit a system configuration file like /etc/fstab: 
sudo nano /etc/fstab

````


</details>

---

