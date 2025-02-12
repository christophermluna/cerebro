zmap para escanear en internet, mucho más rápido

https://github.com/brandonskerritt/RustScan
Para detectar rápidamente que puertos están abiertos y luego ya usar nmap para escanear esos puertos.

https://github.com/Y000o/Nmap
  cursillo con ejemplos de uso
http://www.cyberciti.biz/networking/nmap-command-examples-tutorials/

nmap -p- --min-rate 10000 -oA nmap-alltcp 1.2.3.4
nmap -p 22,80,1337 -sC -sV -oA nmap-tcpscripts 1.2.3.4
  elegimos solo los puertos de los que queremos obtener más info


Escaner de red.

# Sondeos
-sS
  SYN

-sT
  tcp connect

-sP
  ping

-sV
  obtener servicios y sus versiones
  Si devuelve tcpwrapped es que puede haber algún tipo de software protegiendo el puerto
  https://security.stackexchange.com/questions/23407/how-to-bypass-tcpwrapped-with-nmap-scan


Escanear todos los puertos de una IP, enviando 10k pks/s y salvando el resultado en el fichero
nmap -p- --min-rate 10000 -oA scans/nmap-alltcp 10.10.10.147

Escaneo de una red para ver que ips contestan
nmap -sP x.x.x.x/a

Escaneo de red descubriendo SO
nmap -sS -O 192.168.1.1/24

Escaneo de una red en busca de un puerto ssh abierto:
nmap -p 22 192.168.1.1/24

Escanear una red:
nmap 192.168.55.*

Escanear tomando las ips/redes de un fichero de texto
nmap -iL fichero...

Output en formato normal:
nmap -oN fichero.txt ...



# Escaneo de puertos

Todos los puertos
nmap -p- 1.2.3.4


## TCP
nmap 127.0.0.1 -p 80

Envia un paquete SYN al puerto.
Si el server, o u firewall en medio, rechaza el paquete:
  ICMP - Destination unreachable (Port unreachable)
nmap no devuelve:
  STATE: filtered

iptables devuelve este mensaje (si está configurado como REJECT).
Otros firewall pueden devolver:
  ICMP - Destination unreachable  (Host administratively prohibited)

Si iptables está configurado como DROP contesta igual:
  STATE: filtered

En caso de estar cerrado el SO contesta con un TCP [RST,ACK] y es cunado nmap contesta
  STATE: closed

Si NMAP nos dice "Note: Host seems down. If it is really up, but blocking our ping probes" probar con
-Pn

## UDP
Parece que no es fácil comprobar que un puerto UDP está abierto.


# Maquinas lentas
nmap --max-retries 5 --min-rtt-timeout 10 10.0.1.1
  metemos más retries y mas tiempo de espera
  no lo he probado

# Timing parameters
https://www.hackingarticles.in/nmap-scan-with-timing-parameters/

# SNMP
nmap -P0 -v -sU -p 161 10.0.2.5

# XML
Salida en XML
nmap -oX fichero.xml ...


# Vulnerabilidades
nmap -Pn -sV --script vuln 127.0.0.1
  -Pn: escanear el host aunque no conteste a ping
  -sV: mostrar versiones del software de los puertos abiertos


# Python
python-nmap

import nmap
nm = nmap.PortScanner()
nm.scan('10.0.0.0/16', ports="80,443", arguments="-sV")
for host_addr in nm.all_hosts():
  try:
    host = nm[host_addr]
    if host.tcp(80).get("state") == "open" and host.tcp(80).get("product") == "nginx":
      print("nginx")

    for port in host.all_tcp():
      if host.tcp(port).get("name") == "ssh":
        print("ssh")
  except Exception as ex:
    print(f"Exception host {host_addr}: {ex}")



# Plugins
https://nmap.org/book/man-nse.html

Categorías:
auth, broadcast, default. discovery, dos, exploit, external, fuzzer, intrusive, malware, safe, version, vuln


Viene con muchos instalados por defecto:
/usr/share/nmap/scripts/

## Plugins por defecto

Buscar subdominios de uso común:
nmap -p 80 --script dns-brute.nse foo.bar

Buscar otros dominios con la misma ip:
nmap -p 80 --script dns-brute.nse subastas.es

Traceroute + geolocation
sudo nmap --traceroute --script traceroute-geolocation.nse -p 80 hackertarget.com


## NSE
https://www.ncsc.gov.uk/blog-post/introducing-scanning-made-easy
Help system owners and administrators find systems with specific vulnerabilities.

https://github.com/nccgroup/nmap-nse-vulnerability-scripts
Por ahora, dic 2022, solo tienen un script.
