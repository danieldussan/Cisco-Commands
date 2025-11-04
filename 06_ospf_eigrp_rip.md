# 06 - Routing dinámico: OSPF, EIGRP y RIP

Este archivo contiene ejemplos de configuración y comandos de verificación básicos para OSPF, EIGRP y RIP.

## OSPF

Configuración básica (área 0):

R1(config)# router ospf 1
R1(config-router)# network 192.168.1.0 0.0.0.255 area 0
R1(config-router)# passive-interface GigabitEthernet0/0

Notas:
- `router ospf <process-id>` — identifica el proceso OSPF local (número local, no sincroniza entre routers).
- `network <ip> <wildcard> area <area-id>` — anuncia interfaces que coincidan con la máscara wildcard; wildcard es la inversa de la máscara.

Verificación:

R1# show ip protocols
R1# show ip ospf neighbor
R1# show ip ospf interface

## RIP v2

R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 192.168.1.0
R1(config-router)# no auto-summary

Notas:
- `version 2` — habilita RIP v2 (soporta máscaras y multicasting).
- `no auto-summary` — evita sumarización automática en fronteras de clase.

Verificación:

R1# show ip rip database
R1# show ip route rip

## EIGRP (classic)

R1(config)# router eigrp 10
R1(config-router)# network 192.168.1.0 0.0.0.255
R1(config-router)# no auto-summary

Notas:
- `router eigrp <asn>` — AS local para EIGRP (debe coincidir con vecinos para establecer adyacencia en classic EIGRP).
- Para EIGRP named (IOS más nuevo) el comando cambia a `router eigrp NAME` luego `address-family ipv4 unicast`.

Verificación:

R1# show ip eigrp neighbors
R1# show ip eigrp topology
R1# show ip protocols

## Consejos de diseño y CCNA

- OSPF es link-state y escala mejor en redes jerárquicas; divide en áreas para reducir overhead.
- EIGRP (propietario Cisco) es eficiente en convergencia y simplicidad de configuración en infraestructuras Cisco puras.
- RIP es más simple y educativo, pero limitado para redes grandes (salvo en laboratorios).
