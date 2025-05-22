# SNMP & Nagios
Vamos a configurar SNMP y, una vez configurado, utilizaremos una herramienta de verdad para ver como se usa "en el mundo real"

Podéis revisar toda la información sobre [Nagio Core](https://www.nagios.org/)

## Step By Step

0. Revisamos las IPs de los equipos y confirmamos que tenemos ping:
```sh
ip addr
```
o
```sh
ifconfig
```

1. Instalamos snmpd en la máquina AGENT
```sh
sudo apt update
sudo apt install snmpd -y
```

2. Configuramos el AGENT para permitir conexiones externas y añadimos un mail (asi lo leemos luego):
```sh
sudo nano /etc/snmp/snmpd.conf
```
```sh
agentAddress    udp:161
rocommunity     public
sysContact      (vuestro-email)@alumnes.udl.cat
```
```sh
sudo systemctl restart snmpd
sudo systemctl enable snmpd
```


3. Instalamos SNMP en el MANAGER
```sh
sudo apt update
sudo apt install snmp snmp-mibs-downloader -y
```

4. Probamos la conectividad SNMP desde el MANAGER:
```sh
snmpwalk -v2c -c public <IP_AGENT> system
```
- Si habéis llegado hasta aquí, intentad:
* ¿Cómo podéis recuperar la carga de CPU del equipo "AGENT" desde el MANAGER a 1 minuto?
* ¿Cómo podéis recuperar la carga de CPU del equipo "AGENT" desde el MANAGER a 1, 5 y 10 miunutos solo con un comando?


5. Instalamos NAGIOS
```sh
sudo apt install nagios4 nagios-plugins-contrib nagios-snmp-plugins
```

6. Abrimos la web de NAGIOS desde un navegador de nuestro PC
Para esto es importante que el PC que estamos usando (la `máquina host`) esté en la misma red que los otros equipos.

La ruta, debería ser algo del estilo: `http://{IP_MANAGER}/nagios`


6. Configuramos el AGENT desde el MANAGER:
```sh
sudo nano /etc/nagios4/conf.d/agent.cfg
```
```
define host {
    use             generic-host
    host_name       ubuntu-agent
    alias           Ubuntu Agent VM
    address         <IP_AGENT>
}
```

7. Configuramos servicios en NAGIOS



## Posibles problemas
- **Puede que haya el firewall de la UDL.**
Opcion 1: Añadimos y configuramos un adaptador con red interna entre las VMs.
Opcion 2 (Hackathon experience): Conectais vuestro PC al WiFi del movil.

- **Puede que haya el firewall del mismo SO:**
```sh
sudo ufw allow from <IP_MANAGER> to any port 161 proto udp
```

