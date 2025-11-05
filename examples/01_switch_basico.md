# Example 01 - Switch básico (VLANs, Port-Security, Voice VLAN)

Topología: Switch S1 conectado a PCs y teléfonos IP.

Objetivos:
- Crear VLANs (10 datos, 150 voz)
- Asignar puertos de acceso y voice-vlan
- Configurar port-security en puertos de usuario

Configuración (S1):

S1# `enable`
S1(config)# `vlan 10`
S1(config-vlan)# `name Users`
S1(config)# `vlan 150`
S1(config-vlan)# `name Voice`

! Puertos de acceso para PCs
S1(config)# `interface range GigabitEthernet1/0/5 - 8`
S1(config-if-range)# `switchport mode access`
S1(config-if-range)# `switchport access vlan 10`
S1(config-if-range)# `switchport port-security`
S1(config-if-range)# `switchport port-security maximum 1`
S1(config-if-range)# `switchport port-security violation restrict`
S1(config-if-range)# `switchport port-security mac-address sticky`

! Puerto con teléfono + PC (voice VLAN)
S1(config)# `interface GigabitEthernet1/0/10`
S1(config-if)# `switchport mode access`
S1(config-if)# `switchport access vlan 10`
S1(config-if)# `switchport voice vlan 150`
S1(config-if)# `mls qos trust cos`

Verificación:

S1# `show vlan brief`
S1# `show port-security interface GigabitEthernet1/0/5`
S1# `show mac address-table`

Topología (ASCII):

```
			 [PC1]    [Phone1]
				 |         |
		G0/5 |    G0/10|
			+---+---------+---+
			|      S1 (Switch) |
			+------------------+
```

Lab steps (Packet Tracer / GNS3):
1. Conectar PC1 a `G1/0/5`, Phone1 a `G1/0/10`.
2. En S1 ejecutar los comandos listados en la sección de configuración en el mismo orden.
3. Verificar `show vlan brief` y `show port-security`.
