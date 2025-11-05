# Example 03 - NAT + DHCP (Salida a Internet con PAT)

Topología: R1 con interfaz pública hacia Internet (G0/0) y LAN privada (G0/1). Switch S1 conecta hosts.

Objetivos:
- Configurar pool DHCP para LAN
- Configurar PAT (ip nat overload) hacia la interfaz pública
- Verificar traducciones NAT

Configuración (R1):

R1(config)# `ip dhcp excluded-address 192.168.10.1 192.168.10.10`
R1(config)# `ip dhcp pool LAN10`
R1(dhcp-config)# `network 192.168.10.0 255.255.255.0`
R1(dhcp-config)# `default-router 192.168.10.1`
R1(dhcp-config)# `dns-server 8.8.8.8`

! Interfaces
R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `ip address 203.0.113.5 255.255.255.248`
R1(config-if)# `ip nat outside`
R1(config)# `interface GigabitEthernet0/1`
R1(config-if)# `ip address 192.168.10.1 255.255.255.0`
R1(config-if)# `ip nat inside`
R1(config-if)# `no shutdown`

! NAT PAT
R1(config)# `access-list 1 permit 192.168.10.0 0.0.0.255`
R1(config)# `ip nat inside source list 1 interface GigabitEthernet0/0 overload`

Verificación:

R1# `show ip nat translations`
R1# `show ip nat statistics`
S1# `show ip dhcp binding`

Topología (ASCII):

```
	[Internet] --- ISP --- [R1 Gi0/0]
								  |
							  Gi0/1
								  |
								[S1]
							  /   \
						  PC1   PC2
```

Lab steps (Packet Tracer / GNS3):
1. Conectar R1 Gi0/0 a la nube/ISP y Gi0/1 a S1.
2. Conectar hosts a S1.
3. Configurar DHCP pool y NAT en R1 según la sección. Orden sugerido:
	a) Configurar interfaces y `ip nat inside/outside`.
	b) Configurar DHCP (`ip dhcp pool ...`).
	c) Configurar ACL y NAT PAT.
4. Verificar con `show ip dhcp binding` y `show ip nat translations`.
