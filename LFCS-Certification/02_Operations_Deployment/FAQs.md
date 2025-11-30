# FAQs


<details>
<summary>Does every command like <code>ls</code>, <code>cd</code>, <code>mkdir</code> etc. create a process in Linux?</summary>

<strong>Answer (short):</strong> No — not every command spawns a new process. It depends on whether the command is an external program, a shell builtin, a shell function/alias, or runs in a subshell or pipeline.

<strong>Explanation & examples:</strong>

- <strong>External commands</strong> (usually in `/bin`, `/usr/bin`, etc.) create a new process. Examples: `ls`, `mkdir`, `grep`, `python3`.
	- The shell forks and the child executes the program (`execve`). You can confirm with `type -a ls` or `command -v ls`.

- <strong>Shell builtins</strong> run inside the shell process and do not create a new process. Examples: `cd`, `exit`, `export`, `alias`, `read`.
	- Builtins are necessary when a command must change shell state (for example, `cd` must run in the current shell to change its working directory).

- <strong>Shell functions and aliases</strong> run in the shell. They only create child processes if they invoke external programs.

- <strong>Subshells</strong> (created with parentheses `( ... )`) and pipelines generally create new processes for each element. Example: `(cd /tmp; pwd)` runs in a subshell so the `cd` does not affect the parent shell.

- <strong>`exec` builtin</strong> replaces the current shell process with the program — it does not create a new process (the shell PID becomes the program's PID).

- <strong>Scripts</strong>:
	- Running `./script.sh` or `bash script.sh` starts a new interpreter process. 
	- Using `source script.sh` or `. script.sh` executes the script in the current shell (no new process).

<strong>Useful commands to check behavior:</strong>

```bash
# show whether a command is builtin or external
type -a cd       # usually shows 'cd is a shell builtin'
type -a ls       # shows path to /bin/ls (external)

# observe shell PID and a child process
echo "shell PID=$$"
sleep 20 &       # external command — creates a child
ps -o pid,ppid,cmd --forest | head -n 20
```

<strong>Demo you can run:</strong>

```bash
# 1) see your shell PID
echo "shell PID=$$"

# 2) builtin (no new PID):
type -a cd
cd /tmp
echo "still shell PID=$$"

# 3) external command (creates a child process):
type -a sleep
sleep 20 &
echo "started sleep in background; check with ps"
ps -o pid,ppid,cmd --forest | grep sleep -B2 -n || true
```

<strong>Why it matters:</strong>

- Builtins are faster (no fork/exec) and required to change the shell state.
- Knowing which commands spawn processes helps when writing scripts (avoid unnecessary forks), debugging, and understanding performance.

</details>

---



<details>
<summary>How to send currently editing vim session to background job?</summary>

- To send a running Vim editing session to the background as a job in a Unix-like environment (e.g., Linux, macOS), follow these steps:
- __Suspend Vim:__ While in Vim, press ``Ctrl+Z``. This suspends the Vim process and sends it to the background, returning you to your shell prompt.

- __Verify Background Job:__ At your shell prompt, you can type ``jobs`` to list the currently running background jobs. You should see Vim listed among them.

- __Bring Vim to Foreground (Optional):__ If you need to resume editing in Vim, type ``fg`` at your shell prompt. This brings the last background job (Vim in this case) back to the foreground. If you have multiple background jobs and want to resume a specific one, use ``fg %<job_number>``, where ``<job_number>`` is the number associated with Vim in the jobs list.


</details>

---

<details>
<summary> How to run a job directly in background? </summary>

To make a job run in the background in Linux instead of bringing it to the foreground, you can use the following methods: 

1. __Starting a command directly in the background:__ 
Append an ampersand (&) to the end of the command when you execute it. This tells the shell to run the command in the background immediately and return control of the terminal to you. 

````bash
# your_command &
sleep 400 &

````
2. __Sending a running foreground job to the background:__
    
If a command is already running in the foreground, you can send it to the background by: 

- __Suspending the job:__ Press ``Ctrl + Z``. This will pause the job and return you to the shell prompt. 

- __Resuming the job in the background:__ Type ``bg`` and press Enter. This will continue the last suspended job in the background. To run specific job ``bg <job_number>``


3. __Using nohup for processes that should continue after terminal closure:__ 
If you want a process to continue running even after you close your terminal session (e.g., when connecting via SSH), use the nohup command in conjunction with the ampersand: 
````bash
nohup your_command &
````
This command redirects the output to a file named ``nohup.out`` by default, preventing the process from terminating when the controlling terminal closes. 

#### Important Notes: 

- To see a list of jobs running in the background, use the ``jobs`` command. 
- To bring a backgrounded job back to the foreground, use the ``fg`` command. You can specify a job ID (e.g., ``fg %1``) or simply type ``fg`` to bring the most recent background job to the foreground. 
- For long-running tasks or managing multiple detached sessions, consider using tools like ``screen`` or ``tmux``. These tools allow you to create persistent terminal sessions that you can detach from and reattach to later, even from different locations or after closing your initial connection.

#### Example:

- execute ``sleep 500`` and move to background using ``ctrl+z``
- check on ``jobs`` it will be on stopped state
- If you want to run the job without bringing them to foreground use ``bg <job_number>`` now check ``jobs`` you can see Running state
- To bring foreground ``fg <job_number>``


</details>

---

