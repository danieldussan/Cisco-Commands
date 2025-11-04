# 03 - Routing estático y conceptos de routing

## Rutas estáticas

Comando:

ip route <network> <mask> <next-hop-ip | exit-interface> [administrative-distance]

Ejemplo:

R1(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1

Notas:
- `exit-interface` se usa en enlaces punto a punto; `next-hop-ip` es lo más común.

## Conceptos CCNA: Redes 1 / 2 / 3

Redes 1 (Fundamentos):

- Modelos OSI y TCP/IP
- Tipos de cables y conectores (straight-through, crossover)
- Direccionamiento IPv4 e IPv6 básico
- Subnetting y VLSM

Redes 2 (Switching y VLANs):

- VLANs, trunks (802.1Q), STP (spanning-tree) básico
- EtherChannel y configuración de puertos
- Seguridad de puertos (port-security) y gestión (SNMP, SSH)

Redes 3 (Ruteo y servicios):

- Rutas estáticas y dinámicas (OSPF, EIGRP, RIP)
- NAT, PAT, DHCP, DNS
- ACLs y políticas de seguridad
- Troubleshooting y optimización de redes

## Ejercicios recomendados por nivel

- Redes 1: Practicar subnetting y configurar una red pequeña con un switch y un router (una VLAN de usuarios y una VLAN de gestión).
- Redes 2: Configurar trunk entre dos switches, crear VLANs y probar comunicación entre ellas usando router-on-a-stick.
- Redes 3: Implementar OSPF entre 3 routers y verificar convergencia; añadir ACLs para restringir servicios.
