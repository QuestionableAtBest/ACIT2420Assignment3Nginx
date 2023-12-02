# ACIT2420 Assignment 3 Nginx

***Hello fellow Linuxers, I come bearing fruits of knowledge which you will consume to ascend to a new level of programming.***
***Let their information seeds embed into your brain so that you will never forget.***

## Changing from root user to a regular user
First question you may be asking is why would I ever want that?
In Linux, the root user is all powerful, they have unrestricted access to all files and permissions in the system,
allow you to do *anything* and ***everything***.

The concept sounded pretty nice until I remembered a certain quote from someone's grandpa

![alt text][responsibility]

[responsibility]: https://media.tenor.com/fxkppD6xu7UAAAAC/spiderman-uncle-ben.gif "Spiderman Responsibility"

Unrestricted access can lead to unintended consequences, especially when you don't know what you're doing <- **(That's you!)**

![alt text][rm]

[rm]: https://media.tenor.com/eQms3ABR1zEAAAAC/sudo-rm-rf-linux.gif "DO NOT DO THIS" 

Unrestricted access paired with unauthorized access, such as a hacker, could lead to even more disasterous consequences.
Thus, it is better to utilize the Least Privilege Princible by creating accounts with enough permissions to allow them to do what they need to do.
This ensures that every account is *accounted* for and when certain users become compromised, they are unable to pose critical risks to the system. 

***Before you start***  
Remember that Linux provides manual pages for all commands supported, if the explanation here doesn't feel clear or you are unsure what an option for the command does, don't hesistate to use man [COMMAND] and then forward slash and enter your search terms to quickly find help!

### Step 1 Adding a user:  
The first step is to add a new user to our system using the following command:  

```sudo useradd [NAME]```  

Command should be self-explanatory, will add this user to our users
Note: We are using useradd instead of adduser as it is a lower-level command and therefore more accessible on different distros.  
There should be no response after the command  

Now give your new user a password for them to log in using the following command:  
```sudo passwd [NAME]```  

If completed successfully you should see  

```passwd: password updated successfully```  
Congratulations! You now have a new user  

### Step 2 Giving your user sudo privileges:  
Now while you are still root, let's grant sudo privileges to the user.  
This allows the user to execute administrative commands (Which we want them to have).  
Execute the following command:  
```sudo usermod -aG sudo [NAME]```  
Here, -a appends the user to the specified group (sudo) instead of overwriting all previous groups, and -G specifies the group. 

### Step 3 Changing your shell to bash
Changing our shell to bash provides more familiarity as we will be coding in bash a lot  
Execute the following command:  
```sudo chsh -s /bin/bash [NAME]```  

The ```chsh``` command here is our change shell command, the ```-s [PATH]``` refers to shell and /bin/bash is the path to the bash shell. You can also see a list of other shells by doing ```cat /etc/shells```. 

### Step 4 Removing root access
The final step to part 1 will be removing root access to your terminal. This will make it so the next time you ssh you will need to use your user name instead of root  
```ssh -i [PATH_TO_KEY] [USER]@[IP_ADDRESS]```  

Execute the following command:  
```sudo vim /etc/ssh/sshd_config```  
The /etc/ folder will contain all our configuration files.
This will open up the sshd_config in vim. Remember the following:  

* ```i``` - enters insert mode, allowing you to edit the file
* ```esc``` - exits to normal mode, making the file read only, you can now type commands in the terminal
* ```:q``` - quits the vim file
* ```:w``` - writes the vim file
* ```:wq``` - write then quit the vim file

Inside this file enter insert mode and look for the line ```PermitRootLogin yes```  then change it the yes to no. Afterwards, exit to normal mode and then write and quit the file.  

You may want to get ahead of yourself and begin trying to ssh into your VM from root; However, there is always one last step when editing confirguration files. You will need to restart them so the new changes take place on your system.  
Enter the following command:  
```sudo systemctl restart ssh```  
```systemctl``` is the command line tool used to control systemd, the system and service manager on your Linux system. In this line of code you are telling systemd to restart the ssh service and load in the new ssh confirguation files  

If no issues arise then feel free to logout of your VM and try ssh'ing back as root, you should get a ```Permission Denied:``` message which means you did all the steps correctly! Then try logging in with your new user!

## Utilizing Nginx
Explain Nginx and why we might want this

### Step 1 Installing nginx
- sudo apt install nginx

### Step 2 Configure nginx
two important directories /etc/nginx/sites-avilable. /etc/nginx/sites-enabled
on debian, all files to be hosted are in /var/www

Create a .conf in sites-available and talk about which parts of code the change from the default

Create a symbolic link in sites-enabled

### Step 3 Verify nginx running
sudo nginx -t
Restart nginx sudo systemctl restart nginx

### Step 4 Receiving the file
-ip a (address is the same as your debian ip address)

### Step 5 Troubleshooting
-error 404 no file found
-nginx failure

Now you have mastered these two processes and are able to both create a new user with sudo privileges and host a web server on debian!
