# Example 02 - Inter-VLAN Routing (Router-on-a-stick)

Topología: Switch S1 conecta a Router R1 (trunk), varias VLANs 10/20.

Objetivos:
- Configurar trunk 802.1Q entre S1 y R1
- Crear subinterfaces en R1 para VLANs 10 y 20
- Comprobar enrutamiento inter-vlan

Configuración (S1):

S1(config)# `vlan 10`
S1(config-vlan)# `name Users`
S1(config)# `vlan 20`
S1(config-vlan)# `name Servers`

S1(config)# `interface GigabitEthernet1/0/1`
S1(config-if)# `switchport mode trunk`
S1(config-if)# `switchport trunk encapsulation dot1q`
S1(config-if)# `switchport trunk allowed vlan 10,20`

Configuración (R1):

R1(config)# `interface GigabitEthernet0/0.10`
R1(config-subif)# `encapsulation dot1Q 10`
R1(config-subif)# `ip address 192.168.10.1 255.255.255.0`

R1(config)# `interface GigabitEthernet0/0.20`
R1(config-subif)# `encapsulation dot1Q 20`
R1(config-subif)# `ip address 192.168.20.1 255.255.255.0`

R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `no shutdown`

Verificación:

R1# `show ip interface brief`
S1# `show interfaces trunk`
S1# `show vlan brief`

Topología (ASCII):

```
		[PC VLAN10]     [Server VLAN20]
				|               |
			G1/0/5         G1/0/20
				 \             /
					\           /
					 S1 (trunk to R1)
							 | G1/0/1 (trunk)
							 |
						 R1 Gi0/0 (subinterfaces .10/.20)
```

Lab steps (Packet Tracer / GNS3):
1. Conectar PCs a S1 en puertos de acceso: PC VLAN10 -> G1/0/5, Server VLAN20 -> G1/0/20.
2. Conectar S1 G1/0/1 a R1 Gi0/0 (configurar como trunk 802.1Q).
3. Aplicar configuración del switch y del router (subinterfaces) en el orden mostrado.
4. Verificar desde R1 `ping 192.168.10.2` (host VLAN10) y `show interfaces trunk` en S1.
