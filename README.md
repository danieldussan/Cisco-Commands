# Cisco Commands — Organized Reference

Repositorio con comandos y ejemplos para aprender y practicar administración de redes Cisco (enfoque CCNA).

## Tabla de Contenidos

1. [Comandos básicos](#01---comandos-básicos)
2. [VLANs y Trunking](#02---vlans-y-trunking-detallado)
3. [Routing estático y conceptos de routing](#03---routing-estático-y-conceptos-de-routing)
4. [Subnetting y Router-on-a-stick](#04---subnetting-y-router-on-a-stick-inter-vlan-routing)
5. [DHCP, NAT y DNS](#05---dhcp-nat-y-dns)
6. [Routing dinámico: OSPF, EIGRP y RIP](#06---routing-dinámico-ospf-eigrp-y-rip)
7. [ACL y ACE](#07---acl-y-ace)
8. [Seguridad básica](#08---seguridad-básica)
9. [IPv6: configuración y verificación](#09---ipv6-configuración-y-verificación)
10. [Comandos útiles y troubleshooting](#10---comandos-útiles-y-troubleshooting)

---

# 01 - Comandos básicos

Este archivo reúne los comandos más comunes en routers y switches Cisco, con explicaciones breves de los argumentos largos para que sea rápido y práctico.

## Accesos y modos

- Descripción: pasar a modo privilegiado (exec).
- `enable`
  - Descripción: volver al modo anterior.

- `exit`
  - Descripción: entrar a modo de configuración global.

- `configure terminal`

## Visualización de configuraciones

- Muestra la configuración activa en RAM.
- `show running-config`
  - Muestra la configuración almacenada en NVRAM (arranque).

- `show startup-config`
  - Argumentos: `interface-id` (opcional) — p.ej. `GigabitEthernet0/1`.
  - Muestra estadísticas y estado de la interfaz.

- `show interfaces [interface-id]`
  - Muestra resumen de interfaces con IP y estado.

- `show ip interface brief`
  - Muestra la tabla de enrutamiento (rutas conectadas, estáticas y aprendidas).

- `show ip route`
  - Muestra lista de VLANs y sus puertos.

- `show vlan brief`

## Configuración de interfaz

- Ejemplo: `interface GigabitEthernet0/1`.
- Entra al modo de configuración de la interfaz.
- `interface TYPE/NUMBER`
  - Apagar o encender la interfaz.

- `shutdown` / `no shutdown`
  - Agrega una descripción a la interfaz.

- `description TEXT`

## VLANs y trunking (resumen rápido)

- Crea o edita una VLAN.
- `vlan <id>`
  - Configura el puerto como acceso (un solo VLAN).

- `switchport mode access`
  - Asigna la VLAN de acceso al puerto.

- `switchport access vlan <vlan-id>`
  - Pone el puerto en modo troncal (lleva múltiples VLANs).

- `switchport mode trunk`
  - VLAN nativa para troncales (sin tag dot1q).

- `switchport trunk native vlan <vlan-id>`

## Enrutamiento y rutas

- Ejemplo: `ip route 0.0.0.0 0.0.0.0 192.168.1.1`
- Crea una ruta estática. `distance` es opcional (administrative distance).
- `ip route <dest> <mask> <next-hop-ip|exit-intf> [distance]`

## DHCP en routers

- Excluye direcciones del pool DHCP.
- `ip dhcp excluded-address <start> <end>`
  - Crea un pool DHCP. Opciones dentro del modo: `network`, `default-router`, `dns-server`.

- `ip dhcp pool <name>`

## NAT y PAT (resumen)

- NAT dinámico desde una lista de direcciones hacia un pool público.
- `ip nat inside source list <acl> pool <name>`
  - PAT (NAT sobrecargado) — mapea múltiples privados a una sola IP pública (interfaz).

- `ip nat inside source list <acl> interface <if> overload`

Nota: no olvidar marcar las interfaces con `ip nat inside` y `ip nat outside` según corresponda.

## ACLs (resumen)

- ACL estándar. `num` 1-99 o 1300-1999.
- `access-list <num> permit|deny <src> <wildcard>`
  - ACL extendida para protocolos y puertos.

- `access-list <num> deny|permit tcp <src> <wildcard> <dst> <wildcard> eq <port>`

## Guardar y reiniciar

- Guarda la configuración activa en NVRAM.
- `copy running-config startup-config`
  - Reinicia el dispositivo.

- `reload`

## SSH y acceso seguro (resumen)

- Define nombre de dominio para generar claves RSA.
- `ip domain-name <name>`
  - Genera claves RSA. (Algunas versiones requieren `modulus 1024` o `2048`).

- `crypto key generate rsa`
  - Crea usuario local con contraseña encriptada.

- `username <user> secret <password>`
  - Configura líneas vty para Telnet/SSH. Comandos comunes dentro:
  - `transport input ssh` — permitir solo SSH.
  - `login local` — usar base de usuarios local.

- `line vty 0 15`

## Recuperación de contraseña (resumen)

Pasos rápidos:

1. Reiniciar y entrar en ROMMON (Ctrl+Break).
2. `confreg 0x2142` — ignorar startup-config.
3. `reset` o `reload`.
4. `copy startup-config running-config`.
5. Cambiar contraseñas y `config-register 0x2102`.
6. `write memory` y `reload`.

## EtherChannel (resumen)

- Selecciona rango de interfaces.
- `interface range FastEthernet0/1 - 2`
  - Agrupa interfaces en EtherChannel (LACP - activo).

- `channel-group 1 mode active`
  - Configura la interfaz lógica del bundle.

- `interface port-channel 1`

## Cheatsheet rápido

- `show interfaces`
- `show ip route`
- `show vlan brief`
- `copy run start`

---

Notas rápidas adicionales:

- `show running-config | include <string>` — buscar líneas que contengan `<string>` en la running-config.
- `show logging` — ver mensajes del syslog en el dispositivo.

---

# 02 - VLANs y Trunking (detallado)

Contenido:

- Conceptos: access vs trunk, native VLAN, DTP
- Configuración de VLANs y puerto de acceso
- Configuración de trunk 802.1Q (estática y dinámica)
- Voice VLAN (configuración en switch)
- Troubleshooting de trunks

## Conceptos rápidos

- VLAN access: puerto que pertenece a una sola VLAN (p.ej. usuarios).
- VLAN trunk: enlace que transporta múltiples VLANs (usa tagging 802.1Q).
- Native VLAN: VLAN que viaja sin tag en enlaces 802.1Q (por defecto VLAN 1).
- DTP (Dynamic Trunking Protocol): protocolo Cisco que negocia automáticamente trunking entre switches. Puede causar seguridad si no se controla.

## Crear VLAN y asignar puertos

Crear VLAN:

S1(config)# `vlan 10`
S1(config-vlan)# `name Users`

Asignar puerto a VLAN (modo access):

S1(config)# `interface GigabitEthernet1/0/5`
S1(config-if)# `switchport mode access`
S1(config-if)# `switchport access vlan 10`

Explicación breve:

- `switchport mode access` fuerza el puerto a modo acceso.
- `switchport access vlan <id>` asigna la VLAN.

## Configurar trunk 802.1Q

Configurar trunk estático (recomendado cuando se conoce el otro extremo):

S1(config)# `interface GigabitEthernet1/0/1`
S1(config-if)# `switchport mode trunk`
S1(config-if)# `switchport trunk encapsulation dot1q` # (en plataformas que soportan ambos)
S1(config-if)# `switchport trunk native vlan 99`

Permitir VLANs específicas en el trunk:

S1(config-if)# `switchport trunk allowed vlan 10,20,99`

Notas:

- `switchport trunk native vlan <id>` — define qué VLAN se envía sin tag. Evitar usar VLAN 1 como nativa por seguridad.

## DTP (Negotiation)

- Intenta negociar un trunk (LHS inicia).
- `switchport mode dynamic desirable`
  - Responde a negociaciones.

- `switchport mode dynamic auto`
  - Para seguridad: desactiva DTP en puertos trunk estáticos.

- `switchport nonegotiate`

Ejemplo seguro (trunk estático, sin DTP):

S1(config)# `interface GigabitEthernet1/0/1`
S1(config-if)# `switchport mode trunk`
S1(config-if)# `switchport nonegotiate`

## Voice VLAN

Cuando conectas teléfonos IP, normalmente quieres separar la voz y datos:

S1(config)# `interface GigabitEthernet1/0/10`
S1(config-if)# `switchport mode access`
S1(config-if)# `switchport access vlan 10` # VLAN datos
S1(config-if)# `switchport voice vlan 150` # VLAN voz
S1(config-if)# `mls qos trust cos` # confiar en el COS del teléfono

Explicación:

- `switchport voice vlan <id>` — etiqueta el tráfico de voz y permite que el teléfono envíe datos de voz en la VLAN de voz.

## Troubleshooting de trunks

- Muestra puertos trunk, VLANs permitidas y native VLAN.
- `show interfaces trunk`
  - Confirma qué puertos están asignados a cada VLAN.

- `show vlan brief`
  - Muestra modo del puerto (access/trunk), VLAN nativa y configuración DTP.

- `show interfaces GigabitEthernet1/0/1 switchport`
  - Verifica la conexión entre switches y la interfaz remota.

- `show cdp neighbors detail`

Problemas comunes y soluciones:

- Problema: Trunk no se forma.
- Causa probable: mismatch de encapsulation (ISL vs dot1Q) o DTP deshabilitado en un extremo.
- Solución: Asegurar `switchport trunk encapsulation dot1q` (si aplica) y `switchport mode trunk` en ambos extremos, o usar `nonegotiate` si es estático.

- Problema: VLAN no pasa por el trunk.
- Causa probable: `switchport trunk allowed vlan` restringe la VLAN.
- Solución: Agregar la VLAN a la lista permitida: `switchport trunk allowed vlan add <vlan-id>`.

- Problema: Problemas con VLAN nativa (mismatch de native VLAN)
- Causa: native VLAN diferente entre extremos → tráfico no etiquetado va a otra VLAN.
- Solución: Alinear `switchport trunk native vlan <id>` en ambos extremos o evitar native VLAN poniendo todas las VLAN con tags y usando una VLAN no usada para native.

## Buenas prácticas

- No usar VLAN 1 para management ni native.
- Poner trunks en modo estático cuando sea posible y deshabilitar DTP: `switchport nonegotiate`.
- Filtrar VLANs permitidas en trunks para reducir el blast domain: `switchport trunk allowed vlan 10,20,150`.
- Habilitar Port-Security en puertos de acceso.

---

# 03 - Routing estático y conceptos de routing

## Rutas estáticas

Comando:

`ip route <network> <mask> <next-hop-ip | exit-interface> [administrative-distance]`

Ejemplo:

R1(config)# `ip route 0.0.0.0 0.0.0.0 203.0.113.1`

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

---

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

- Muestra resumen de interfaces con IP y estado.
- `show ip interface brief`
  - Muestra puertos trunk, VLANs permitidas y native VLAN (en switch).

- `show interfaces trunk`
  - Muestra la tabla de enrutamiento (ver rutas conectadas a las subinterfaces).

- `show ip route`

---

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

- Muestra traducciones NAT activas.
  R1# `show ip nat translations`

- Muestra estadísticas de NAT.
  R1# `show ip nat statistics`

- Muestra asignaciones DHCP activas.
  R1# `show ip dhcp binding`

---

Nota práctica:

- No olvidar marcar las interfaces de acuerdo a su rol en NAT:
  - `interface GigabitEthernet0/0`
    - `ip nat inside` (en la interfaz hacia la red privada)
  - `interface GigabitEthernet0/1`
    - `ip nat outside` (en la interfaz hacia Internet)

---

# 06 - Routing dinámico: OSPF, EIGRP y RIP

Este archivo contiene ejemplos de configuración y comandos de verificación básicos para OSPF, EIGRP y RIP.

## OSPF

Configuración básica (área 0):

R1(config)# `router ospf 1`
R1(config-router)# `network 192.168.1.0 0.0.0.255 area 0`
R1(config-router)# `passive-interface GigabitEthernet0/0`

Notas:

- `router ospf <process-id>` — identifica el proceso OSPF local (número local, no sincroniza entre routers).
- `network <ip> <wildcard> area <area-id>` — anuncia interfaces que coincidan con la máscara wildcard; wildcard es la inversa de la máscara.

Verificación:

- Muestra protocolos de routing configurados.
  R1# `show ip protocols`

- Muestra vecinos OSPF.
  R1# `show ip ospf neighbor`

- Muestra información de interfaces OSPF.
  R1# `show ip ospf interface`

## RIP v2

R1(config)# `router rip`
R1(config-router)# `version 2`
R1(config-router)# `network 192.168.1.0`
R1(config-router)# `no auto-summary`

Notas:

- `version 2` — habilita RIP v2 (soporta máscaras y multicasting).
- `no auto-summary` — evita sumarización automática en fronteras de clase.

Verificación:

- Muestra base de datos RIP.
  R1# `show ip rip database`

- Muestra solo rutas aprendidas por RIP.
  R1# `show ip route rip`

## EIGRP (classic)

R1(config)# `router eigrp 10`
R1(config-router)# `network 192.168.1.0 0.0.0.255`
R1(config-router)# `no auto-summary`

Notas:

- `router eigrp <asn>` — AS local para EIGRP (debe coincidir con vecinos para establecer adyacencia en classic EIGRP).
- Para EIGRP named (IOS más nuevo) el comando cambia a `router eigrp NAME` luego `address-family ipv4 unicast`.

Verificación:

- Muestra vecinos EIGRP.
  R1# `show ip eigrp neighbors`

- Muestra topología EIGRP.
  R1# `show ip eigrp topology`

- Muestra protocolos de routing configurados.
  R1# `show ip protocols`

## Consejos de diseño y CCNA

- OSPF es link-state y escala mejor en redes jerárquicas; divide en áreas para reducir overhead.
- EIGRP (propietario Cisco) es eficiente en convergencia y simplicidad de configuración en infraestructuras Cisco puras.
- RIP es más simple y educativo, pero limitado para redes grandes (salvo en laboratorios).

---

# 07 - ACL y ACE

Guía práctica para crear y aplicar listas de control de acceso (ACL) en routers Cisco.

## ACL estándar

Ejemplo:

- R1(config)# `access-list 10 permit 192.168.1.0 0.0.0.255`
- R1(config)# `interface GigabitEthernet0/0`
- R1(config-if)# `ip access-group 10 in`

Notas:

- ACL estándar usan sólo la dirección de origen. Para especificar rutas use wildcard mask (inversa de la máscara: p.ej. 0.0.0.255).
- Rango de números: 1-99 (estándar) y 1300-1999 (extendidos dinámicos).

Verificación:

- Muestra todas las ACLs configuradas.
  R1# `show access-lists`

- Muestra información de la interfaz incluyendo ACLs aplicadas.
  R1# `show ip interface GigabitEthernet0/0`

## ACL extendida

Ejemplo denegando HTTP entre dos subredes:

R1(config)# `access-list 110 deny tcp 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255 eq 80`
R1(config)# `access-list 110 permit ip any any`
R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `ip access-group 110 out`

Notas:
Notas:

- ACL extendida permite especificar protocolos (tcp/udp/icmp), puertos y direcciones origen/destino.
- Orden importante: la primera coincidencia aplica (al final existe una denegación implícita - "implicit deny").

## ACL por nombre (recomendada)

R1(config)# `ip access-list extended BLOCK_HTTP`
R1(config-ext-nacl)# `deny tcp 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255 eq 80`
R1(config-ext-nacl)# `permit ip any any`

Aplicar:

R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `ip access-group BLOCK_HTTP in`

---

# 08 - Seguridad básica

Sección con comandos para asegurar equipos de red a nivel básico (switches y routers).

## Port-Security (switch)

S1(config)# `interface FastEthernet0/1`
S1(config-if)# `switchport mode access`
S1(config-if)# `switchport port-security`
S1(config-if)# `switchport port-security maximum 1`
S1(config-if)# `switchport port-security violation shutdown`
S1(config-if)# `switchport port-security mac-address sticky`

Explicación rápida:

- Limita a 1 MAC por puerto.
- `switchport port-security maximum 1`
  - Acción al detectar violación: shut the port.

- `switchport port-security violation shutdown`
  - Aprende y guarda la MAC en la running-config.

- `switchport port-security mac-address sticky`

## Desactivar servicios inseguros

R1(config)# `no cdp run`
R1(config)# `no ip http server`

## Protección de accesos y bloqueo de intentos

R1(config)# `login block-for 60 attempts 3 within 30`

Explicación:

- Bloquea intentos de login por 60 segundos si hay 3 intentos fallidos en 30 segundos.
- `login block-for 60 attempts 3 within 30`

## SSH (seguro)

R1(config)# `ip domain-name example.com`
R1(config)# `crypto key generate rsa modulus 2048`
R1(config)# `username admin secret MiPasswordSeguro`
R1(config)# `line vty 0 15`
R1(config-line)# `transport input ssh`
R1(config-line)# `login local`

Recomendación:

- Forzar SSHv2: `R1(config)# ip ssh version 2`

## SNMP (recomendación)

- Evitar usar SNMPv1/2c en producción; usar SNMPv3 con autenticación y cifrado.

## Backups y auditoría

- Guardar configuraciones frecuentemente: `copy running-config startup-config`.
- Automatizar backups con logs o scripts (externos).

Buenas prácticas adicionales:

- Algoritmo más seguro y encriptado. Usar en lugar de `enable password`.
- `enable secret <clave>`
  - Ofusca contraseñas de texto claro en la running-config.

- `service password-encryption`
  - Almacena logs en memoria para revisión (tamaño del buffer en bytes).

- `logging buffered 4096`
  - Envía logs a servidor remoto.

- `logging <ip-del-servidor-syslog>`
  - Habilita modelo AAA para centralizar autenticación/autorización/auditoría.

- `aaa new-model`
  - Configura autenticación con RADIUS y fallback local (ejemplo).

- `aaa authentication login default group radius local`

---

# 09 - IPv6: configuración y verificación

## Habilitar enrutamiento IPv6

R1(config)# `ipv6 unicast-routing`

## Asignar direcciones a interfaces

R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `ipv6 address 2001:db8:acad:1::1/64`
R1(config-if)# `no shutdown`

## SLAAC y DHCPv6

- Para soporte SLAAC, las interfaces anuncian prefijos si están configuradas correctamente.
- Para DHCPv6 usar un servidor DHCPv6 externo o configurar `ipv6 dhcp pool` en IOS.

## Verificación

- Muestra resumen de interfaces IPv6 con estado.
  R1# `show ipv6 interface brief`

- Muestra la tabla de enrutamiento IPv6.
  R1# `show ipv6 route`

- Muestra tabla de vecinos IPv6 (equivalente a ARP en IPv4).
  R1# `show ipv6 neighbors`

---

# 10 - Comandos útiles y troubleshooting

Comandos rápidos para diagnóstico y monitoreo.

- Verifica conectividad básica.
- `ping <ip>`
  - Muestra la ruta hacia un destino.

- `traceroute <ip>`
  - Muestra dispositivos Cisco conectados directamente.

- `show cdp neighbors`
  - Muestra tabla ARP.

- `show arp`
  - Información de controladores y hardware (puede variar por plataforma).

- `show controllers`
  - Muestra paquetes IP procesados; usar con precaución en producción.

- `debug ip packet`

## Comandos de verificación de capa 2/3

- Muestra tabla de direcciones MAC aprendidas (en switch).
- `show mac-address-table`
  - Muestra puertos trunk, VLANs permitidas y native VLAN.

- `show interfaces trunk`
  - Muestra resumen de grupos EtherChannel configurados.

- `show etherchannel summary`

## Recuperación de contraseñas (resumen operativo)

1. Reiniciar y entrar en ROMMON (Ctrl+Break).
2. `confreg 0x2142`
3. `reset`
4. `copy startup-config running-config`
5. Cambiar contraseñas.
6. `config-register 0x2102`
7. `write memory` y `reload`.

## Tips rápidos

- Cuando algo no funciona, revisar estos comandos para diagnóstico básico.
- `show ip interface brief`, `show ip route`, `show running-config`
  - Para problemas de enlace físico, revisar estos comandos.

- `show interfaces <if>`, `show controllers <if>`

---
