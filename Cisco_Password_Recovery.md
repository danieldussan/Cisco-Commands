# Cisco Password Recovery & Configuration Reset

Guía combinada para recuperar contraseñas y borrar configuraciones en routers y switches Cisco. Incluye pasos para ROMMON, borrar `vlan.dat` en switches y notas sobre el prompt ROMMON (ej. `rommon 9>`).

---

## Contenido

1. Recuperar contraseña en un Router Cisco
2. Recuperar contraseña en un Switch Cisco
3. Borrar configuración completamente
4. Resumen rápido

---

## 1) Recuperar contraseña en un Router Cisco

### Requisitos

- Conexión por puerto de consola (PuTTY, TeraTerm, SecureCRT, etc.)
- Cable de consola Cisco
- Velocidad típica: `9600` bps (baud)

### Procedimiento

1. Conectar al router por consola.
2. Reiniciar el router y durante el arranque presionar `Ctrl+Break` (o `Ctrl+Pause` en algunas terminales) para entrar en ROMMON:

   ```
   rommon 1>
   ```

3. En ROMMON, cambiar el registro de configuración para que el router ignore el `startup-config`:

   ```
   rommon 1> confreg 0x2142
   rommon 2> reset
   ```

4. El router arrancará sin cargar la configuración guardada. Entra al modo privilegiado:

   ```
   Router> enable
   Router#
   ```

5. Copiar el `startup-config` a `running-config` para recuperar las interfaces y otros datos sin activar las contraseñas antiguas:

   ```
   Router# copy startup-config running-config
   ```

6. Cambiar la contraseña (ejemplo):

   ```
   Router# configure terminal
   Router(config)# enable secret NUEVA_CONTRASEÑA
   Router(config)# line console 0
   Router(config-line)# password NUEVA_CONTRASEÑA
   Router(config-line)# login
   Router(config)# exit
   ```

7. Restaurar el registro de configuración a su valor normal (0x2102) para que cargue la configuración en el próximo reinicio:

   ```
   Router(config)# config-register 0x2102
   Router# write memory
   ```

8. Reiniciar el router:

   ```
   Router# reload
   ```

> Nota: en algunos IOS/PLATAFORMAS el procedimiento puede variar ligeramente. Siempre revisar documentación del modelo si algo no responde.

---

## 2) Recuperar contraseña en un Switch Cisco (Catalyst 2960/9200 o similar)

> Algunos switches Cisco usan un botón `MODE` para entrar en un modo similar a ROMMON. El procedimiento varía por modelo. A continuación el flujo común para Catalyst 2960 / 9200.

### Procedimiento

1. Apagar el switch.
2. Mantener presionado el botón `MODE` y encender el switch.
   - Mantenerlo presionado hasta que el LED del MODE parpadee o cambie a color verde, luego soltar.
3. El switch entra en el prompt `switch:` (modo de recuperación / ROMMON del switch).

4. Inicializar el sistema de archivos en flash (si se solicita):

   ```
   switch: flash_init
   switch: dir flash:
   ```

5. Renombrar el archivo de configuración para que no se cargue al arrancar:

   ```
   switch: rename flash:config.text flash:config.old
   ```

6. Reiniciar el switch normalmente:

   ```
   switch: boot
   ```

7. Una vez arrancado, entra al modo privilegiado y restaura la configuración a `running-config`:

   ```
   Switch> enable
   Switch# rename flash:config.old flash:config.text
   Switch# copy flash:config.text system:running-config
   ```

8. Cambiar la contraseña y guardar:

   ```
   Switch(config)# enable secret NUEVA_CONTRASEÑA
   Switch(config)# line console 0
   Switch(config-line)# password NUEVA_CONTRASEÑA
   Switch(config-line)# login
   Switch(config)# end
   Switch# write memory
   ```

> Nota: algunos modelos más modernos (ej. ciertos 9200) pueden tener ligeras diferencias. Consultar el manual del modelo cuando sea posible.

---

## 3) Borrar configuración completamente

### En Router

```
Router# write erase
Router# erase startup-config
Router# reload
```

Al reiniciar, cuando pregunte si guardar la configuración, responder `no`.

### En Switch

```
Switch# write erase
Switch# delete flash:vlan.dat
Switch# reload
```

- `vlan.dat` contiene las VLAN creadas manualmente y no se borra con `write erase`, por eso es importante eliminarlo si se quiere un estado limpio.

---

## 4) Resumen rápido (tabla)

| Acción                  | Comando principal                    | Dispositivo   |
| ----------------------- | ------------------------------------ | ------------- |
| Ignorar configuración   | `confreg 0x2142`                     | Router        |
| Restaurar config normal | `config-register 0x2102`             | Router        |
| Borrar configuración    | `write erase`                        | Router/Switch |
| Borrar VLANs            | `delete flash:vlan.dat`              | Switch        |
| Recuperar configuración | `copy startup-config running-config` | Router/Switch |

---

### Notas finales y recomendaciones

- Siempre asegurarse de tener acceso físico y un cable de consola antes de comenzar.
- Para laboratorios, documentar cambios y guardarlos en un repositorio (ej. Git) o exportar configs.
- En entornos de producción, no realizar estos procedimientos sin autorización y ventanas de mantenimiento.

---
