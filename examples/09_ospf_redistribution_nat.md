# Example 09 - OSPF + NAT + Redistribución

Topología: R1 (edge/NAT) conectado a Internet y a R2 (core OSPF). R2 redistribuye rutas al R3.

Objetivos:
- NAT/PAT en edge
- OSPF en área 0 entre R2 y R3
- Redistribución de rutas estáticas en OSPF

Configuración (R1 - edge):

R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `ip address 203.0.113.5 255.255.255.248`
R1(config-if)# `ip nat outside`
R1(config)# `interface GigabitEthernet0/1`
R1(config-if)# `ip address 10.20.10.1 255.255.255.0`
R1(config-if)# `ip nat inside`
R1(config-if)# `no shutdown`

! NAT
R1(config)# `access-list 1 permit 10.20.10.0 0.0.0.255`
R1(config)# `ip nat inside source list 1 interface GigabitEthernet0/0 overload`

! Ruta estática para redes internas anunciadas por OSPF en R2
R1(config)# `ip route 192.168.50.0 255.255.255.0 10.20.10.2`

Configuración (R2 - OSPF core):

R2(config)# `router ospf 1`
R2(config-router)# `network 10.20.10.0 0.0.0.255 area 0`
R2(config-router)# `network 10.20.20.0 0.0.0.255 area 0`

! redistribuir rutas estáticas en OSPF
R2(config-router)# `redistribute static subnets`

Configuración (R3):

R3(config)# `router ospf 1`
R3(config-router)# `network 10.20.20.0 0.0.0.255 area 0`

Verificación:

R2# `show ip route`
R2# `show ip ospf database`
R1# `show ip nat translations`

Notas:
- Configurar métricas adecuadas al redistribuir para evitar rutas subóptimas.
- Monitorizar `show ip protocols` y `show ip route` después de aplicar cambios.

Topología (ASCII):

```
 [Internet]
	 |
	R1 (edge/NAT)
	 |
	R2 (OSPF Core)
	 |
	R3
```

Lab steps (Packet Tracer / GNS3):
1. Configurar NAT en R1 y verificar `show ip nat translations`.
2. Configurar OSPF en R2 y R3, incluir la red de interconexión con R1.
3. En R2, aplicar `redistribute static subnets` para anunciar rutas estáticas aprendidas de R1.
4. Verificar en R3 que las rutas redistribuidas aparecen en `show ip route`.
