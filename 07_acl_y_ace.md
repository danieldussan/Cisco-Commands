# 07 - ACL y ACE

Guía práctica para crear y aplicar listas de control de acceso (ACL) en routers Cisco.

## ACL estándar

Ejemplo:

R1(config)# access-list 10 permit 192.168.1.0 0.0.0.255
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip access-group 10 in

Notas:
- ACL estándar usan sólo la dirección de origen. Para especificar rutas use wildcard mask (inversa de la máscara: p.ej. 0.0.0.255).
- Rango de números: 1-99 (estándar) y 1300-1999 (extendidos dinámicos).

Verificación:

R1# show access-lists
R1# show ip interface GigabitEthernet0/0

## ACL extendida

Ejemplo denegando HTTP entre dos subredes:

R1(config)# access-list 110 deny tcp 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255 eq 80
R1(config)# access-list 110 permit ip any any
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip access-group 110 out

Notas:
- ACL extendida permite especificar protocolos (tcp/udp/icmp), puertos y direcciones origen/destino.
- Orden importante: la primera coincidencia aplica (implicit deny al final).

## ACL por nombre (recomendada)

R1(config)# ip access-list extended BLOCK_HTTP
R1(config-ext-nacl)# deny tcp 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255 eq 80
R1(config-ext-nacl)# permit ip any any

Aplicar:

R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip access-group BLOCK_HTTP in

## Ejercicios

1. Crea una ACL que permita sólo tráfico SSH (tcp 22) desde la red 192.168.5.0/24 hacia el router.
2. Crea una ACL que bloqueé todo el tráfico entre VLAN10 y VLAN20 excepto DNS y ICMP.
