# 09 - IPv6: configuración y verificación

## Habilitar enrutamiento IPv6


R1(config)# `ipv6 unicast-routing`

## Asignar direcciones a interfaces


R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `ipv6 address 2001:db8:acad:1::1/64`
R1(config-if)# `no shutdown`

## SLAAC y DHCPv6


- Para soporte SLAAC, las interfaces anuncian prefixos si están configuradas correctamente.
- Para DHCPv6 usar un servidor DHCPv6 externo o configurar `ipv6 dhcp pool` en IOS.

## Verificación

R1# `show ipv6 interface brief`
R1# `show ipv6 route`
R1# `show ipv6 neighbors`

---
