# WSL Setup on Windows

<details>
<summary>How to install Linux OS on Windows using WSL?</summary>

# TBD

</details>

---
---


<details>
<summary>How to Install & Uninstall other distros on existing WSL?</summary>

- Listdown all the available distros on WSL

````shell

    # open terminal
    wsl -l -o  # -l=list -o=online
````

### Install:

![wsl_list_all_distros](./images/wsl_list_all_distros_online.png)

- Install Debian Distro

![alt text](./images/debian_install.png)

- Now check what all the distros installed on your WSL using `wsl -l`

![alt text](./images/wsl_list_all_distros_localhost.png)

- from GUI: if you go to file explorer you can see Linux displayed next to ``This PC`` if you click on it you can see what all the Linux distros you have installed on WSL

![alt text](./images/wsl_list_all_distros_localhost_gui.png)

- from start Menu: if you click on start menu and search for Debian it will be visible

![alt text](./images/debian_from_start_menu.png)

- How to open Debian?
  
- **GUI:** Go to Start Menu search for Debian and click on open

![alt text](./images/launch_debian_from_start_menu.png)



- **Terminal:** Go to Terminal use command `wsl -d debian`  
  
![launch_debian_from_terminal](./images/launch_debian_from_terminal.png)

- to logout type exit

---

### Uninstall:

- Open Terminal and run `wsl --list --verbose` to find the exact distribution name. 
- Then, use ``wsl --unregister <DistributionName>`` to remove the Debian instance

![uninstall_debian_1](./images/uninstall_debian_1.png)

- If you go to File Explorer Debian will be removed
  
![uninstall_debian_2](./images/uninstall_debian_2.png)


- Windows-10 Users Finally, from GUI go to ``Settings > Apps > Apps & features``, find "Debian" in the list, and click Uninstall to remove the application.


</details>