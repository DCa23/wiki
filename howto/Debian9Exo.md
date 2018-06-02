# Manual de configuracion de debian como router para exo #

## Indice: ##
  1. Introduccion, pasos previos y convenciones
  2. Configuracion de red
  3. Configuracion del tunel l2tp de salida a internet (exo)
  4. Habilitar forwarding y Configuracion de IPtables

### 1. Introduccion, pasos previos y convenciones ###

Por tal de salir a internet los socios de exo debemos crear un tunel de conexion con la salida a internet, este manual esta orientado a quien quiera realizar esta tarea con un ordenador con linux en lugar de con un router clasico o un router con openwrt ya sea por no disponer de uno o por eleccion personal, este manual esta hecho para Debian 9 no significa que no pueda funcionar en otro SO pero los pasos son susceptibles de cambiar.

Los pasos previos necesarios son:
- Conseguir un ordenador con 2 interfaces de red
- Instalar Debian 9 en el ordenador
- Conectar las 2 interfaces de red a la antena y la red local

Los nombres de las interfaces de red seran
- eth0 -> interfaz conectada a la antena
- eth1 -> interfaz conectada a la red local


### 2. Configuracion de red ###

En primer lugar debemos configurar la red, la tarjeta de la red LAN tendra una ip fija ya que sera la puerta de enlace de la red local, la tarjeta de red conectada a la antena por el contrario tendra una ip dinamica que nos brindara la antena.

Para esto editamos el fichero **/etc/network/interfaces** e introducimos el siguiente contenido al final:

```
# Wan network
auto eth0
allow-hotplug eth0
iface eth0 inet dhcp


#Pci for lan
auto eth1
allow-hotplug eth1
iface eth1 inet static
	address 192.168.1.1
	netmask 255.255.255.0
	gateway 192.168.1.1
```

Ademas hay que añadir un fichero en el directorio **/etc/network/if-up.d/** con el nombre que se desee y se le dan permisos de ejecucion, de esta forma este fichero se ejecutara una vez las interfaces esten levantadas:

```
touch /etc/network/if-up.d/staticroutes
chmod +x /etc/network/if-up.d/staticroutes
```

Se ha de añadir el siguiente contenido:

```
#!/bin/bash
ip route add 10.38.140.225 via 10.1.15.129 dev eth0
```

De esta forma creamos una ruta estatica en la maquina, de manera que siempre que vaya a buscar la ip del tunel de exo, lo hara a traves de la tarjeta de red conectada a la antena, en caso de no hacerlo xl2tpd seguramente dara un error de rebundancia.

Una vez editados estos ficheros cargara las configuraciones al iniciar el ordenador, para que los cambios tengan efecto sin necesidad de reiniciar, ejecutar el siguiente comando.

```
systemctl restart networking
```

### 3. Configuracion del tunel l2tp de salida a internet (exo) ###

Por tal de salir a internet hay que crear un tunel virtual con el servidor de exo. Para esto necesitamos instalar los clientes **xl2tp** y **ppp** que se encargaran de gestionar la conexion.

Para instalarlo ejecutamos lo siguiente:

```
apt update
apt install xl2tp ppp
```

Una vez se haya instalado debemos editar el fichero **/etc/xl2tp/xl2tpd.conf**, hay que borrar el contenido del fichero y dejar las siguientes lineas. (Substituyendo los valores entre [] por vuestro user y password)

```
[global]
access control = no
port = 1723
auth file = /etc/ppp/chap-secrets
debug avp = no
debug network = no
debug packet = no
debug state = no
debug tunnel = no
[lac exo]
lns = 10.38.140.225
redial = yes
redial timeout = 5
require chap = yes
require authentication = yes
ppp debug = no
pppoptfile = /etc/ppp/options.l2tpd
require pap = no
autodial = yes
name = [USERNAME]
```

En el valor name hay que colocar nuestro mail de exo. Posteriormente editamos el fichero **/etc/ppp/options.l2tpd** dejando unicamente el siguiente contenido:(Substituyendo los valores entre [] por vuestro user y password)

```
ipcp-accept-local
ipcp-accept-remote
require-mschap-v2
noccp
noauth
idle 1800
mtu 1420
mru 1410

defaultroute
replacedefaultroute
proxyarp
connect-delay 5000
name [USERNAME]
password [PASSWORD]
```

Los campos name password se pueden introducir de forma alternativa en el fichero **/etc/ppp/pap-secrets**

