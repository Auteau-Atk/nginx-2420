
# Good practice, update Pacman

First, update pacman before starting a project. (Good practice)

```bash

sudo  pacman  -Syu

```

  

# Step 1: Download nginx

Download nginx using the command.

```bash

sudo  -S  nginx

```

Input 'y' when it asks if you want the installation to proceed.

  

# Step 2: Create Root Project Directory

Instead of changing the ```/usr/share/nginx/html/index.html``` file nginx comes with, we will be making our own. Use the following command to create the whole directory for our project.
```bash
mkdir -p /web/html/nginx-2420
```
This command creates ```/web```, ```/html``` and, ```/nginx-2420``` in 1 command.
# Step 3: Edit nginx.conf
Before making our own configuration file, we need to setup the original ```/etc/nginx/nginx.conf```, while it does have configurations that work with ```/usr/share/nginx/html/index.html```, it may error with ```could not build optimal types_hash```.

Instead, we will edit the configuration using an example configuration given in the arch linux wiki.
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
This will set our ```types_hash_max_size``` and our ```worker_connections``` properties. (You can also set the ```worker_connections``` to auto)
Now that everything is setup, we can start making our own configuration and html scripts.

# Step 4: Set Up 
Like mentioned before we are making our own nginx files to work with. In that case, we will set up a server block for our website. First, we have to make 2 directories.
```bash
mkdir /etc/nginx/sites-available
mkdir /etc/nginx/sites-enabled
```

```/etc/nginx/sites-available``` is where we will store all of our ```listen``` configurations and ```/etc/nginx/sites-enabled``` will be how we enable our websites.

