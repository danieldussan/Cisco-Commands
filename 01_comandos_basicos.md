# 01 - Comandos básicos

Este archivo reúne los comandos más comunes en routers y switches Cisco, con explicaciones breves de los argumentos largos para que sea rápido y práctico.

## Accesos y modos

- `enable`
  - Descripción: pasar a modo privilegiado (exec).

- `exit`
  - Descripción: volver al modo anterior.

- `configure terminal`
  - Descripción: entrar a modo de configuración global.

## Visualización de configuraciones

 - `show running-config`
  - Muestra la configuración activa en RAM.

- `show startup-config`
  - Muestra la configuración almacenada en NVRAM (arranque).

- `show interfaces [interface-id]`
  - Argumentos: `interface-id` (opcional) — p.ej. `GigabitEthernet0/1`.
  - Muestra estadísticas y estado de la interfaz.

- `show ip interface brief`
  - Muestra resumen de interfaces con IP y estado.

- `show ip route`
  - Muestra la tabla de enrutamiento (rutas conectadas, estáticas y aprendidas).

- `show vlan brief`
  - Muestra lista de VLANs y sus puertos.

## Configuración de interfaz

 - `interface TYPE/NUMBER`
  - Ejemplo: `interface GigabitEthernet0/1`.
  - Entra al modo de configuración de la interfaz.

- `shutdown` / `no shutdown`
  - Apagar o encender la interfaz.

- `description TEXT`
  - Agrega una descripción a la interfaz.

## VLANs y trunking (resumen rápido)

 - `vlan <id>`
  - Crea o edita una VLAN.

- `switchport mode access`
  - Configura el puerto como acceso (un solo VLAN).

- `switchport access vlan <vlan-id>`
  - Asigna la VLAN de acceso al puerto.

- `switchport mode trunk`
  - Pone el puerto en modo troncal (lleva múltiples VLANs).

- `switchport trunk native vlan <vlan-id>`
  - VLAN nativa para troncales (sin tag dot1q).

## Enrutamiento y rutas

 - `ip route <dest> <mask> <next-hop-ip|exit-intf> [distance]`
  - Ejemplo: `ip route 0.0.0.0 0.0.0.0 192.168.1.1`
  - Crea una ruta estática. `distance` es opcional (administrative distance).

## DHCP en routers

 - `ip dhcp excluded-address <start> <end>`
  - Excluye direcciones del pool DHCP.

- `ip dhcp pool <name>`
  - Crea un pool DHCP. Opciones dentro del modo: `network`, `default-router`, `dns-server`.

## NAT y PAT (resumen)

 - `ip nat inside source list <acl> pool <name>`
  - NAT dinámico desde una lista de direcciones hacia un pool público.

- `ip nat inside source list <acl> interface <if> overload`
  - PAT (NAT sobrecargado) — mapea múltiples privados a una sola IP pública (interfaz).

Nota: no olvidar marcar las interfaces con `ip nat inside` y `ip nat outside` según corresponda.

## ACLs (resumen)

 - `access-list <num> permit|deny <src> <wildcard>`
  - ACL estándar. `num` 1-99 o 1300-1999.

- `access-list <num> deny|permit tcp <src> <wildcard> <dst> <wildcard> eq <port>`
  - ACL extendida para protocolos y puertos.

## Guardar y reiniciar

 - `copy running-config startup-config`
  - Guarda la configuración activa en NVRAM.

- `reload`
  - Reinicia el dispositivo.

## SSH y acceso seguro (resumen)

 - `ip domain-name <name>`
  - Define nombre de dominio para generar claves RSA.

- `crypto key generate rsa`
  - Genera claves RSA. (Algunas versiones requieren `modulus 1024` o `2048`).

- `username <user> secret <password>`
  - Crea usuario local con contraseña encriptada.

- `line vty 0 15`
  - Configura líneas vty para Telnet/SSH. Comandos comunes dentro:
  - `transport input ssh` — permitir solo SSH.
  - `login local` — usar base de usuarios local.

## Recuperación de contraseña (resumen)

Pasos rápidos:

1. Reiniciar y entrar en ROMMON (Ctrl+Break).
2. `confreg 0x2142` — ignorar startup-config.
3. `reset` o `reload`.
4. `copy startup-config running-config`.
5. Cambiar contraseñas y `config-register 0x2102`.
6. `write memory` y `reload`.

## EtherChannel (resumen)


- `interface range FastEthernet0/1 - 2`
  - Selecciona rango de interfaces.

- `channel-group 1 mode active`
  - Agrupa interfaces en EtherChannel (LACP - activo).

- `interface port-channel 1`
  - Configura la interfaz lógica del bundle.

## Cheatsheet rápido

 - `show interfaces`
 - `show ip route`
 - `show vlan brief`
 - `copy run start`

---

Notas rápidas adicionales:

- `show running-config | include <string>` — buscar líneas que contengan `<string>` en la running-config.
- `show logging` — ver mensajes del syslog en el dispositivo.
