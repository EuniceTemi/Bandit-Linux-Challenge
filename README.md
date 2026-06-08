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


### 🔒 Level 2 ➔ Level 3
* **Objective:** Log into the game server using SSH and locate the password hidden in a file named `--spaces in the filename` in the home directory.
* **Concept Learned:** Handling special characters and spaces in filenames.
* **Command Executed:**
  ```bash
  # Step 1: Connect via SSH using the provided port
  ssh bandit1@bandit.labs.overthewire.org -p 2220

  # Step 2: Read the file using the relative path prefix
  cat ./--spaces\ in\ this\ filename--
  ```
* **Methodology:** The regular `cat spaces in this filename` command failed because Linux interpreted the hyphen as an argument flag and the presence of spaces in the filename. By specifying the explicit path `./-`, I forced the shell to look at the actual file. And by using the escape charater `\`, it forces Linux to treat the space as a regular character in a filename.

### 🔍 Level 3 ➔ Level 4
* **Objective:** Retrieve the password stored in a hidden file located within the `inhere` directory.
* **Concept Learned:** Navigating directory trees and uncovering hidden files (dotfiles) in Linux.
* **Command Executed:**
  ```bash
  # Step 1: Connect to Level 3 from the local machine
  ssh bandit3@bandit.labs.overthewire.org -p 2220

  # Step 2: Navigate into the target directory
  cd inhere

  # Step 3: List all files, including hidden ones
  ls -la

  # Step 4: Read the hidden password file uncovered by the list command
  cat .hidden
  ```
* **Methodology:** A standard `ls` command returned no visible files because any file in Linux starting with a dot (`.`) is hidden by default. By utilizing the `ls -la` command, the `-a` (all) flag forced the terminal to reveal the hidden `.hidden` file, allowing me to view its contents and capture the password.

### 📄 Level 4 ➔ Level 5
* **Objective:** Identify and read the single human-readable file out of multiple hidden files starting with hyphens inside the `inhere` directory.
* **Concept Learned:** Utilizing data stream identification tools and file-type analysis techniques.
* **Command Executed:**
  ```bash
  # Step 1: Connect to Level 4 from the local machine
  ssh bandit4@bandit.labs.overthewire.org -p 2220

  # Step 2: Navigate into the source directory
  cd inhere

  # Step 3: Analyze the file types of all contents simultaneously using a wildcard
  file ./*
  
  # Step 4: Open the only file flagged as human-readable ASCII text
  cat ./-file07
  ```
* **Methodology:** The directory contained 10 distinct files (`-file00` through `-file09`). To avoid manually printing binary noise to the console, I executed `file ./*`. Prepending the `./` path shortcut was necessary to keep the shell from interpreting the filename hyphens as command flags. The output explicitly flagged `./-file07` as `ASCII text`, revealing the correct destination path to query for the password.


### 🔍 Level 5 ➔ Level 6
* **Objective:** Find a specific file hidden somewhere inside the `inhere` directory structure matching three exact properties: human-readable, exactly 1033 bytes in size, and non-executable.
* **Concept Learned:** Using the `find` command with advanced attribute filtering flags like size and execution permissions.
* **Command Executed:**
  ```bash
  # Step 1: Connect to Level 5 from the local machine
  ssh bandit5@bandit.labs.overthewire.org -p 2220

  # Step 2: Search recursively from the 'inhere' directory for a 1033-byte, non-executable file
  find inhere/ -type f -size 1033c ! -executable

  # Step 3: Read the single file path returned by the search filter
  cat inhere/maybehere07/.file2
  ```
* **Methodology:** The `inhere` directory contained dozens of nested subfolders, making a manual search impossible. I leveraged the `find` command using `-type f` to look only for files, `-size 1033c` to match the exact byte count (`c` stands for bytes), and `! -executable` to invert the permission filter and skip executable scripts. This instantly isolated the exact target path containing the cleartext password.

### 🔍 Level 6 ➔ Level 7
* **Objective:** Locate a 33-byte password file hidden somewhere on the server owned by user `bandit7` and group `bandit6`.
* **Concept Learned:** Global filesystem indexing, system ownership filters, and standard error (`stderr`) redirection.
* **Command Executed:**
  ```bash
  # Step 1: Connect to Level 6 from the local machine
  ssh bandit6@bandit.labs.overthewire.org -p 2220

  # Step 2: Search the entire server from root (/) while hiding system errors
  find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null

  # Step 3: Read the specific configuration path returned by the query
  cat /var/lib/dpkg/info/bandit7.password
  ```
* **Methodology:** Because the file was located outside the user home directory, I initialized the `find` command from the root partition (`/`). I chained the `-user bandit7`, `-group bandit6`, and `-size 33c` (bytes) parameters to identify the file criteria. To prevent the console from being overwhelmed by unauthorized directory logs, I appended `2>/dev/null` to discard standard error streams, returning a single path output containing the target credential.

*(Note to recruiters: To preserve the integrity of the challenge, actual passwords have been omitted from this documentation.)*

---

## 🧠 Key Takeaways
* **The Power of Piping:** Learning to combine commands taught me how to sift through data quickly.
* **RTFM (Read The Fine Manual):** Utilizing `man` pages helps solve complex navigation obstacles efficiently.
