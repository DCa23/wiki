# Generació de firmwares de VPN per TTN-Guifi.net per dispositius LEDE/OpenWRT

El projecte TTN-guifi.net es basa en la col·laboració de la comunitat TTN a Catalunya i la de Guifi.net per tal de desplegar una xarxa de gateways [Lora](https://en.wikipedia.org/wiki/LPWAN#LoRa) del [LORIX One](https://lorixone.io/) a la xarxa oberta Guifi.net.

## Descripció de la solució

Argumentem aquí per què hem triat l'opció de VPN, pros i contres...Descripció dels elements que conformen l'arquitectura i firmwares usats.

El pla d'adreçament considerant una solució tipus VPN amb protocol d'encaminament intern:

| Segment       | Subxarxa IPv4| Hosts adreçables | Comentaris  |Prefix d'agregació|
| ------------- |-------------:| -----:|--------:|--------:|
| GWs Lora Grup 0 | `172.16.N.0/24`| `254` | `N=1,2,...,255`|`172.16.0.0/14`|
| GWs Lora Grup 1 | `172.17.N.0/24`| `254` | `N=1,2,...,255`|`172.16.0.0/14`|
| GWs Lora Grup 2 | `172.18.N.0/24`| `254` | `N=1,2,...,255`|`172.16.0.0/14`|
| GWs Lora Grup 3 | `172.19.N.0/24`| `254` | `N=1,2,...,255`|`172.16.0.0/14`|
| Malla Broker 1| `172.31.0.N/22`| `1022`|`N=1,2,...,255`|`172.31.0.0/21`|
| Malla Broker 2| `172.31.4.N/22`| `1022`|`N=1,2,...,255`|`172.31.0.0/21`|
| Accés admins Wireguard | `172.31.252.0/24`| `254` | |`172.31.252.0/22`|
| Accés admins Wireguard | `172.31.253.0/24`| `254` | |`172.31.252.0/22`|
| Accés admins Wireguard | `172.31.254.0/24`| `254` | |`172.31.252.0/22`|
| Accés admins Wireguard | `172.31.255.0/24`| `254` | |`172.31.252.0/22`|


# Configuracions de referència

La configuració de referència pel *Gateway-client* és:
```
config globals 'globals'

config interface 'lan'
        option type 'bridge'
        option proto 'static'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option _orig_ifname 'eth0 wlan0'
        option _orig_bridge 'true'
        option ifname 'eth0'
        option ipaddr '172.16.1.1'

config interface 'wan'
        option ifname 'eth1'
        option _orig_ifname 'eth1'
        option _orig_bridge 'false'
        option proto 'static'
        option ipaddr '10.228.193.115'
        option netmask '255.255.255.224'
        option dns '10.228.203.104'

config interface 'ttn'
        option proto 'wireguard'
        option private_key '6Jm4kaReQsnuyNva4wvg8D3F8V7ZbFvUB5EHpy3h73ax'
        option listen_port '45955'
        option mtu '1396'
        list addresses '172.31.0.3/22'

config wireguard_ttn
        list allowed_ips '0.0.0.0/0'
        option endpoint_host '10.38.140.235'
        option endpoint_port '45955'
        option public_key 'LBW/StqTmQdJLWeWuIUYNkRbSFa3s3RGe7MdeLrV01E='

config route
        option interface 'wan'
        option target '10.0.0.0'
        option netmask '255.0.0.0'
        option gateway '10.228.193.97'
```
Pel que fa al *TTN Broker* tenim:
```
config globals 'globals'

config interface 'lan'
        option type 'bridge'
        option ifname 'eth0'
        option proto 'static'
        option ipaddr '10.38.140.235'
        option netmask '255.255.255.224'
        option ip6assign '60'
        option dns '8.8.8.8'
        option gateway '10.38.140.225'

config interface 'wan'
        option proto 'static'
        option ifname 'eth1'
        option ipaddr '109.69.10.98'
        option netmask '255.255.255.224'
        option gateway '109.69.10.126'

config route
        option interface 'lan'
        option target '10.0.0.0'
        option netmask '255.0.0.0'
        option gateway '10.38.140.225'

config interface 'ttn'
        option proto 'wireguard'
        option private_key 'tBtpfwf65JkCyeuhc47bt87fJRdUKhSBphXLxrKXAM8='
        option listen_port '45955'
        option mtu '1396'
        list addresses '172.31.0.1/22'

config wireguard_ttn
        option public_key 'u7VjcLp3N2sJ7EcFjsPuRw9pYu6ogRO70NT1eewl+AU='
        option endpoint_port '45955'
        option endpoint_host '10.90.234.5'
        list allowed_ips '172.16.0.0/24'
        list allowed_ips '172.31.0.0/22'
        list allowed_ips '172.31.252.0/22'

config wireguard_ttn
        option public_key '7JfICIH5zKTSoH/5YT8kMkDmVQdg5Oy2r4PM2PId81c='
        option endpoint_host '10.228.193.115'
        option endpoint_port '45955'
        list allowed_ips '172.16.1.0/24'
        list allowed_ips '172.31.0.0/22'
        list allowed_ips '172.31.252.0/22'

config interface 'adm'
        option proto 'wireguard'
        option private_key 'tBtpfwf65JkCyeuhc47bt87fJRdUKhSBphXLxrKXAM8='
        list addresses '172.31.252.1/24'
        option listen_port '51820'

config wireguard_adm
        option public_key 'CelE+tvz3yCVePvHcEK8um7Wah2CHyDmQkU4B+PKNzY='
        list allowed_ips '172.16.0.0/14'
        list allowed_ips '172.31.0.0/21'
        list allowed_ips '172.31.252.0/22'
```

També ens caldrà un protocol d'encaminament dinàmic. Instal·lem OLSR sense cap extensió o plugin:
```
root@LEDE:~# opkg install olsrd luci-app-olsr
```
La configuració minimalista per tal que funcioni l'anunci de prefixes dins la VPN és:
```
config olsrd             
        option IpVersion '4'
        option FIBMetric 'flat'
        option LinkQualityLevel '2'
        option LinkQualityAlgorithm 'etx_ff'
        option OlsrPort '698'
        option Willingness '3'
        option NatThreshold '1.0'

config LoadPlugin
        option library 'olsrd_arprefresh.so.0.1'
        option ignore '1'

config LoadPlugin
        option library 'olsrd_dyn_gw.so.0.5'
        option ignore '1'

config LoadPlugin
        option library 'olsrd_httpinfo.so.0.1'
        option port '1978'
        list Net '0.0.0.0 0.0.0.0'config InterfaceDefaults

config Hna4
        option netaddr '0.0.0.0'
        option netmask '0.0.0.0'

config LoadPlugin
        option library 'olsrd_jsoninfo.so.0.0'
        option ignore '0'
        option ignore '1'

config LoadPlugin
        option library 'olsrd_nameservice.so.0.3'
        option ignore '1'

config LoadPlugin
        option library 'olsrd_txtinfo.so.0.1'
        option accept '0.0.0.0'
        option ignore '0'

config Interface
        option interface 'ttn'
        option Mode 'ether'
        option ignore '0'
        option Ip4Broadcast '172.31.3.255'
```
