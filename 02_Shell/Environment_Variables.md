# Learning Notes: Environment Variables

Environment variables are dynamic values that affect the behavior of processes and applications in a Linux system. They are used to store configuration settings and system-wide information.

## What are Environment Variables?
Environment variables are key-value pairs that are set in the shell and inherited by child processes. They provide a way to configure the operating system and applications without hardcoding values.

### Examples of Common Environment Variables
| Variable | Description                                      |
| -------- | ------------------------------------------------ |
| `PATH`   | Specifies directories to search for executables. |
| `HOME`   | The current user's home directory.               |
| `USER`   | The name of the current user.                    |
| `SHELL`  | The default shell for the user.                  |
| `PWD`    | The current working directory.                   |

## The `$PATH` Variable
The `$PATH` variable is one of the most important environment variables. It defines the directories the shell searches for executable files when you run a command.

### How `$PATH` Works
- When you type a command (e.g., `ls`), the shell searches for the executable in the directories listed in `$PATH`.
- Directories in `$PATH` are separated by colons (`:`).
- Example value of `$PATH`:
  ```
  /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
  ```

### Why is `$PATH` Needed?
- Without `$PATH`, you would need to specify the full path to every command (e.g., `/usr/bin/ls` instead of `ls`).
- It simplifies command execution by allowing the shell to locate executables automatically.

### Viewing and Modifying `$PATH`
- **View `$PATH`:**
  ```bash
  echo $PATH
  ```
- **Add a Directory to `$PATH`:**
  ```bash
  export PATH=$PATH:/new/directory
  ```
- **Make Changes Permanent:** Add the `export` command to your shell configuration file (e.g., `~/.bashrc` or `~/.zshrc`).

## Making Changes Permanent

When you modify environment variables, the changes are temporary by default and last only for the current session. To make these changes permanent, you need to add them to your shell's configuration file.

### Steps to Make Changes Permanent
1. **Identify Your Shell Configuration File:**
   - For `bash`, use `~/.bashrc` (or `~/.bash_profile` on some systems).
   - For `zsh`, use `~/.zshrc`.
   - For `fish`, use `~/.config/fish/config.fish`.

2. **Edit the Configuration File:**
   - Open the file in a text editor (e.g., `nano`):
     ```bash
     nano ~/.bashrc
     ```

3. **Add the Export Command:**
   - Append the following line to the file:
     ```bash
     export PATH=$PATH:/new/directory
     ```

4. **Apply the Changes:**
   - Reload the configuration file to apply the changes:
     ```bash
     source ~/.bashrc
     ```

### Example: Adding a Directory to `$PATH`
Suppose you have a directory `/home/user/scripts` containing custom scripts, and you want to add it to your `$PATH` permanently:
1. Open your shell configuration file:
   ```bash
   nano ~/.bashrc
   ```
2. Add the following line:
   ```bash
   export PATH=$PATH:/home/user/scripts
   ```
3. Save the file and exit the editor.
4. Reload the configuration file:
   ```bash
   source ~/.bashrc
   ```
5. Verify the change:
   ```bash
   echo $PATH
   ```
   You should see `/home/user/scripts` included in the output.

## Setting Environment Variables Permanently

To set any environment variable permanently, follow a similar process as above.

### Example: Setting a Custom Variable
Suppose you want to set a custom variable `MY_VAR` with the value `HelloWorld`:
1. Open your shell configuration file:
   ```bash
   nano ~/.bashrc
   ```
2. Add the following line:
   ```bash
   export MY_VAR=HelloWorld
   ```
3. Save the file and exit the editor.
4. Reload the configuration file:
   ```bash
   source ~/.bashrc
   ```
5. Verify the variable:
   ```bash
   echo $MY_VAR
   ```
   The output should be:
   ```
   HelloWorld
   ```

By following these steps, you can ensure that your environment variable changes persist across sessions.


### Setting Environment Variables
- **Temporary (for current session):**
  ```bash
  export VAR_NAME=value
  ```
- **Permanent (across sessions):** Add the `export` command to your shell configuration file.

### Viewing Environment Variables
- **List all environment variables:**
  ```bash
  printenv
  ```
- **View a specific variable:**
  ```bash
  echo $VAR_NAME
  ```

### Unsetting Environment Variables
- **Remove a variable:**
  ```bash
  unset VAR_NAME
  ```

## Best Practices
- Avoid overwriting critical variables like `$PATH` without appending the existing value.
- Use descriptive names for custom variables to avoid conflicts.
- Test changes to environment variables in a temporary session before making them permanent.


## Summary
Environment variables are essential for configuring the Linux environment and ensuring smooth operation of commands and applications. Understanding and managing variables like `$PATH` is crucial for efficient system usage.


## How to execute commands outside of $PATH.
-  /full/path/to/command
- ./command-in-this-dir