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
  ```bashls
  
  # Step 1: Connect to Level 6 from the local machine
  ssh bandit6@bandit.labs.overthewire.org -p 2220

  # Step 2: Search the entire server from root (/) while hiding system errors
  find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null

  # Step 3: Read the specific configuration path returned by the query
  cat /var/lib/dpkg/info/bandit7.password
  ```
* **Methodology:** Because the file was located outside the user home directory, I initialized the `find` command from the root partition (`/`). I chained the `-user bandit7`, `-group bandit6`, and `-size 33c` (bytes) parameters to identify the file criteria. To prevent the console from being overwhelmed by unauthorized directory logs, I appended `2>/dev/null` to discard standard error streams, returning a single path output containing the target credential.

### 🔍 Level 7 ➔ Level 8

* **Objective:** Retrieve the 33-byte password stored inside `data.txt` located directly next to the keyword `millionth`.
* **Concept Learned:** Pattern matching, plain-text string filtering, and word anchoring using the stream processor.
* **Command Executed:**
```bash
# Step 1: Connect to Level 7 from the local machine
ssh bandit7@bandit.labs.overthewire.org -p 2220

# Step 2: Inspect the current directory contents to confirm data.txt exists
ls -la

# Step 3: Scan the data file and extract the line containing the target keyword
grep "millionth" data.txt
```
* **Methodology:** Because the file contains a massive volume of text data, reading it manually with page readers would be inefficient. I utilized the `grep` utility to scan the contents of `data.txt` dynamically. By supplying the literal query string `"millionth"`, the terminal isolated the exact row containing the phrase, automatically displaying the adjacent alphanumeric password hash on the right side of the output line.

### 🔍 Level 8 ➔ Level 9

* **Objective:** Locate the unique line of text within `data.txt` that appears exactly once amidst thousands of duplicated lines.
* **Concept Learned:** Output piping (`|`), lexicographical sorting, and filtering duplicate adjacent lines.
* **Command Executed:**
```bash
# Step 1: Connect to Level 8 from the local machine
ssh bandit8@bandit.labs.overthewire.org -p 2220

# Step 2: Sort the file contents and filter for the unique single occurrence
sort data.txt | uniq -u
```
* **Methodology:** The `uniq` utility can only detect duplicate lines if they are immediately adjacent to one another. To bypass this limitation, I first used `sort` to reorganize `data.txt`, grouping all identical text blocks together sequentially. I then piped (`|`) that organized output directly into `uniq -u`. The `-u` flag instructs the system to discard all repeated entries entirely and print only the solitary line that occurs exactly once, revealing the Level 9 password.

### 🔍 Level 9 ➔ Level 10

* **Objective:** Extract the human-readable password from the binary file `data.txt` which is prefixed by multiple equality (`=`) symbols.
* **Concept Learned:** Binary data parsing, printable text extraction, and character pattern anchoring using regular expressions.
* **Command Executed:**
```bash
# Step 1: Connect to Level 9 from the local machine
ssh bandit9@bandit.labs.overthewire.org -p 2220

# Step 2: Extract printable characters and filter for lines starting with '='
strings data.txt | grep "^=="
```
* **Methodology:** The `data.txt` file is saved in a non-text binary format, making standard text editors or `cat` commands output unreadable garbage data. I used the `strings` command to filter the file and isolate only the sequences of human-readable, printable characters. I then piped (`|`) those readable lines into `grep "^=="`. The caret (`^`) regular expression anchors the search to the start of each line, isolating the row containing the password directly next to the identifying equals signs.

### 🔍 Level 10 ➔ Level 11

* **Objective:** Decode the Base64 encoded payload stored inside `data.txt` to reveal the plain-text password.
* **Concept Learned:** Data encoding vs. encryption, binary-to-text decoding primitives, and standard stream piping.
* **Command Executed:**
```bash
# Step 1: Connect to Level 10 from the local machine
ssh bandit10@bandit.labs.overthewire.org -p 2220

# Step 2: Decode the Base64 contents of the data file
base64 -d data.txt
```
* **Methodology:** The data inside `data.txt` is encoded in Base64 format, which represents binary information using a set of 64 printable ASCII characters. Because encoding is a reversible translation rather than secure encryption, I used the native `base64` tool with the `-d` (decode) flag. The system automatically parsed the structured character blocks back into raw ASCII text, instantly printing the plain-text Level 11 password to the console.

### 🔍 Level 11 ➔ Level 12

* **Objective:** Decrypt the password inside `data.txt` by reversing a ROT13 cipher that has shifted all alphabetic characters by 13 positions.
* **Concept Learned:** Substitution ciphers, character set rotation, and stream translation using standard input maps.
* **Command Executed:**
```bash
# Step 1: Connect to Level 11 from the local machine
ssh bandit11@bandit.labs.overthewire.org -p 2220

# Step 2: Rotate the uppercase and lowercase character sets by 13 positions
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
```
* **Methodology:** The file is obfuscated using ROT13, a Caesar cipher variant where every letter is swapped with the one 13 spaces ahead of it in the alphabet. Because the alphabet contains 26 letters, running the rotation a second time completely reverses the obfuscation. I used the `tr` (translate) utility, defining the input set as `A-Za-z` and mapping it to a shifted output set starting halfway through the alphabet (`N-ZA-Mn-za-m`). By feeding `data.txt` into this filter, the scrambled text shifted back into readable alignment, displaying the Level 12 password.




*(Note to recruiters: To preserve the integrity of the challenge, actual passwords have been omitted from this documentation.)*

---

## 🧠 Key Takeaways
* **The Power of Piping:** Learning to combine commands taught me how to sift through data quickly.
* **RTFM (Read The Fine Manual):** Utilizing `man` pages helps solve complex navigation obstacles efficiently.
