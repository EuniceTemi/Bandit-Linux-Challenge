# 🌐 Bandit-Linux-Challenge

## 📝 Overview
This project documents my journey through the **OverTheWire: Bandit** challenge. The goal of this project is to build a solid, practical understanding of the Linux command line (CLI), networking basics, and core security concepts. 

Through this hands-on lab, I configure a local Virtual Machine (VM) environment to connect to remote servers, navigate directories, and manipulate data using core Linux utilities.

Skills Demonstrated: Linux CLI, SSH, Cryptography, File Permissions, Data Filtering.


## Environment & Tools
* **Host OS:** Windows 11
* **Hypervisor:** VirtualBox
* **Guest OS (VM):** Kali Linux
* **Target:** OverTheWire Bandit Servers (`bandit.labs.overthewire.org`)
* **Protocols Used:** SSH



## 🚀 Technical Skills Acquired
* **File Management:** `cat`, `ls`, `cd`, `find`
* **Data Processing:** `grep`, `sort`, `uniq`, `strings`, `base64`
* **Networking:** `ssh`
* **Permissions:** `chmod`

---

## 📖 Level-by-Level Walkthrough

### 🔒 Level 0 ➔ Level 1
* **Objective:** Log into the game server using SSH and locate the password hidden in a file named `readme` in the home directory.
* **Concept Learned:** Reading text in files.
* **Command Executed:**
  ```bash
  # Step 1: Connect via SSH using the provided port
  ssh bandit0@bandit.labs.overthewire.org -p 2220

  # Step 2: Read the file using the relative path prefix
  cat readme
  ```
* **Methodology:** Using th `cat` command to read text in a file.

### 🔒 Level 1 ➔ Level 2
* **Objective:** Log into the game server using SSH and locate the password hidden in a file named `-` in the home directory.
* **Concept Learned:** Handling special characters/hyphens in filenames.
* **Command Executed:**
  ```bash
  # Step 1: Connect via SSH using the provided port
  ssh bandit1@bandit.labs.overthewire.org -p 2220

  # Step 2: Read the file using the relative path prefix
  cat ./-
  ```
* **Methodology:** The regular `cat -` command failed because Linux interpreted the hyphen as an argument flag. By specifying the explicit path `./-`, I forced the shell to look at the actual file.

*(Note to recruiters: To preserve the integrity of the challenge, actual passwords have been omitted from this documentation.)*

---

## 🧠 Key Takeaways
* **The Power of Piping:** Learning to combine commands taught me how to sift through data quickly.
* **RTFM (Read The Fine Manual):** Utilizing `man` pages helps solve complex navigation obstacles efficiently.
