# Lab Instructions — Paso a paso para los labs

Este archivo reúne instrucciones exportables y ordenadas para ejecutar los labs del directorio `examples/` en Packet Tracer o GNS3. Cada lab incluye: topología ASCII, conexiones físicas sugeridas, orden de comandos a aplicar y verificaciones clave.

Tabla de contenidos

1. Lab 01 — Switch básico (VLANs, Port-Security, Voice VLAN)
2. Lab 02 — Inter-VLAN Routing (Router-on-a-stick)
3. Lab 03 — NAT + DHCP (PAT)
4. Lab 04 — OSPF Área 0 (3 routers)
5. Lab 05 — ACLs y seguridad (bloqueo HTTP)
6. Lab 06 — Recuperación y reseteo (routers/switches)
7. Lab 07 — Edge: NAT, ACLs y hardening
8. Lab 08 — Switching escalable: VLANs, STP, EtherChannel
9. Lab 09 — OSPF + NAT + Redistribución

---

## Lab 01 — Switch básico (VLANs, Port-Security, Voice VLAN)

Topología (ASCII):

```
      [PC1]    [Phone1]
        |         |
   G1/0/5 |  G1/0/10|
     +---+---------+---+
     |      S1 (Switch) |
     +------------------+
```

Conexiones en Packet Tracer/GNS3:
- PC1 -> S1 Gi1/0/5
- Phone1 -> S1 Gi1/0/10

Orden de configuración (S1):
1. `enable` → `configure terminal`
2. Crear VLANs: `vlan 10` → `name Users`; `vlan 150` → `name Voice`
3. Configurar puertos de acceso:
   - `interface range GigabitEthernet1/0/5 - 8`
   - `switchport mode access`
   - `switchport access vlan 10`
   - `switchport port-security`
   - `switchport port-security maximum 1`
   - `switchport port-security violation restrict`
   - `switchport port-security mac-address sticky`
4. Configurar puerto con teléfono:
   - `interface GigabitEthernet1/0/10`
   - `switchport mode access`
   - `switchport access vlan 10`
   - `switchport voice vlan 150`
   - `mls qos trust cos`
5. Guardar: `copy running-config startup-config`

Verificaciones clave:
- `show vlan brief`
- `show port-security interface GigabitEthernet1/0/5`
- `show mac address-table`

---

## Lab 02 — Inter-VLAN Routing (Router-on-a-stick)

Topología (ASCII):

```
   [PC VLAN10]     [Server VLAN20]
       |               |
     G1/0/5         G1/0/20
        \             /
         \           /
          S1 (trunk to R1)
              | G1/0/1 (trunk)
              |
            R1 Gi0/0 (subinterfaces .10/.20)
```

Conexiones:
- PC VLAN10 -> S1 Gi1/0/5
- Server VLAN20 -> S1 Gi1/0/20
- S1 Gi1/0/1 -> R1 Gi0/0 (trunk)

Orden de configuración:
1. En S1: crear VLANs (`vlan 10`, `vlan 20`) y poner puertos como `switchport access vlan` según corresponda.
2. En S1: configurar Gi1/0/1 como `switchport mode trunk` y `switchport trunk allowed vlan 10,20`.
3. En R1: crear subinterfaces:
   - `interface GigabitEthernet0/0.10`
   - `encapsulation dot1Q 10`
   - `ip address 192.168.10.1 255.255.255.0`
   - Repetir para `.20` con su IP.
4. Verificar: `show interfaces trunk` (S1) y `ping` entre VLANs desde hosts.

Verificaciones:
- `show ip interface brief`
- `show interfaces trunk`

---

## Lab 03 — NAT + DHCP (PAT)

Topología (ASCII):

```
  [Internet] --- ISP --- [R1 Gi0/0]
                         |
                      Gi0/1
                         |
                       [S1]
                      /   \
                   PC1   PC2
```

Conexiones:
- R1 Gi0/0 -> ISP/cloud
- R1 Gi0/1 -> S1
- Hosts -> S1

Orden de configuración (R1):
1. Configurar interfaces y `ip nat inside/outside`.
2. Configurar DHCP pool:
   - `ip dhcp excluded-address 192.168.10.1 192.168.10.10`
   - `ip dhcp pool LAN10` + `network`, `default-router`, `dns-server`.
3. Configurar NAT PAT:
   - `access-list 1 permit 192.168.10.0 0.0.0.255`
   - `ip nat inside source list 1 interface GigabitEthernet0/0 overload`
4. Guardar y verificar.

Verificaciones:
- `show ip dhcp binding`
- `show ip nat translations`
- `show ip nat statistics`