[Mas info al respecto del fichero secrets](http://l4u-00.jinr.ru/usoft/WWW/HOWTO/PPP-HOWTO-13.html)

Una vez realizada la configuracion, reiniciamos el servicio de xl2tpd para que cargue la nueva configuracion.

```
systemctl restart xl2l2tpd
```

Si hemos configurado todo correctamente al realizar un ifconfig nos deberia mostrar una nueva interfaz llamada pppX, ejemplo.

```
ppp1: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1420
        inet x.x.x.x  netmask 255.255.255.255  destination 192.168.132.1
        ppp  txqueuelen 3  (Point-to-Point Protocol)
        RX packets xx  bytes xx (xx GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets xx  bytes xx (xx MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

### 4. Habilitar forwarding y Configuracion de IPtables ###

Por tal de que la maquina sea capaz de enrutar los paquetes de la red local hacia internet debemos habilitar la redireccion(forwarding) de paquetes, esta opcion esta disponible en el kernel de linux pero por defecto esta desactivada.

Editamos el fichero **/etc/sysctl.conf**, para que el sistema habilite el forwarding al arrancar, hay que añadir la linea:
```
net.ipv4.ip_forward=1
```

Tras reiniciar el ordenador surtira efecto el cambio en la configuración, por tal de habilitar el forwarding sin necesidad de reiniciar se puede ejecutar el comando:
```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

Ahora el servidor puede redirigir el trafico entre sus interfaces, para poder configurar como queremos redirigir el trafico lo haremos mediante IPtables, un firewall ya incluido en la mayoria de distribuciones linux.

Por tal de que el ordenador cargue un fichero de configuracion de ip tables creamos un fichero **/etc/network/if-pre-up.d/iptables** con el siguiente contenido:
```
#!/usr/bin/env bash
iptables-restore < /etc/network/iptables
```
Una vez este creado le damos permisos de ejecucion:
```
chmod +x /etc/network/if-pre-up.d/iptables
```

Esto lo que hara sera cargar el fichero de configuracion **/etc/network/iptables** justo antes de levantar las interfaces.
En este fichero introducimos lo siguiente:

```
*nat

:PREROUTING ACCEPT [0:0]
:INPUT ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]

-A POSTROUTING -s 192.168.1.0/24 -o ppp0 -j MASQUERADE
-A POSTROUTING -s 192.168.1.0/24 -o ppp1 -j MASQUERADE

COMMIT

*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]

# Service rules
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -s 192.168.1.0/24 -i eth1 -p tcp --dport 22 -j ACCEPT
-A INPUT -j DROP


# Forwarding rules
-A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS -m tcpmss --mss 1361:1536 -o ppp0  --set-mss 1360
-A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS -m tcpmss --mss 1361:1536 -o ppp1  --set-mss 1360
-A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.168.1.0/24 -o ppp0 -m conntrack --ctstate NEW,INVALID -j ACCEPT
-A FORWARD -s 192.168.1.0/24 -o ppp1 -m conntrack --ctstate NEW,INVALID -j ACCEPT
-A FORWARD -s 192.168.1.0/24 -o ppp0 -j ACCEPT
-A FORWARD -s 192.168.1.0/24 -o ppp1 -j ACCEPT
-A FORWARD -j DROP

#OUTPUT rules
-A OUTPUT -p tcp --tcp-flags SYN,RST SYN -j TCPMSS -m tcpmss --mss 1361:1536 -o ppp0  --set-mss 1360
-A OUTPUT -p tcp --tcp-flags SYN,RST SYN -j TCPMSS -m tcpmss --mss 1361:1536 -o ppp1  --set-mss 1360
-A OUTPUT -j ACCEPT

COMMIT

```

Esto hara que se cargue esta configuracion al iniciar el ordenador, si queremos cargar de forma inmediata la configuracion basta con ejecutar el script que hemos creado.

```
/etc/network/if-pre-up/iptables
```


Una vez realizada la configuracion si realizamos un traceroute desde una maquina conectada a la lan deberiamos ver algo como esto:

```
> traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  _gateway (192.168.1.1)  0.606 ms  0.563 ms  0.539 ms
 2  192.168.132.1 (192.168.132.1)  20.136 ms  20.128 ms  20.327 ms
 3  10.253.4.21 (10.253.4.21)  23.363 ms  24.045 ms  24.037 ms
 4  google.02.catnix.net (193.242.98.156)  31.372 ms  34.516 ms  33.797 ms
 5  108.170.253.225 (108.170.253.225)  35.003 ms  34.995 ms  34.978 ms
 6  216.239.48.245 (216.239.48.245)  34.962 ms 216.239.49.149 (216.239.49.149)  30.048 ms 216.239.35.211 (216.239.35.211)  31.319 ms
 7  google-public-dns-a.google.com (8.8.8.8)  30.303 ms  40.201 ms  39.797 ms
```

Y ya podriamos conectarnos a internet con cualquier ordenador conectado a la lan del servidor debian.
