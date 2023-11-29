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

### Step 1 Adding a user: 
useradd
-give user password
-mention adduser and why we aren't using it

### Step 2 Giving your user sudo privileges:
usermod -aG
-explain a and G and the wheel group
-Test that you can sudo

### Step 3 Changing your shell to bash
-why?
-verifying that you are bashing

### Step 4 Removing root access
- config file in etc folder
- verify you cant root ssh anymore

Done!

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

Now you have mastered these two processes and are able to both create a new user with sudo privileges and host a web server on debian! Pogu
