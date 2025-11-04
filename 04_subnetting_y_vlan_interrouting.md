# 04 - Subnetting y Router-on-a-stick (Inter-VLAN Routing)

Contenido:

- Subnetting básico y cálculo paso a paso
- VLSM (Variable Length Subnet Mask) — ejemplos
- Router-on-a-stick: configuración de subinterfaces (dot1Q)

## Subnetting: ejemplo paso a paso

Red: 192.168.10.0/24
Necesitamos: 4 subredes

- Bits prestados: 2 (porque 2^2 = 4)
- Nueva máscara: /24 + 2 = /26 → 255.255.255.192
- Hosts por subred: 2^(8-2) - 2 = 62

Subredes:

- 192.168.10.0/26 (hosts .1 - .62)
- 192.168.10.64/26 (hosts .65 - .126)
- 192.168.10.128/26 (hosts .129 - .190)
- 192.168.10.192/26 (hosts .193 - .254)

## VLSM ejemplo

Supongamos redes con estos requisitos:
- R1: 100 hosts
- R2: 50 hosts
- R3: 20 hosts

Ordenar por tamaño y asignar:

- Para 100 hosts → /25 (126 hosts) → reservar 192.168.1.0/25
- Para 50 hosts → /26 (62 hosts) → siguiente bloque 192.168.1.128/26
- Para 20 hosts → /27 (30 hosts) → siguiente bloque 192.168.1.192/27

Consejo: siempre comenzar con la subred más grande.

## Router-on-a-stick (subinterfaces dot1Q)


Configuración en un router (R1) para VLAN 10, 20:

R1(config)# `interface GigabitEthernet0/0.10`
R1(config-subif)# `encapsulation dot1Q 10`
R1(config-subif)# `ip address 192.168.10.1 255.255.255.192`

R1(config)# `interface GigabitEthernet0/0.20`
R1(config-subif)# `encapsulation dot1Q 20`
R1(config-subif)# `ip address 192.168.20.1 255.255.255.192`

R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `no shutdown`

Explicación rápida de argumentos:
- `encapsulation dot1Q <vlan-id>` — etiqueta la subinterfaz con la VLAN (802.1Q). Si la VLAN es nativa usa `encapsulation dot1Q <vlan-id> native`.


## Verificación

- `show ip interface brief`
- `show interfaces trunk` (en switch)
- `show ip route` (ver rutas conectadas a las subinterfaces)

---
