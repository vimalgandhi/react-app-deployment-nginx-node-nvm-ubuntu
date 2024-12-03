# React App Deployment with Nginx, Node.js, and NVM on Ubuntu Server

This guide walks you through the process of setting up a React app on an Ubuntu server using Nginx, Node.js, and NVM (Node Version Manager).

## Prerequisites

- A server running Ubuntu or a similar Debian-based Linux distribution.
- Sudo privileges to install packages and modify system configurations.
- A React application ready for deployment.

## Steps-1 (Fonrt-end)
### 1. Update System Packages

Start by updating the package list and upgrading any existing packages:

```bash
sudo apt-get update -y
```

### 2. Install Node.js (v20.x)
Install the latest stable version of Node.js (v20.x) by adding the NodeSource repository and running the following commands:
```
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

### 3. Install npm (Node Package Manager)
To manage Node.js packages, install npm globally:
```
sudo apt install npm -y
```

### 4. Install Nginx
Next, install the Nginx web server:
```
sudo apt install nginx -y

```

### 5. Install NVM (Node Version Manager)
NVM allows you to install and manage different versions of Node.js. Install NVM with the following command:
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash

```
After installing NVM, make sure to load it into your shell session:
```
source ~/.nvm/nvm.sh
```

### 6. Install Node.js Version 20 Using NVM
Now, install Node.js version 20 using NVM:
```nvm install 20```

###7. Configure Nginx for React App
Remove Existing Nginx Sites
Clean up any existing site configurations in Nginx and create necessary directories:

```
rm -rf sites-* && sudo mkdir -p /etc/nginx/sites-available && sudo mkdir -p /etc/nginx/sites-enabled
```
Create a New Nginx Configuration File
Create a new Nginx site configuration file for your React app. Use a text editor to define your site's configuration:
```sudo nano /etc/nginx/sites-available/<file-name>```

In this file, replace the content with your React app's specific settings. Here's an example configuration for a basic React app:

```
server {
  listen 80;
  server_name your_domain_or_IP;

  root /path_to_your_react_app/build;

  index index.html;

  location / {
    try_files $uri /index.html;
  }
}
```

Make sure to replace /path_to_your_react_app/build with the actual path to your React app's build folder after running npm run build.

Enable the Site
Create a symbolic link to enable the site in Nginx:

```
sudo ln -s /etc/nginx/sites-available/<File name> /etc/nginx/sites-enabled/

```

Test Nginx Configuration
Before restarting Nginx, test the configuration for any errors:
```sudo nginx -t```

f the test passes without errors, proceed to restart Nginx.

### 8. Restart Nginx
Finally, restart Nginx to apply the new configuration:
```
sudo systemctl restart nginx
```

## Steps-1 (Back-end)
1. Install PM2 Globally
Install PM2, a process manager for Node.js applications, globally using npm:
```
sudo npm install -g pm2
```

### 2. Clone the Git Repository
Clone the GitHub repository containing your Node.js app:
```
git clone https://github.com/johnpapa/node-hello.git
```

### 3. Start the Application with PM2
Navigate to the app's directory and start the app using PM2. Replace "your-app-name" with a name for your app:

```
cd node-hello
pm2 start app.js --name "your-app-name"
```

update /etc/nginx/sites-available/react-app file
```
 # Handle API requests - reverse proxy to Node.js backend (running on port 3000)
    location /api/ {
        proxy_pass http://localhost:3000/;  # Adjust port if necessary
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

```


### Troubleshooting
If you encounter any issues with Nginx not starting, run the following command to check for configuration errors:
```
sudo nginx -t
```
Make sure that your React app has been built using:
```
npm run build
```
Ensure that the path in the Nginx configuration file points to the correct build folder of your React app.





# Full code :
```

sudo apt-get update -y
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
sudo apt install npm -y
sudo apt install nginx -y
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
source ~/.nvm/nvm.sh
nvm install 20

rm -rf sites-* && sudo mkdir -p /etc/nginx/sites-available && sudo mkdir -p /etc/nginx/sites-enabled
sudo nano /etc/nginx/sites-available/react-app <react-app file name>
server {
    listen 80;
    server_name 34.205.32.201;

    # Serve static files from the /build folder in /var/www
    root /var/www/react-aws-ec2-nginx/build;

    # Serve index.html for all routes
    index index.html;

    # Handle all requests
    location / {
        try_files $uri /index.html;
    }

    # Serve static files with cache
    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        log_not_found off;
        access_log off;
        add_header Cache-Control "public";
    }
    
    location /api/ {
        proxy_pass http://localhost:3000/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    # Serve error pages
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /var/www/react-aws-ec2-nginx/build;
    }
}

sudo ln -s /etc/nginx/sites-available/react-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```
