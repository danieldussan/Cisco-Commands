# 🧹 Restablecer configuración en Router y Switch Cisco

Guía rápida para **borrar completamente la configuración existente** en equipos Cisco (routers y switches), útil para iniciar desde cero en laboratorios o reconfiguraciones.

---

## 🔧 1. Borrar configuración en un **Router Cisco**

### 🔁 Pasos

1. Conéctate al router por **consola serial** usando PuTTY, TeraTerm o similar.
2. Ingresa al modo privilegiado:

```
Router> enable
```

3. Borra la configuración guardada en NVRAM:

```
Router# write erase
```

o alternativamente:

`Router# erase startup-config`

4. (Opcional) Verifica que se haya borrado:

```
Router# show startup-config
```

Debe mostrar una configuración vacía o inexistente.

5. Reinicia el router para aplicar los cambios:

```
Router# reload
```

Cuando pregunte si deseas guardar la configuración, responde:

`no`

6. El router se reiniciará como si fuera nuevo, mostrando el diálogo de configuración inicial:

Would you like to enter the initial configuration dialog? [yes/no]:

---

## 🧰 2. Borrar configuración en un **Switch Cisco**

### 🔁 Pasos

1. Conéctate al switch por consola.
2. Ingresa al modo privilegiado:

```
Switch> enable
```

3. Borra la configuración almacenada:

```
Switch# write erase
```

o:

```
Switch# erase startup-config
```

4. Borra también el archivo de VLANs (muy importante):

```
Switch# delete flash:vlan.dat
```

Confirma cuando lo pida:

```
Delete filename [vlan.dat]? [confirm]
```

5. Verifica que los archivos fueron eliminados:

```
Switch# dir flash:
```

6. Reinicia el switch:

```
Switch# reload
```

Cuando pregunte si deseas guardar la configuración, responde:

```
no
```

7. Al iniciar nuevamente, el switch estará limpio, sin configuraciones previas ni VLANs personalizadas.

---

## ⚠️ Notas importantes

- El comando `write erase` borra la **configuración de arranque (startup-config)**.
- El archivo `vlan.dat` almacena las **VLAN creadas manualmente** y no se borra con `write erase`.
- Después del reinicio, el equipo se comporta como si fuera **nuevo de fábrica**.
- No es necesario usar `confreg` o `ROMMON` para este proceso si simplemente se desea borrar la configuración.

---

## 🧠 Resultado final esperado

Al reiniciar el dispositivo, deberías ver:

```
--- System Configuration Dialog ---
Would you like to enter the initial configuration dialog? [yes/no]:

```

Esto confirma que la configuración fue completamente eliminada y el router/switch está listo para ser configurado nuevamente.
