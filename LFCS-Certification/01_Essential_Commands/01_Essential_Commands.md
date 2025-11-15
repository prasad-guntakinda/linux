# LFCS Essential Commands


## 01. Read and Use System Documentation

### man

- All important commands in Linux have their own manuals or "man pages". 
- To access a command's manual enter `man <name_of_command>`

```shell
man ls
man mkdir
``` 
- General syntax of command in `SYNOPSIS`
- Detailed description of command, how it works, and so on, in `DESCRIPTION`.
- Detailed descriptions of command line options in `OPTIONS`.
- And some manual pages even have some `EXAMPLES` near the end of the manual.
- `printf` is a command. But `printf` is also a function that can be used by programmers.
- If two man commands has same name then use category number during the command

![printf_man_category](./images/printf_man_category.png)


### help
- `--help` will usually show a condensed form of help, with very short explanations.
- Examples:
```shell
ls --help
mkdir --help
```

### apropos

- apropos is a command that lets you search through man pages. 
- It looks at the short descriptions of each man page and tries to see if it matches the text we entered.

- Imagine you forgot the name of the command that lets you create a new directory. How would you search for it?

- search for all man pages that have the word "director" in their short descriptions. 
- To run apropos for the first time run `sudo mandb` 

![apropos_search](./images/apropos_search.png)

- Commands will be found in sections `1` and `8`. 
- We can tell apropos to only filter out results that lead to commands from these categories. 

```shell
apropos -s 1,8 director
```

### Auto-Complete:  <command_name> TAB/2TABs
- To auto-fill file/directory names use TAB if there is a conflict give 2TABs to list all the available options


---
---

## 02.Working with Files and Directories

### 2.1 Basic Commands

| Command | Description                            | Common Options/Examples                                                   |
| ------- | -------------------------------------- | ------------------------------------------------------------------------- |
| `ls`    | Lists the contents of a directory.     | `-l` (long format), `-a` (show hidden files), `-h` (human-readable sizes) |
| `cd`    | Changes the current directory.         | `cd /home/user`                                                           |
| `pwd`   | Prints the current working directory.  |                                                                           |
| `mkdir` | Creates a new directory.               | `-p` (create parent directories as needed)                                |
| `rm`    | Removes files or directories.          | `-r` (recursive), `-f` (force removal)                                    |
| `cp`    | Copies files or directories.           | `-r` (recursive), `-i` (prompt before overwrite)                          |
| `mv`    | Moves or renames files or directories. | `-i` (prompt before overwrite)                                            |

---

### File Permissions

### Hard Links & Soft Links

- Inodes
- stat
- Hard Links

### Soft Links

- soft link is nothing but a software shortcut on the Windows Desktop. if you click on Chrome shortcut it will open `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe` file
- Desktop shortcut is a soft link to the `.exe` file

![soft_links_1](./images/soft_links_1.png)

- Soft links in Linux are very similar to hard link.
- A hard link pointed to an `inode`. 
- A soft link is nothing more than a file that points to a path instead.
- It's almost like a text file, with a path to a file or directory inside.
- syntax:

```shell
ln -s path_to_target path_to_link_file
```

![soft_links_2](./images/soft_links_2.png)

- Since soft links are nothing more than paths pointing to a file, you can also softlink to directories:
```shell 
ln -s Pictures/ shortcut_to_directory
```
- you can softlink to files/directories on a different filesystem.

#### How to read soft link?
- use `ls -l` long listing format to see the file type. if permissions section starts with `l` means it's a soft link
- It even displays the path that the soft link points to. If this path is long, `ls -l` might not show the entire path. 
- An alternative command to see the path stored in a soft link is:
```shell
readlink path_to_soft_link
```

![soft_links_3](./images/soft_links_3.png)


- You may also notice that all permission bits, rwx (read, write, execute) seem to be enabled for this file. That's because the permissions of the soft link do not matter, the permissions of the destination file applies.
- Broken links are displayed in RED color

</details>

---


<details>
<summary></summary>
</details>




## FAQs

<details>
<summary>find vs grep</summary>

### find vs grep:
---

# 🔍 1. `grep`

`grep` searches **inside file contents**.

Use `grep` when you want to find **text, patterns, or strings** within files.

### ✔ Examples

### 1. Search for a word inside files
```bash
grep "error" /var/log/syslog
````

Searches for the word `"error"` inside `/var/log/syslog`.

---

### **2. Search recursively in directories**

```bash
grep -r "timeout" /var/log/
```

Searches for `"timeout"` in all files under `/var/log`.

---

### 3. Show line numbers

```bash
grep -n "Failed" auth.log
```

---

### 4. Case-insensitive search

```bash
grep -i "warning" messages
```

---

### When to Use `grep`

* To search **text in files**
* To match **patterns** using regex
* To find **lines** containing certain words

---

# 📁 2. `find`

`find` searches for **files and directories** based on conditions such as name, type, size, permissions, date, etc.

### ✔ Examples

### **1. Find a file by name**

```bash
find /home -name "data.txt"
```

---

### **2. Find all directories**

```bash
find /etc -type d
```

---

### **3. Find files modified in the last 1 day**

```bash
find /var/log -mtime -1
```

---

### **4. Find files with specific permissions**

```bash
find /var/log -perm 644
```

---

### **5. Find files and execute a command**

```bash
find /tmp -name "*.log" -exec rm {} \;
```

---

### **When to Use `find`**

* To search **for files or directories**
* To filter using **permissions, size, type, owner**
* To **execute actions** on matched files (delete, copy, chmod, etc.)

---

# 🆚 Key Differences

| Feature                               | `grep`      | `find`                |
| ------------------------------------- | ----------- | --------------------- |
| Searches **inside files**             | ✔ Yes       | ✖ No                  |
| Searches **file names / directories** | ✖ No        | ✔ Yes                 |
| Supports **pattern matching**         | ✔ (regex)   | ✔ (file names)        |
| Can act on files found                | With pipes  | With `-exec`          |
| Primary use                           | Text search | File/directory search |

---

# 🧠 Summary

* Use **`grep`** when you want to search **text within files**.
* Use **`find`** when you want to search **for files or directories** on the filesystem.

These two commands are often used together for powerful searches.

Example:

```bash
find /var/log -type f -exec grep -i "failed" {} \;
```

This finds all files in `/var/log` and searches for `"failed"` inside each.

---


</details>