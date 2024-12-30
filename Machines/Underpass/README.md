# Steps
## Step 1. Scan 
- Scan using nmap also scan for UDP ports
- There is a snmp service running on a udp port
- Search about snmp service

## Step 2. SNMP
- view snmp for the ip
- we can find the domain name email host server and other various details
    
## Step 3. Directory Search

- **gobuster /daloradius** <br>
-> /app                  
-> /ChangeLog            
-> /contrib              
-> /doc                  
-> /library              
-> /LICENSE              
-> /setup                

- **gobuster /daloradius/contrib** <br>
-> /db
-> /scripts


- **gobuster /daloradius/doc** <br>
-> /install


- **gobuster /daloradius/app** <br>
    - -> **/common**<br>
	    -> /includes
	    -> /library
	    -> /static
	    -> /templates
	
    - -> **/users** 
	-> /include
	-> /lang
	-> /library
	-> /notifications
	-> /static


- **gobuster /daloradius/library**
nothing

- **gobuster /daloradius/setup**
nothing

> Found ``dirsearch`` to be more effective for directory searching so used this instead <br>

- **dirsearch underpass.htb/**
    - we get docker-compose.yml<br>
    - which consists of database password for environments

- **dirsearchh /daloradius/doc/**
    -  we get /install/INSTALL<br>
    - Reading that gives us password for administrator 

- **dirsearch /daloradius/app**
    - we come across /operators/ which has another login page<br>
    - use the username and password from /INSTALL to go into daloradius<br>
    - search around, we come across users list under management <br>
    - we have user svcMosh which has some encrypted password<br>
    - crack that password with john the ripper to get password for user svcMosh<br>

## Step 4. SSH
- ssh into svcMosh <br>
- get the user flag 

## Step 5. Root
- sudo -l to see the sudo privileges <br>
- we come across /usr/bin/mosh-server which is a command<br>
- mosh lets us run sudo for svcMosh user<br>
- ``mosh --server="sudo /usr/bin/mosh-server" localhost``<br>
- this lets us run as root in localhost<br>
- ``cat /root/root.txt`` to get root password


The most important thing in this machine was enumeration searching and few knowledge about effeciency of different commands.
