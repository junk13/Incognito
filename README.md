# Incognito
A simple *yet classy* rootkit for simple *yet classy* folks. 

**Authors:** 
Kordell Stewart, Suvaion Das, Alex Hassenbein  

This basic rootkit works on the Linux operating system and is a loadable kernel module which when loaded into the kernel will do the following:

  - Grant root privileges
  - Hide process by PID
  - Unhide a previously hidden process by PID
  - Hide files or directories with the special prefix in their name
  - Hide itself
  - Unhide itself
  - Protect against being unloaded by the user
  - Disable the unload protection
  
 # Requirements 
 Created and tested on Distro: Ubuntu 18.04.1 LTS Kernel: 4.15.0-39-generic.
 *Will not function on Kernel versions 2.x or 3.x!* 
 
 # Installation 
 Check that you are running a compatible 4.x kernel. 
 ```
 uname -r
 ```
 Download the repository. 
 
 Navigate to the Incognito directory
 ```
 cd where you put it/Incognito
 ```
 Run the makefile. 
 ```
 make
 ```
 At this point, the module is ready for insertion. 
 ```
 sudo insmod incognito.ko
 ```
 # Using Incognito
Incognito has two major interactions on its own. First it hijacks the kill command to give user input. Second, when it is inserted, all files with our super secret target prefix `incognito_secret` will be hidden from any attempts to find them (such as `ls` or trying to navigate there). The files are still usable, just hidden. Some sample files and a directory for hiding are provided in the repository; these files should be automatically hidden when incognito is inserted. The only way to reveal hidden files is to unload the module itself. 
 
 After Incognito is initialized, it is immediately hidden and protected. It won't show up if you attempt: 
 ```
 lsmod
 ```
In addition, Incognito will print a message letting you know it is loaded and if it found the system call table (and where). A warning message is also printed, reminding you to remove and unhide incognito. Use `dmesg` to view these messages. 

Incognito can be controlled using the terminal via the userspace command program or directly with the hijacked kill command. 

## Using the Command Handler 
In order to use the Command Handler, navigate to the Incognito direcotry. Once there we can print a help message with 
```
./command help
```
This will print off a help message listing the available commands. The command handler currently accepts the following commands: 
```
  protect           Toggles rootkit removal protection.
  help              Shows this help menu in the terminal.
  hide [PID]        Toggles hiding the specified [PID].
  incognito         Toggles hiding and protection of the rootkit.
  root              Grants root privelege.
  test              Loops the handler program to make an easy pid hiding target. 
	
```
To enter a command, type `./command [type chosen command here]`. 
In order to actually reveal Incognito, the following command must be used:
 ```
 ./command incognito
 ```
 Now the module can be revealed and will show up on the module list when `lsmod` is used.  
 Attempting to `rmmod` Incognito at this point will result in a `ERROR: Module incognito is in use`. 
 In order to remove, the removal protection must be turned off. 
 ```
 ./command protect
 ```
 At this point the module can be unloaded. 
 Another capability is granting root privileges (it is after all, a *rootkit*). 
 ```
 ./command root
 ```
 No more need for pesky `sudo`, you have root! 
 **Note: The command handler root is unstable and usually roots `sh` which does nothing. I recommend using the raw command for root to get 100% consistency.** 
 
 We can also hide proccesses via their pid. We can do this by calling `ps` and picking a victim. If our victim's pid is `1234` we would  hide it with the command: 
 ```
 ./command hide 1234
 ```
 Using `ps`, this process shouldn't show up anymore! Entering the above commadn again will unhide the process. The command handler has a test mode as well, that when executed makes it loop. Using `./command test` and then Ctrl+Z with `bg` we can throw it to the back and make it an easy process hiding target.
 
 At this point you can remove Incognito (provided it's revealed and protected!) with the command: 
 ```
 rmmod incognito.ko
 ```
## Raw Kill Commands
In order to issue raw commands to Incognito, the kill command is used. Incognito currently accepts the following commands: 
 ```
        kill -64 0              Grants root privelege.
        kill -31 [pid]          Toggles hiding the specified [pid].
        kill -63 0              Toggles hiding of the rootkit.
        kill -2 0               Print this help message to dmesg.
        kill -1 0               Toggles rootkit removal protection.
 ```
 If you need to see these while it is installed, use `kill -1 0` and then `dmesg` to view the help menu! 
 
 In order to actually reveal Incognito, the following command must be used:
 ```
 kill -63 0
 ```
 Now the module can be revealed and will show up on the module list when `lsmod` is used.  
 Attempting to `rmmod` Incognito at this point will result in a `ERROR: Module incognito is in use`. 
 In order to remove, the removal protection must be turned off. 
 ```
 kill -1 0
 ```
 At this point the module can be unloaded. 
 Another capability is granting root privileges (it is after all, a *rootkit*). 
 ```
 kill -64 0
 ```
 No more need for pesky `sudo`, you have root! 
 
 We can also hide proccesses via their pid. We can do this by calling `ps` and picking a victim. If our victim's pid is `1234` we would hide it with the command: 
 ```
 kill -31 1234
 ```
 Using `ps`, this process shouldn't show up anymore! Entering the above commadn again will unhide the process. 
 
 At this point you can remove Incognito (provided it's revealed and protected!) with the command: 
 ```
 rmmod incognito.ko
 ```
 
  
 # Resources
 Here's some helpful guides, manuals, and links we used on this project 
- https://www.idontnix.net/tech/anaroot.html
- https://uwnthesis.wordpress.com/2016/12/26/basics-of-making-a-rootkit-from-syscall-to-hook/
- https://www.kernel.org/doc/htmldocs/kernel-hacking/routines-module-use-counters.html
- http://www.ouah.org/LKM_HACKING.html
- https://stackoverflow.com/questions/2103315/linux-kernel-system-call-hooking-example
- http://books.gigatux.nl/mirror/networksecuritytools/0596007949/networkst-CHP-7-SECT-2.html
- https://elixir.bootlin.com/linux/latest/source

# Disclaimer 
This project was made purely with academic interests in mind. We are releasing this (relatively tame and neutered) malicious code with no intent to cause any harm or damages. There are plenty of articles and guides (see above) on how to make similar rootkits. 

**Please use responsibly!**

# Known Issues
The Command Handler `root` command is currently unstable and grants root priveleges to `sh` rather than `bash`. Use of the raw command should be used to reliably grant root access.
