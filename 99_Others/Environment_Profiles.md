# System Wide Environment profiles


### How to print the env
```shell 

printenv 
#or 
env
```

### How to set environment variable?

```shell
# sets environment vairable to only current session, Once you close the terminal, it disappears.
MY_VAR=TRUE
```
#### How to add env variable permanent to the current user?

- To make the variable available system-wide or across sessions, you need to add `export MY_VAR=TRUE` to `~/.bashrc` and then run `source ~/.bashrc`

- So, if you only did `MY_VAR=TRUE`, it was temporary and limited to your current shell.

#### what is the diff b/w MY_VAR=TRUE vs export MY_VAR=TRUE

The difference is:

- MYVAR=TRUE sets the variable only for the current shell session.
- export MYVAR=TRUE exports the variable, making it available to child processes and future sessions.In short, export makes the variable global to the environment, so any programs or scripts started from that shell can access MYVAR.


#### what is source ~/.bashrc doing?

- `source ~/.bashrc` reads and executes the commands inside the `.bashrc` file in the current shell session.
- This means any environment variables or configurations you added to `.bashrc` (like `export MYVAR=TRUE`) will take effect immediately without needing to log out and back in. Think of it as "refreshing" your current shell with the new settings!



## How to set the globally available environment variables in a Linux-based system?

### What is the difference b/w .bashrc vs /etc/environment
.bashrc: User-specific, located in your home directory. It runs every time you start a new Bash shell (interactive). Perfect for customizing your shell environment, aliases, functions, etc.

/etc/environment: System-wide, applies to all users. It sets environment variables globally, but it's static and doesn't support shell-specific syntax or commands.

In short: .bashrc is for user-specific, shell-related configs; /etc/environment is for system-wide, static environment variables. 😊



### How to set a global variable?

Edit the `/etc/environment` file:

```shell
sudo vi /etc/environment

#Save the below line at the end of the file:

GLOBALOPTION=ON
```

- Then execute the following command on the terminal for the changes to take effect:

```shell 
source /etc/environment
```


### How to add a command which executes for all users login?

adding the command to execute for all users upon login. To do this, you should create a script with your message and place it in /etc/profile.d/. Here's what to do:

echo 'echo Welcome to our server!' > /etc/profile.d/welcome.sh
chmod +x /etc/profile.d/welcome.sh

This ensures the message runs for any user logging in. Keep in mind, the script must have execute permissions! 🚀



### what is /etc/profile.d/ and what is the use of it?

/etc/profile.d/ is a directory where system administrators can place scripts that are automatically executed for all users when they log in via a shell. It's a way to set environment variables, run commands, or customize the login environment system-wide.

Use:

To add scripts that run at login for every user.
To organize startup scripts neatly, instead of editing /etc/profile directly.
To ensure consistent environment setup across all user sessions.
Think of it as a "startup script folder" for login shells!

