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

```
sudo useradd [NAME]
```  

Command should be self-explanatory, will add this user to our users  
Note: We are using useradd instead of adduser as it is a lower-level command and therefore more accessible on different distros.  
There should be no response after the command  

Now give your new user a password for them to log in using the following command:  
```
sudo passwd [NAME]
```  

If completed successfully you should see  

```passwd: password updated successfully```  
Congratulations! You now have a new user  

### Step 2 Giving your user sudo privileges:  
Now while you are still root, let's grant sudo privileges to the user.  
This allows the user to execute administrative commands (Which we want them to have).  
Execute the following command:  
```
sudo usermod -aG sudo [NAME]
```  
Here, -a appends the user to the specified group (sudo) instead of overwriting all previous groups, and -G specifies the group. 

### Step 3 Changing your shell to bash
Changing our shell to bash provides more familiarity as we will be coding in bash a lot  
Execute the following command:  
```
sudo chsh -s /bin/bash [NAME]
```  

The `chsh` command here is our change shell command, the `-s [PATH]` refers to shell and /bin/bash is the path to the bash shell. You can also see a list of other shells by doing `cat /etc/shells`. 

### Step 4 Removing root access
The final step to part 1 will be removing root access to your terminal. This will make it so the next time you ssh you will need to use your user name instead of root  
```
ssh -i [PATH_TO_KEY] [USER]@[IP_ADDRESS]
```  

Execute the following command:  
```
sudo vim /etc/ssh/sshd_config
```  
The /etc/ folder will contain all our configuration files.
This will open up the sshd_config in vim. Remember the following:  

* `i`- enters insert mode, allowing you to edit the file
* `esc` - exits to normal mode, making the file read only, you can now type commands in the terminal
* `:q` - quits the vim file
* `:w` - writes the vim file
* `:wq` - write then quit the vim file

Inside this file enter insert mode and look for the line `PermitRootLogin yes`  then change it the `yes` to `no`. Afterwards, exit to normal mode and then write and quit the file.  

You may want to get ahead of yourself and begin trying to ssh into your VM from root; However, there is always one last step when editing confirguration files. You will need to restart them so the new changes take place on your system.  
Enter the following command:  
```
sudo systemctl restart ssh
```  
`systemctl` is the command line tool used to control systemd, the system and service manager on your Linux system. In this line of code you are telling systemd to restart the ssh service and load in the new ssh confirguation file  

If no issues arise then feel free to logout of your VM and try ssh'ing back as root, you should get a ```Permission Denied:``` message which means you did all the steps correctly! Then try logging in with your new user!

## Utilizing Nginx
Nginx is a popular web server for letting us configure which static files to serve and to what incoming traffic

### Step 1 Installing nginx
Enter the following command:  
```
sudo apt install nginx
```  
The Advanced Packaging Tool (apt) lets us manage packages, this command will install nginx on our system

### Step 2 Configure nginx
Nginx configuration involves two important directories: /etc/nginx/sites-available and /etc/nginx/sites-enabled. Sites-available will contain cofiguration files to load your sites, abd sites-enabled will contain symbolic links to those configuration files that will tell the service that the linked files are active.  
All files to be hosted are typically placed in /var/www by default, although you may change this in the configuration.  

Lets create a index.html file to be retrieved so we can test our server out  
Create a folder called  my-site in /var/www:  
```
sudo mkdir /var/www/my-site
```  
Now let's create the index.html inside this folder using this command:  
```
sudo vim /var/www/my-site/index.html
```  
This will create a new file in which you can enter insert mode and paste the following code:  

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
        }
    </style>
</head>
<body>
    <h1>Hello, World</h1>
</body>
</html>
```
Be sure to write and quit as we are done here, we will now make the web server!  

Enter the following command to create a configuration file in sites-available:  
```
sudo vim /etc/nginx/sites-available/my-site.conf
```

Paste the following:  
```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/my-site;

    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```  
Explaining the following:  
* `/var/www/my-site` was altered from the default so that it only serves files in this folder instead of `/var/www/`
* `index index.html....etc` are the file types that this web server will look for
* `server_name _;` defines the domain names or ip addresses that this web server will accept requests from, right now it is set to wildcard which means it accepts all traffic.

Now we can save and quit from this file and next is creating the symbolic link in sites-enabled to make this server live.
Enter the following:  
```
sudo ln -s /etc/nginx/sites-available/my-site.conf /etc/nginx/sites-enabled
```  
Additionally, we are going to be unlinking the default web server to make sure we don't get any conflicts
```
sudo unlink /etc/nginx/sites-enabled/default
```  

### Step 3 Verify nginx running
Now to ensure everything is working lets verify that nginx is functional
Enter the following:  
```
sudo nginx -t
```  
This command will check for syntax errors on the nginx configurations  
If no errors show then proceed to enter:
```
sudo systemctl restart nginx
```

### Step 4 Receiving the file
Now to test if the web server is functional, go to your web browser and enter in the ip of your VM. If you see a "Hello World" in the middle of the page, congratulations! You have mastered these two processes and are able to both create a new user with sudo privileges and host a web server on debian!
