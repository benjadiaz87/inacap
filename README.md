[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/)

Demo para crear laboratorios automatizados para la carrera de seguridad en INACAP
====================

Primera parte del lab:

La primera parte del laboratorio consiste en ingresar via ssh a la maquina con nombre kali-linuxvm01. Esta maquina tiene una ip publica. Para obtener la lista de maquinas virtuales corriende se puede correr el comando 

        az vm list-ip-addresses --out table

        VirtualMachine    PublicIPAddresses    PrivateIPAddresses
        ----------------  -------------------  --------------------
        kali-linuxvm01    191.237.252.78       10.0.0.4
        ubuntuvm-01                            10.0.0.6
        windows-01        191.237.249.89       10.0.0.5

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

        nmap -sP -PI -PT 10.0.0.0/24
        
La salida de nmap nos va a mostrar

        Starting Nmap 7.70 ( https://nmap.org ) at 2018-12-20 08:41 EST
        Stats: 0:00:01 elapsed; 0 hosts completed (0 up), 256 undergoing Ping Scan
        Ping Scan Timing: About 31.25% done; ETC: 08:41 (0:00:04 remaining)
        Nmap scan report for 10.0.0.4
        Host is up (0.00010s latency).
        Nmap scan report for 10.0.0.6
        Host is up (0.0022s latency).
        Nmap done: 256 IP addresses (2 hosts up) scanned in 1.91 seconds



