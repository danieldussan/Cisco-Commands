# Example 05 - ACLs y Seguridad

Topología: R1 con dos redes (192.168.10.0/24 y 192.168.20.0/24). Queremos bloquear HTTP entre ambas, permitir resto.

Objetivos:
- Crear ACL extendida por nombre
- Aplicarla a la interfaz adecuada
- Revisar logs y estadísticas

Configuración (R1):

R1(config)# `ip access-list extended BLOCK_HTTP`
R1(config-ext-nacl)# `deny tcp 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255 eq 80 log`
R1(config-ext-nacl)# `permit ip any any`

R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `ip access-group BLOCK_HTTP in`

Verificación:

R1# `show access-lists`
R1# `show ip interface GigabitEthernet0/0`

Notas:
- Para pruebas, usar `log` en las entradas para observar coincidencias antes de aplicar permanentemente.
- En switches, considerar usar Port-Security y deshabilitar DTP en trunks que no lo requieran.

Topología (ASCII):

```
 [Host A - 192.168.10.10]   [Host B - 192.168.20.10]
			|                         |
		   R1 (interface G0/0 with ACL applied)
```

Lab steps (Packet Tracer / GNS3):
1. Configurar las dos redes en R1 y aplicar la ACL por nombre `BLOCK_HTTP`.
2. Usar `telnet` o `curl` desde un host a puerto 80 en el otro para probar la denegación.
3. Verificar `show access-lists` para ver contadores de coincidencia.
