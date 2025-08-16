# cse135-2025-ss2-HW1

## Part 2 ： Deploy from Github

Deploying from GitHub (Local) to DigitalOcean Server

1. Server Setup
   
1.1 Create a --bare repository

On the server, create a bare repo at `/var/repo/site.git` to receive pushes from your local machine:

`sudo mkdir -p /var/repo`

`sudo chown -R yanhua:yanhua /var/repo`

`cd /var/repo`

`git init --bare site.git`

1.2 Create the post-receive hook

Edit `/var/repo/site.git/hooks/post-receive` and add:

`#!/bin/sh`

`set -e`

`git --work-tree=/var/www/cse135.online --git-dir=/var/repo/site.git checkout -f`

Make it executable:

`chmod +x /var/repo/site.git/hooks/post-receive`

1.3 Ensure the web root exists and is writable

`sudo mkdir -p /var/www/cse135.online`

`sudo chown -R yanhua:yanhua /var/www/cse135.online`

2. Local Machine Setup (Windows + Git Bash)

2.1 Go to local site source folder

`cd ~/OneDrive/Desktop/COG108/cse135-2025-ss2-HW1`

2.2 Initialize Git

`git init`

2.3 Add and commit

`git add .`

`git commit -m "test-test-test deployment"`

2.4 branch to main

`git branch -M main`

2.5 Add the server as a remote

`git remote remove prod 2>/dev/null`

`git remote add prod ssh://yanhua@cse135.online/var/repo/site.git`

2.6 push to server

`git push -u prod main`

The server’s post-receive hook automatically checks out the latest code "into /var/www/cse135.online/" so the site updates instantly.

   
## Step 4: Employ password protection

Login information: 

username:grader
password:8888

username:nicole
password:8888

username:yanhua
password:8888


## Step 5: Compression Verification

Enabled mod_deflate in Apache to compress HTML, CSS, and JS

Verified using Chrome DevTools:

content-encoding: gzip present in Response Headers

HTML reduced in transfer size, confirming that compression is active.

After enable the compression, the HTML file sent from the server became much smaller in size when transferred, but the actual page content stayed the same. The browser still showed the full HTML after it was uncompressed. This made the page load faster and used less data.


## Step 6: Obscure server identity

Hiding server- information or server version can help prevent attackers from accessing your server. This improves security, as clear version information gives hackers a better understanding of where to begin looking for vulnerabilities. I spent a long time working on this part, and we didn't successfully to create a 'server: CSE135 Server'.But I removed the specific Apache version information to minimize risk within my capabilities.

#Create a directory for hw1 report
ls -l /var/www/cse135.online/
sudo mkdir -p /var/www/cse135.online/hw1

## Step 8: Verify Access Logs and Run a Report

## Give permission to users

`sudo chown -R yanhua:8888 /var/www/cse135.online/hw1`

`goaccess /var/log/apache2/access.log \
  --log-format=COMBINED \
  -o /var/www/cse135.online/hw1/report.html`

`sudo chown -R nicole:8888 /var/www/cse135.online/hw1`

`goaccess /var/log/apache2/access.log \
  --log-format=COMBINED \
  -o /var/www/cse135.online/hw1/report.html`

`sudo chown -R grader:8888 /var/www/cse135.online/hw1`

`sudo chmod 755 /var/www/cse135.online/hw1`

`ls -ld /var/www/cse135.online /var/www/cse135.online/hw1`

`goaccess /var/log/apache2/access.log \
  --log-format=COMBINED \
  -o /var/www/cse135.online/hw1/report.html`

Check
`ls -lh /var/www/cse135.online/hw1/`

Give permission to access adm

`sudo usermod -aG adm yanhua`

`sudo usermod -aG adm nicole`

`sudo usermod -aG adm grader`

`exit`

relogin ssh: `groups`

GoAccess(generates the report)
`goaccess /var/log/apache2/access.log \
  --log-format=COMBINED \
  -o /var/www/cse135.online/hw1/report.html`

Check

`sudo chmod 644 /var/www/cse135.online/hw1/report.html`

`ls -lh /var/www/cse135.online/hw1/report.html`

`curl -I https://cse135.online/hw1/report.html`


We found HTTP/1.1 500 Internal Server Error

Check the log

`sudo grep -n "report.html" /var/log/apache2/error.log | tail -n 20`

`sudo tail -n 80 /var/log/apache2/error.log`

We found the error is :ModSecurity: Output filter: Content-Length (970165) over the limit (524288).

`sudo nano /etc/modsecurity/modsecurity.conf`

We change this part: “limited” to “ProcessPartial”
`SecResponseBodyLimitAction ProcessPartial`

`sudo apache2ctl configtest`
`sudo systemctl reload apache2`

Reload the page, done!
