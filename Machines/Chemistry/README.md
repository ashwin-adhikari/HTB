## 1. Scan 
`` nmap -sC -sV --min--rate 3500 10.10.11.38``
- This gives us two open ports 22 and 5000
- Go to ``10.10.11.38:5000`` we find a login and register dashboard.
- Simply register using any username and password
    - username: admin123
    - password: 1234
## 2. Search for CFI CVE exploit
- We come across an exploit leading to CFI arbitrary code execution [CFI Exploit](https://github.com/9carlo6/CVE-2024-23346)
- We also have a clue from ``example.cif``
- Make an ``exploit.cif`` file and upload it to the upload section
- And make sure you are listening on the given port with ``nc -lvnp <port>``
- Click on view we will get a shell on the terminal
- check for the user using ``whoami`` -> it is ``app``
- search around the directories
- we come across database.db in ~/instance/
![alt text](/Machines/Chemistry/images/databasedb.png)
- read the file
- It has hashes of passwords copy hash for rosa and decrypt hash to get password

## 3. Use a python shell
- We are unable to use sudo command to read user.txt 
- So we use python to generate shell using ``python3 -c 'import pty; pty.spawn("/bin/bash")'``
- we are in app@chemistry we need to get access to rosa to read user.txt 
- so we use ``ssh rosa@10.10.11.38`` and give password to it.
- Now we are able to read user.txt to get userflag

## 4. Read root flag
- After logging in as rosa user. Check for open ports using ``netstat -tulnp``
- We see ``localhost:8080`` running 
- We cannot directly access it so instead we use ssh tunnel for it using ``ssh -L 8080:127.0.0.1:8080 rosa@1010.11.38``

- We can now access the website in our browser
- Check the header of the site using ``curl -I http://127.0.0.1:8080``
    ![alt text](/Machines/Chemistry/images/curl.png)
- we see ``Server: Python/3.9 aiohttp/3.9.1`` ; think to notice is aiohttp which contains vulnerability
- Search for the CVE we come across the path traversal vulnerability in the python AioHTTP library =< 3.9.1
- We came across [CVE-2024-23334](https://github.com/z3rObyte/CVE-2024-23334-PoC/tree/main)
    ![alt text](/Machines/Chemistry/images/cve.png)
- Now searching for directories to traverse using ``dirsearch -u http://localhost:8080``
    ![alt text](/Machines/Chemistry/images/dirsearch.png)
- We come across /assets/
- Updating the ``exploit.sh`` to attack
    ![alt text](/Machines/Chemistry/images/exploit.png)
- Running it we obtain root flag
    ![alt text](/Machines/Chemistry/images/root.png)
