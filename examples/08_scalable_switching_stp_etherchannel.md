# Example 08 - Switching escalable: VLANs, STP, EtherChannel

Topología: Dos switches S1 y S2 con enlaces redundantes hacia un core (S1<->S2), múltiples VLANs, y EtherChannel entre ellos.

Objetivos:
- Crear VLANs y distribuir en ambos switches
- Configurar EtherChannel (LACP)
- Ajustar STP (prio / root) para estabilidad

Configuración (S1):

S1(config)# `vlan 10`
S1(config-vlan)# `name Users`
S1(config)# `vlan 20`
S1(config-vlan)# `name Servers`

! Interfaces hacia S2: crear EtherChannel con LACP
S1(config)# `interface range GigabitEthernet1/0/1 - 2`
S1(config-if-range)# `channel-group 10 mode active`
S1(config)# `interface Port-channel10`
S1(config-if)# `switchport mode trunk`
S1(config-if)# `switchport trunk allowed vlan 10,20`

! STP tuning (preferir S1 como root para VLANs)
S1(config)# `spanning-tree vlan 10 priority 24576`
S1(config)# `spanning-tree vlan 20 priority 24576`

Configuración (S2):

S2(config)# `interface range GigabitEthernet1/0/1 - 2`
S2(config-if-range)# `channel-group 10 mode active`
S2(config)# `interface Port-channel10`
S2(config-if)# `switchport mode trunk`
S2(config-if)# `switchport trunk allowed vlan 10,20`

Verificación:

S1# `show etherchannel summary`
S1# `show spanning-tree vlan 10`
S1# `show interfaces trunk`

Notas:
- Mantener MTU consistente en trunks y port-channel cuando uses GRE/IPsec en el core.

Topología (ASCII):

```
 [PCs VLAN10]      [Servers VLAN20]
		 |                 |
		S1 --- Port-channel10 --- S2
		 |                       |
	 (access)               (access)
```

Lab steps (Packet Tracer / GNS3):
1. Conectar dos enlaces físicos entre S1 y S2 (Gi1/0/1 y Gi1/0/2).
2. Configurar `channel-group 10 mode active` en ambos extremos y crear `port-channel10`.
3. Configurar trunks y VLANs en ambos switches, luego verificar `show etherchannel summary`.
