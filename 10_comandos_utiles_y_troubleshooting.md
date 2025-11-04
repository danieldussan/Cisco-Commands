# 10 - Comandos útiles y troubleshooting

Comandos rápidos para diagnóstico y monitoreo.


- `ping <ip>`
  - Verifica conectividad básica.

- `traceroute <ip>`
  - Muestra la ruta hacia un destino.

- `show cdp neighbors`
  - Muestra dispositivos Cisco conectados directamente.

- `show arp`
  - Tabla ARP.

- `show controllers`
  - Información de controladores y hardware (puede variar por plataforma).

- `debug ip packet`
  - Muestra paquetes IP procesados; usar con precaución en producción.

## Comandos de verificación de capa 2/3


- `show mac-address-table`
- `show interfaces trunk`
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

-- Cuando algo no funciona, revisar `show ip interface brief`, `show ip route` y `show running-config`.
- Para problemas de enlace, revisar `show interfaces <if>` y `show controllers <if>`.
