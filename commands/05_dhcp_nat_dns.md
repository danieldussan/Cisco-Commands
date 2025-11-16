# 05 - DHCP, NAT y DNS

## DHCP


Exclusión de direcciones:

R1(config)# `ip dhcp excluded-address 192.168.10.1 192.168.10.10`

Pool DHCP:

R1(config)# `ip dhcp pool LAN10`
R1(dhcp-config)# `network 192.168.10.0 255.255.255.0`
R1(dhcp-config)# `default-router 192.168.10.1`
R1(dhcp-config)# `dns-server 8.8.8.8`

Explicación de argumentos:
- `network <ip> <mask>` — define el rango a asignar.
- `default-router` — puerta de enlace por defecto que recibe el cliente.

## NAT dinámico


R1# `ip nat pool MiPool 200.10.10.1 200.10.10.10 netmask 255.255.255.0`
R1# `access-list 1 permit 192.168.10.0 0.0.0.255`
R1# `ip nat inside source list 1 pool MiPool`

Explicación:
- `ip nat pool <name> <start> <end> netmask <mask>` — rango de IP públicas para NAT.
- `ip nat inside source list <acl> pool <name>` — mapea direcciones privadas a pool.

## PAT (NAT Overload)


R1(config)# `ip nat inside source list 1 interface GigabitEthernet0/0 overload`

Explicación:
- `overload` — permite utilizar una única IP pública (la de la interfaz) para múltiples privados usando puertos.

## DNS básico en IOS


R1(config)# `ip name-server 8.8.8.8`

Nota: routers pueden resolver nombres para ciertos servicios; para servidores DNS completos, usar servidores dedicados.

## Verificación


R1# `show ip nat translations`
R1# `show ip nat statistics`
R1# `show ip dhcp binding`

---

Nota práctica:

- No olvidar marcar las interfaces de acuerdo a su rol en NAT:
	- `interface GigabitEthernet0/0`
		- `ip nat inside` (en la interfaz hacia la red privada)
	- `interface GigabitEthernet0/1`
		- `ip nat outside` (en la interfaz hacia Internet)
