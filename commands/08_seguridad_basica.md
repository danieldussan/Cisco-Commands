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
- `switchport port-security maximum 1` — limita a 1 MAC por puerto.
- `violation shutdown` — acción al detectar violación: shut the port.
- `mac-address sticky` — aprende y guarda la MAC en la running-config.

## Desactivar servicios inseguros


R1(config)# `no cdp run`
R1(config)# `no ip http server`

## Protección de accesos y bloqueo de intentos


R1(config)# `login block-for 60 attempts 3 within 30`

Explicación:
- Bloquea intentos de login por 60 segundos si hay 3 intentos fallidos en 30 segundos.

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

- Encriptar contraseñas sensibles y usar `enable secret` en lugar de `enable password`:
	- `enable secret <clave>` — algoritmo más seguro y encriptado.

- Habilitar `service password-encryption` para ofuscar contraseñas de texto claro en la running-config:
	- `service password-encryption`

- Registrar logs localmente y en un servidor remoto (syslog):
	- `logging buffered 4096` — almacenar logs en memoria para revisión.
	- `logging <ip-del-servidor-syslog>` — enviar logs a servidor remoto.

- Considerar `aaa new-model` para centralizar autenticación/autorización/auditoría y usar RADIUS/TACACS+:
	- `aaa new-model`
	- Configurar `aaa authentication login default group radius local` (ejemplo con fallback local).
