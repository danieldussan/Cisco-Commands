# Example 07 - Edge: NAT, ACLs y hardening (ssh, logging)

Topología: R1 (edge) con interfaz pública y red LAN; aplicar NAT, ACLs para limitar servicios y endurecer acceso SSH.

Objetivos:
- PAT para la LAN
- ACLs para limitar acceso al dispositivo (solo SSH desde administración)
- Habilitar logging y bloqueo de intentos

Configuración (R1):

! Interfaces
R1(config)# `interface GigabitEthernet0/0`
R1(config-if)# `description Link to ISP`
R1(config-if)# `ip address 198.51.100.2 255.255.255.248`
R1(config-if)# `ip nat outside`

R1(config)# `interface GigabitEthernet0/1`
R1(config-if)# `description LAN`
R1(config-if)# `ip address 10.10.10.1 255.255.255.0`
R1(config-if)# `ip nat inside`
R1(config-if)# `no shutdown`

! DHCP
R1(config)# `ip dhcp excluded-address 10.10.10.1 10.10.10.10`
R1(config)# `ip dhcp pool LAN`
R1(dhcp-config)# `network 10.10.10.0 255.255.255.0`
R1(dhcp-config)# `default-router 10.10.10.1`

! NAT PAT
R1(config)# `access-list 10 permit 10.10.10.0 0.0.0.255`
R1(config)# `ip nat inside source list 10 interface GigabitEthernet0/0 overload`

! ACL para administración (permitir SSH desde 192.0.2.0/28)
R1(config)# `access-list 101 permit tcp 192.0.2.0 0.0.0.15 any eq 22`
R1(config)# `access-list 101 deny ip any any`
R1(config)# `line vty 0 4`
R1(config-line)# `access-class 101 in`
R1(config-line)# `transport input ssh`

! Hardening
R1(config)# `service password-encryption`
R1(config)# `username admin secret VeryS3cure!`
R1(config)# `ip domain-name example.local`
R1(config)# `crypto key generate rsa modulus 2048`
R1(config)# `ip ssh version 2`
R1(config)# `login block-for 60 attempts 5 within 60`
R1(config)# `logging buffered 4096`
R1(config)# `logging 10.0.0.5`  ! servidor syslog interno

Verificación:

R1# `show ip nat translations`
R1# `show access-lists`
R1# `show logging`

Notas:
- Probar SSH desde la red de administración antes de cerrar accesos.
- Usar `log` en entradas ACL durante pruebas si necesitas auditar coincidencias.

Topología (ASCII):

```
 [Mgmt-Net 192.0.2.0/28] --+ 
									 \ 
									  [R1 - Edge]
									 / 
 [LAN 10.10.10.0/24] -------+
							  Gi0/0: ISP
```

Lab steps (Packet Tracer / GNS3):
1. Conectar interfaz Gi0/0 a la nube/ISP y Gi0/1 a la LAN.
2. Configurar DHCP, NAT y ACLs en el orden:
	a) Interfaces + `ip nat inside/outside`.
	b) DHCP pool.
	c) ACLs de administración y aplicar a `line vty`.
	d) Seguridad: `username`, `service password-encryption`, `ip ssh version 2`.
3. Verificar `show ip nat translations`, `show access-lists` y probar accesos SSH desde la red de administración.
