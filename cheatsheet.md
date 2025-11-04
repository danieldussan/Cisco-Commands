# Cheatsheet / Comandos rápidos

Referencias rápidas de comandos Cisco más usados.

-- Accesos
enable — entrar a modo privilegiado
configure terminal — entrar a configuración global

-- Interfaces
interface <type/num>
shutdown / no shutdown
description <text>

-- VLAN y trunking
vlan <id>
switchport mode access
switchport access vlan <id>
switchport mode trunk
switchport trunk allowed vlan <list>
switchport trunk native vlan <id>

-- Enrutamiento
ip route <network> <mask> <next-hop>
show ip route

-- DHCP / NAT
ip dhcp pool <name>
ip nat inside source list <acl> interface <if> overload

-- Seguridad
service password-encryption
username <user> secret <pass>
line vty 0 15
 transport input ssh

-- Verificaciones utiles
show running-config
show ip interface brief
show interfaces trunk
show access-lists
show ip nat translations
