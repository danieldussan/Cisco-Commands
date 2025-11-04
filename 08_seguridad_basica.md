# 08 - Seguridad básica

Sección con comandos para asegurar equipos de red a nivel básico (switches y routers).

## Port-Security (switch)

S1(config)# interface FastEthernet0/1
S1(config-if)# switchport mode access
S1(config-if)# switchport port-security
S1(config-if)# switchport port-security maximum 1
S1(config-if)# switchport port-security violation shutdown
S1(config-if)# switchport port-security mac-address sticky

Explicación rápida:
- `switchport port-security maximum 1` — limita a 1 MAC por puerto.
- `violation shutdown` — acción al detectar violación: shut the port.
- `mac-address sticky` — aprende y guarda la MAC en la running-config.

## Desactivar servicios inseguros

R1(config)# no cdp run
R1(config)# no ip http server

## Protección de accesos y bloqueo de intentos

R1(config)# login block-for 60 attempts 3 within 30

Explicación:
- Bloquea intentos de login por 60 segundos si hay 3 intentos fallidos en 30 segundos.

## SSH (seguro)

R1(config)# ip domain-name example.com
R1(config)# crypto key generate rsa modulus 2048
R1(config)# username admin secret MiPasswordSeguro
R1(config)# line vty 0 15
R1(config-line)# transport input ssh
R1(config-line)# login local

## SNMP (recomendación)

- Evitar usar SNMPv1/2c en producción; usar SNMPv3 con autenticación y cifrado.

## Backups y auditoría

- Guardar configuraciones frecuentemente: `copy running-config startup-config`.
- Automatizar backups con logs o scripts (external).
