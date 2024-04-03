# Update Pacman

First, update pacman before starting a project. (Good practice)

```bash

sudo  pacman  -Syu

```

```sudo``` will bypass root restrictions

# Step 1: Download nginx

Download nginx using the command.

```bash

sudo  -S  nginx

```

Input 'y' when it asks if you want the installation to proceed.

# Step 2: Create Root Project Directory

Instead of changing the ```/usr/share/nginx/html/index.html``` file nginx comes with, we will be making our own. Use the following command to create the whole directory for our project.

```bash

mkdir  -p  /web/html/nginx-2420

```
```mkdir``` creates a directory and ```-p``` allows multiple directories to be created

This command creates ```/web```, ```/html``` and, ```/nginx-2420``` in 1 command.

# Step 3: Edit nginx.conf

Before making our own configuration file, we need to setup the original ```/etc/nginx/nginx.conf```, while it does have configurations that work with ```/usr/share/nginx/html/index.html```, it may error with ```could not build optimal types_hash```.  

Instead, we will edit the configuration using an example configuration given in the arch linux wiki.

```bash 
sudo  vim /etc/nginx/nginx.conf
```

```vim``` is our text editor where we can write code for our files.

```bash

user http;
worker_processes auto;
worker_cpu_affinity auto;

events {
    multi_accept on;
    worker_connections 1024;
}

http {
    charset utf-8;
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    server_tokens off;
    log_not_found off;
    types_hash_max_size 4096;
    client_max_body_size 16M;

    # MIME
    include mime.types;
    default_type application/octet-stream;

    # logging
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log warn;

    # load configs
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}

```

This will set our ```types_hash_max_size``` and our ```worker_processes``` properties. ```worker_processes``` is important as it sets how many connections ```nginx``` will accept and how many prcessors it will use.

Now that everything is setup, we can start making our own configuration and html scripts.

# Step 4: Set Up

Like mentioned before we are making our own nginx files to work with. In that case, we will set up a server block for our website. First, we have to make 2 directories.

```bash

sudo  mkdir  /etc/nginx/sites-available

sudo  mkdir  /etc/nginx/sites-enabled

```

```/etc/nginx/sites-available``` is where we will store all of our ```listen``` configurations and ```/etc/nginx/sites-enabled``` will be how we enable our websites.

# Step 5: Create nginx-2420.conf

This is where we setup our ```listen``` and ```domain ip```. First, make the file.

```bash

sudo  vim  /etc/nginx/sites-available/nginx-2420

```
Paste this.

```bash

server {
    listen 80;
    server_name your_domain_or_ip;

    root /web/html/nginx-2420;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

```

This will setup the website and where our html file is located.

The location will be our root directory we made ```root /web/html/nginx-2420``` and the html file will be ```index index.html```.

We will be making the html in the coming steps.

# Step 6: Symlink sites-available and sites-enabled

This is how we setup the website and enable it so that if we put our ip into a browser, we will see the html file we created.

```bash

# Enable Website

ln  -s  /etc/nginx/sites-available/nginx-2420  /etc/nginx/sites-enabled/

```
This will link ```nginx-2420``` between both directories so that we can easily enable and disable the website we want to output to the browser.

```bash
# Disable Website
unlink /etc/nginx/sites-enabled/example.conf
```

If we disable the Symlink, the website won't be on the browser anymore.

# Step 7: Test and Start Server
Now that our backend is setup, we can test if we have any errors and if not, we can start our server.
```bash
# Check for errors
nginx -t

# Start server if you haven't started the server already
sudo systemctl start nginx.service

# Restart server if changes were made
sudo systemctl restart nginx.service
```

```sytemctl``` is a command line tool that monitors our system and interacrs with our systemd, service managers, etc.

# Step 8: Create HTML file
We can now create our HTML file.
```bash
sudo vim /web/html/nginx-2420/index.html
```
Then either input your own HTML file or use this example file.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        * {
            color: #db4b4b;
            background: #16161e;
        }
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
            font-family: sans-serif;
        }
    </style>
</head>
<body>
    <h1>All your base are belong to us</h1>
</body>
</html>
```
Once this is made, restart nginx
```bash
sudo systemctl restart nginx.service
```

In your browser, put your ip address used to log into you droplet ```ssh root@your_droplet_ip``` and your website should be there.