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

S1(config)# vlan 10
S1(config-vlan)# name Users

Asignar puerto a VLAN (modo access):

S1(config)# interface GigabitEthernet1/0/5
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10

Explicación breve:
- `switchport mode access` fuerza el puerto a modo acceso.
- `switchport access vlan <id>` asigna la VLAN.

## Configurar trunk 802.1Q

Configurar trunk estático (recomendado cuando se conoce el otro extremo):

S1(config)# interface GigabitEthernet1/0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk encapsulation dot1q  # (en plataformas que soportan ambos)
S1(config-if)# switchport trunk native vlan 99

Permitir VLANs específicas en el trunk:

S1(config-if)# switchport trunk allowed vlan 10,20,99

Notas:
- `switchport trunk native vlan <id>` — define qué VLAN se envía sin tag. Evitar usar VLAN 1 como nativa por seguridad.

## DTP (Negotiation)

- `switchport mode dynamic desirable` — intenta negociar un trunk (LHS inicia).
- `switchport mode dynamic auto` — responde a negociaciones.
- Para seguridad: `switchport nonegotiate` para desactivar DTP en puertos trunk estáticos.

Ejemplo seguro (trunk estático, sin DTP):

S1(config)# interface GigabitEthernet1/0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport nonegotiate

## Voice VLAN

Cuando conectas teléfonos IP, normalmente quieres separar la voz y datos:

S1(config)# interface GigabitEthernet1/0/10
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10       # VLAN datos
S1(config-if)# switchport voice vlan 150       # VLAN voz
S1(config-if)# mls qos trust cos               # confiar en el COS del teléfono

Explicación:
- `switchport voice vlan <id>` — etiqueta el tráfico de voz y permite que el teléfono envíe datos de voz en la VLAN de voz.

## Troubleshooting de trunks

- show interfaces trunk
  - Muestra puertos trunk, VLANs permitidas y native VLAN.

- show vlan brief
  - Confirma qué puertos están asignados a cada VLAN.

- show interfaces GigabitEthernet1/0/1 switchport
  - Muestra modo del puerto (access/trunk), VLAN nativa y configuración DTP.

- show cdp neighbors detail
  - Verifica la conexión entre switches y la interfaz remota.

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

## Ejercicios prácticos

1. Configura un enlace troncal entre S1 y S2 que lleve VLANs 10, 20 y 150, con native VLAN 99. Verifica con `show interfaces trunk`.
2. Configura un puerto con voice VLAN y simula la conectividad con un IP Phone (datos en VLAN10, voz en VLAN150).
