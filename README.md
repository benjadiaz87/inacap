[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/)

Demo para crear laboratorios automatizados para la carrera de seguridad en INACAP
====================

Primera parte del lab:

La primera parte del laboratorio consiste en ingresar via ssh a la maquina con nombre kali-linuxvm01. Esta maquina tiene una ip publica. Para obtener la lista de maquinas virtuales corriende se puede correr el comando 

        az vm list-ip-addresses -g inacap-demoRG --out table

        VirtualMachine    PublicIPAddresses    PrivateIPAddresses
        ----------------  -------------------  --------------------
        kali-linuxvm01    191.237.252.78       10.0.0.4
        ubuntuvm-01                            10.0.0.6
        windows-01                             10.0.0.5

En este caso tanto la maquina windows-01 como kali-linuxvm01 tienen una IP publica. Podemos llegar a la maquina kali-linuxvm01 via ssh con la ip 191.237.252.78. Corramos un ifconfig dentro de la maquina:

benjadiaz@kali-linuxvm01:~$ ifconfig

        eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500

                inet 10.0.0.4  netmask 255.255.255.0  broadcast 10.0.0.255
                inet6 fe80::20d:3aff:fec0:6f03  prefixlen 64  scopeid 0x20<link>
                ether 00:0d:3a:c0:6f:03  txqueuelen 1000  (Ethernet)
                RX packets 266398  bytes 82820133 (78.9 MiB)
                RX errors 0  dropped 0  overruns 0  frame 0
                TX packets 322630  bytes 76050507 (72.5 MiB)
                TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

        lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536

                inet 127.0.0.1  netmask 255.0.0.0
                inet6 ::1  prefixlen 128  scopeid 0x10<host>
                loop  txqueuelen 1000  (Local Loopback)
                RX packets 463  bytes 39116 (38.1 KiB)
                RX errors 0  dropped 0  overruns 0  frame 0
                TX packets 463  bytes 39116 (38.1 KiB)
                TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


De la tarjeta de red eth0 podemos entender que la ip interna es 10.0.0.4 y que la mascara es 10.0.0.255. O sea 10.0.0.0/24.

Ahora entendamos que otras maquinas estan en la red utilizando nmap (ya instalado por defecto en Kali):

        sudo nmap -sP -PI -PT 10.0.0.0/24
        
La salida de nmap nos va a mostrar los hosts que hay activos en la red donde esta la maquina. En este caso nos damos cuenta que las maquinas en la red son:

        Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-20 10:22 EST
        Nmap scan report for 10.0.0.1
        Host is up (0.00045s latency).
        MAC Address: 12:34:56:78:9A:BC (Unknown)
        Nmap scan report for 10.0.0.5
        Host is up (0.00064s latency).
        MAC Address: 12:34:56:78:9A:BC (Unknown)
        Nmap scan report for 10.0.0.6
        Host is up (0.0011s latency).
        MAC Address: 12:34:56:78:9A:BC (Unknown)
        Nmap scan report for 10.0.0.4

Hay dos hosts en la red mas aparte de el de kali-linuxvm01. 
Ahora hagamos un scan de puertos abiertos en los hosts que estan arriba:

Partamos por la maquina 10.0.0.5:

        sudo nmap -sS -O -PI -PT 10.0.0.5

        Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-20 10:25 EST
        Nmap scan report for 10.0.0.5
        Host is up (0.00088s latency).
        Not shown: 999 filtered ports
        PORT     STATE SERVICE
        3389/tcp open  ms-wbt-server
        MAC Address: 12:34:56:78:9A:BC (Unknown)
        Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
        Device type: specialized|general purpose
        Running (JUST GUESSING): AVtech embedded (87%), FreeBSD 6.X (85%)
        OS CPE: cpe:/o:freebsd:freebsd:6.2
        Aggressive OS guesses: AVtech Room Alert 26W environmental monitor (87%), FreeBSD 6.2-RELEASE (85%)
        No exact OS matches for host (test conditions non-ideal).
        Network Distance: 1 hop

        OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
        Nmap done: 1 IP address (1 host up) scanned in 9.44 seconds
 
Nos damos cuenta que la maquina tiene abierto un puerto RDP, por lo cual podemos intentar conectarnos por RDP,
Ahora vamos con la 10.0.0.6:

        sudo nmap -sS -O -PI -PT 10.0.0.6

        Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-20 10:25 EST
        Nmap scan report for 10.0.0.6
        Host is up (0.00078s latency).
        Not shown: 999 closed ports
        PORT   STATE SERVICE
        22/tcp open  ssh
        MAC Address: 12:34:56:78:9A:BC (Unknown)
        No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
        TCP/IP fingerprint:
        OS:SCAN(V=7.70%E=4%D=12/20%OT=22%CT=1%CU=40489%PV=Y%DS=1%DC=D%G=Y%M=123456%
        OS:TM=5C1BB485%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10D%TI=Z%CI=I%II=
        OS:I%TS=A)OPS(O1=M58AST11NW7%O2=M58AST11NW7%O3=M58ANNT11NW7%O4=M58AST11NW7%
        OS:O5=M58AST11NW7%O6=M58AST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%W
        OS:6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M58ANNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=
        OS:O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD
        OS:=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0
        OS:%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1
        OS:(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI
        OS:=N%T=40%CD=S)

        Network Distance: 1 hop

        OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
        Nmap done: 1 IP address (1 host up) scanned in 12.05 seconds

Instalemos un escritorio en la maquina Kali:

        sudo apt-get update
        sudo apt-get install xfce4
        sudo apt-get install xrdp
        sudo systemctl enable xrdp
        echo xfce4-session >~/.xsession
        sudo service xrdp restart
        
Con esto, ya deberiamos poder acceder a la maquina Kali via RDP para poder utilizar las herramientas de penetration testing de Kali a nivel escritorio. En Ubuntu se puede utilizar Remmina y en Windows Remote desktop client.

Nos dimos cuenta que la maquina Windows tiene un puerto RDP abierto y una IP interna definida. Intentemos hacer RDP a esa IP, ya que sabemos que esa VM tiene un puerto 3389 abierto. Como la maquina no tiene una IP Publica tenemos que accederla desde Kali. Para esto debemos instalar Remmina en Kali.

        sudo apt-get update
        sudo apt-get install remmina

Ahora desde remmina intentemos llegar a la IP de la maquina Windows que sabemos que tiene abierto el puerto 3389, y probablmente tiene escritorio remoto como servicio arriba. 

Como sabemos que la VM ubuntu tiene el puerto 22 abierto, podemos intentar lo mismo y llegar via ssh a la maquina ubuntu. 

Notar que ni la maquina Windows ni la maquina Ubuntu tienen una IP Publica, por ende, la unica manera de llegar a ellas es desde la maquina Kali que esta en la misma red. Desde la maquina Kali, intentemos llegar a la maquina ubuntu.

        ssh benjadiaz@10.0.0.6
        sudo apt-get update

Intentemos instalar Apache desde la mauqina Ubuntu.

        sudo apt-get install apache2
        sudo service --status-all   
        
Esta maquina no tiene escritorio, asi que para poder ver si es que podemos ver el landing page de Apache, tenemos que entrar desde Edge en la maquina Windows.

Esta Arriba.




