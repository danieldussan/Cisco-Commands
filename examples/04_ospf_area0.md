# Example 04 - OSPF Área 0 (3 routers)

Topología: R1 -- R2 -- R3 en línea, cada enlace con subred /30 y redes LAN en cada router.

Objetivos:
- Configurar OSPF en área 0
- Establecer adyacencias y verificar tablas

Configuración (R1):

R1(config)# `router ospf 1`
R1(config-router)# `network 10.0.12.0 0.0.0.3 area 0`
R1(config-router)# `network 192.168.1.0 0.0.0.255 area 0`

Configuración (R2):

R2(config)# `router ospf 1`
R2(config-router)# `network 10.0.12.0 0.0.0.3 area 0`
R2(config-router)# `network 10.0.23.0 0.0.0.3 area 0`

Configuración (R3):

R3(config)# `router ospf 1`
R3(config-router)# `network 10.0.23.0 0.0.0.3 area 0`
R3(config-router)# `network 192.168.3.0 0.0.0.255 area 0`

Verificación:

R1# `show ip ospf neighbor`
R1# `show ip route`
R2# `show ip ospf database`

Topología (ASCII):

```
[R1]---10.0.12.0/30---[R2]---10.0.23.0/30---[R3]
	|192.168.1.0/24             |192.168.3.0/24
```

Lab steps (Packet Tracer / GNS3):
1. Conectar R1-R2 y R2-R3 con enlaces punto-a-punto (/30).
2. Configurar OSPF en cada router con `router ospf 1` y los `network` correspondientes.
3. Verificar adyacencias con `show ip ospf neighbor` y rutas con `show ip route`.