---

## Lab 04 — OSPF Área 0 (3 routers)

Topología (ASCII):
```
[R1]---10.0.12.0/30---[R2]---10.0.23.0/30---[R3]
  |192.168.1.0/24             |192.168.3.0/24
```

Conexiones:
- R1-R2 y R2-R3 enlaces punto-a-punto (/30)
- LANs conectadas a R1 y R3

Orden de configuración (cada router):
1. `router ospf 1`
2. `network` statements por interfaz (usar wildcard mask)
3. Comprobar adyacencias: `show ip ospf neighbor`

Verificaciones:
- `show ip route`
- `show ip ospf database`

---

## Lab 05 — ACLs y seguridad (bloqueo HTTP)

Topología (ASCII):
```
 [Host A - 192.168.10.10]   [Host B - 192.168.20.10]
            |                         |
           R1 (interface G0/0 with ACL applied)
```

Conexiones:
- R1 conectado a ambas redes

Orden de configuración (R1):
1. Crear ACL por nombre:
   - `ip access-list extended BLOCK_HTTP`
   - `deny tcp 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255 eq 80 log`
   - `permit ip any any`
2. Aplicar en la interfaz correcta: `interface GigabitEthernet0/0` → `ip access-group BLOCK_HTTP in`
3. Probar conexión HTTP entre hosts y verificar `show access-lists`.

Verificaciones:
- `show access-lists`
- `show ip interface GigabitEthernet0/0`

---

## Lab 06 — Recuperación y reseteo (routers/switches)

Topología (ASCII) - concepto:

```
 Console connections:
  [PC-Console] ---console--- [Router/Switch]
```

Conexiones:
- Consola desde PC a dispositivo (simulador o real)

Orden de pasos (resumen):
1. Router: durante arranque presionar `Ctrl+Break` → `confreg 0x2142` → `reset`.
2. Copiar `startup-config` a `running-config`.
3. Cambiar contraseñas y `config-register 0x2102`.
4. Guardar con `write memory` y `reload`.

Switch (Catalyst 2960/9200):
1. Apagar, mantener `MODE` y encender hasta que LED cambie.
2. `flash_init`, `rename flash:config.text flash:config.old`, `boot`.
3. `rename flash:config.old flash:config.text` y `copy flash:config.text system:running-config`.
4. Cambiar contraseñas y `write memory`.

Verificaciones:
- `show running-config`

---

## Lab 07 — Edge: NAT, ACLs y hardening

Topología (ASCII):

```
 [Mgmt-Net 192.0.2.0/28] --+ 
                             \ 
                              [R1 - Edge]
                             / 
 [LAN 10.10.10.0/24] -------+
                    Gi0/0: ISP
```

Conexiones y pasos:
1. Interfaces: Gi0/0 -> ISP, Gi0/1 -> LAN.
2. Configurar DHCP, NAT, luego ACLs de administración y SSH hardening.
3. Orden sugerido: interfaces → DHCP → NAT → ACLs → SSH keys → logging → pruebas.

Verificaciones:
- `show ip nat translations`
- `show access-lists`
- `show logging`

---

## Lab 08 — Switching escalable: VLANs, STP, EtherChannel

Topología (ASCII):

```
 [PCs VLAN10]      [Servers VLAN20]
     |                 |
    S1 --- Port-channel10 --- S2
     |                       |
   (access)               (access)
```

Conexiones y pasos:
1. Crear enlaces físicos entre S1 y S2 (2 cables) y configurar `channel-group 10 mode active` en ambos.
2. Crear `port-channel10` y configurarlo como trunk.
3. Configurar VLANs y ajustar STP priorities si necesitas controlar root bridge.

Verificaciones:
- `show etherchannel summary`
- `show spanning-tree vlan <id>`

---

## Lab 09 — OSPF + NAT + Redistribución

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

Conexiones y pasos:
1. Configurar NAT en R1 y verificar traducciones.
2. Configurar OSPF en R2 y R3 y asegurar que las redes de interconexión están bajo OSPF area 0.
3. En R2 aplicar `redistribute static subnets` para anunciar rutas estáticas hacia OSPF.
4. Verificar en R3 que aparecen las rutas redistributed.

Verificaciones:
- `show ip route`
- `show ip ospf database`
- `show ip nat translations`

---

# Export

Este archivo está listo para usarse como guía exportable. Puedes copiarlo, imprimirlo o convertirlo a PDF para distribuirlo en laboratorios.

---

Archivo generado: `lab_instructions.md` — ubicado en la raíz del repo.
