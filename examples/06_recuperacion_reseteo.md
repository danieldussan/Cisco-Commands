# Example 06 - Recuperación y reseteo (resumen práctico)

Contenido rápido con comandos paso a paso para recuperar contraseñas y dejar equipos en estado limpio.

## Router (resumen)

1. Entrar a ROMMON con `Ctrl+Break` durante el arranque.
2. `rommon> confreg 0x2142`
3. `rommon> reset`
4. `copy startup-config running-config`
5. Cambiar contraseñas y `config-register 0x2102`
6. `write memory` y `reload`

## Switch (resumen)

1. Apagar switch.
2. Mantener `MODE` y encender hasta que LED cambie.
3. `flash_init` / `rename flash:config.text flash:config.old` / `boot`
4. `rename flash:config.old flash:config.text` / `copy flash:config.text system:running-config`
5. Cambiar contraseñas y `write memory`

Notas:
- Siempre verificar modelo y documentación oficial para pasos específicos.

Topología (ASCII) - concepto:

```
 Console connections:
	[PC-Console] ---console--- [Router/Switch]
```

Lab steps (Packet Tracer / GNS3):
1. Conectar la consola del PC al dispositivo (simulador o real).
2. Seguir los pasos de ROMMON o MODE según el dispositivo (ver sección correspondiente).
3. Tras restaurar la config, comprobar `show running-config` y guardar con `write memory`.
