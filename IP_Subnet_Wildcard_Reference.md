# Referencia: Direcciones IP, Máscaras de Subred y Wildcards

Guía completa de referencia para direccionamiento IPv4, máscaras de subred y wildcard masks utilizadas en configuraciones Cisco.

## Tabla de Contenidos

1. [Direccionamiento IPv4](#direccionamiento-ipv4)
2. [Máscaras de Subred (Subnet Masks)](#máscaras-de-subred-subnet-masks)
3. [Wildcard Masks](#wildcard-masks)
4. [Conversión entre Máscara y Wildcard](#conversión-entre-máscara-y-wildcard)
5. [Notación CIDR](#notación-cidr)
6. [Clases de Redes (Histórico)](#clases-de-redes-histórico)
7. [Direcciones IP Privadas vs Públicas](#direcciones-ip-privadas-vs-públicas)
8. [Tablas de Referencia Rápida](#tablas-de-referencia-rápida)
9. [Ejemplos Prácticos](#ejemplos-prácticos)

---

## Direccionamiento IPv4

### Estructura de una Dirección IPv4

Una dirección IPv4 consta de **32 bits** divididos en **4 octetos** (8 bits cada uno), representados en notación decimal punteada:

```
Ejemplo: 192.168.1.100

Binario:  11000000.10101000.00000001.01100100
Decimal:  192      .168      .1        .100
```

### Componentes de una Dirección IP

- **Porción de Red**: Identifica la red a la que pertenece el host
- **Porción de Host**: Identifica el host específico dentro de la red
- **Máscara de Subred**: Define qué parte es red y qué parte es host

---

## Máscaras de Subred (Subnet Masks)

### ¿Qué es una Máscara de Subred?

La máscara de subred es un número de 32 bits que identifica qué parte de una dirección IP corresponde a la red y qué parte corresponde al host.

**Regla**: Los bits en **1** indican la porción de red, los bits en **0** indican la porción de host.

### Ejemplos de Máscaras Comunes

| Notación CIDR | Máscara Decimal | Binario | Hosts Usables | Uso Común |
|---------------|------------------|---------|---------------|-----------|
| /8            | 255.0.0.0        | 11111111.00000000.00000000.00000000 | 16,777,214 | Redes clase A |
| /16           | 255.255.0.0      | 11111111.11111111.00000000.00000000 | 65,534 | Redes clase B |
| /24           | 255.255.255.0    | 11111111.11111111.11111111.00000000 | 254 | Redes clase C / LANs pequeñas |
| /25           | 255.255.255.128  | 11111111.11111111.11111111.10000000 | 126 | Subredes medianas |
| /26           | 255.255.255.192  | 11111111.11111111.11111111.11000000 | 62 | Subredes pequeñas |
| /27           | 255.255.255.224  | 11111111.11111111.11111111.11100000 | 30 | Subredes muy pequeñas |
| /28           | 255.255.255.240  | 11111111.11111111.11111111.11110000 | 14 | Subredes pequeñas |
| /30           | 255.255.255.252  | 11111111.11111111.11111111.11111100 | 2 | Enlaces punto a punto |
| /32           | 255.255.255.255  | 11111111.11111111.11111111.11111111 | 1 | Host específico |

**Nota**: Se restan 2 hosts porque la primera dirección es la dirección de red y la última es la dirección de broadcast.

### Cálculo de Hosts por Subred

Fórmula: **2^(bits de host) - 2**

Ejemplo con /26:
- Bits de host: 32 - 26 = 6 bits
- Hosts totales: 2^6 = 64
- Hosts usables: 64 - 2 = 62

---

## Wildcard Masks

### ¿Qué es una Wildcard Mask?

Una **wildcard mask** (máscara inversa) es el complemento de una máscara de subred. Se utiliza en:
- **ACLs (Access Control Lists)**
- **OSPF network statements**
- **EIGRP network statements**
- **NAT configurations**

**Regla**: Los bits en **0** indican que el bit debe coincidir, los bits en **1** indican que el bit puede ser cualquier valor (wildcard).

### Conversión: Máscara → Wildcard

**Fórmula**: `Wildcard = 255.255.255.255 - Subnet Mask`

Ejemplos:

| Máscara de Subred | Cálculo | Wildcard Mask |
|-------------------|---------|---------------|
| 255.255.255.0     | 255.255.255.255 - 255.255.255.0 | 0.0.0.255 |
| 255.255.255.128   | 255.255.255.255 - 255.255.255.128 | 0.0.0.127 |
| 255.255.255.192   | 255.255.255.255 - 255.255.255.192 | 0.0.0.63 |
| 255.255.255.224   | 255.255.255.255 - 255.255.255.224 | 0.0.0.31 |
| 255.255.255.240   | 255.255.255.255 - 255.255.255.240 | 0.0.0.15 |
| 255.255.255.248   | 255.255.255.255 - 255.255.255.248 | 0.0.0.7 |
| 255.255.255.252   | 255.255.255.255 - 255.255.255.252 | 0.0.0.3 |
| 255.255.255.254   | 255.255.255.255 - 255.255.255.254 | 0.0.0.1 |
| 255.255.255.255   | 255.255.255.255 - 255.255.255.255 | 0.0.0.0 |

### Wildcards Especiales

| Wildcard | Significado | Uso |
|----------|-------------|-----|
| 0.0.0.0  | Coincidencia exacta | Un host específico |
| 0.0.0.255 | Cualquier host en el último octeto | Red /24 completa |
| 0.0.255.255 | Cualquier host en los últimos 2 octetos | Red /16 completa |
| 255.255.255.255 | Cualquier dirección | `any` en ACLs |

---

## Conversión entre Máscara y Wildcard

### Método Manual (Octeto por Octeto)

Para cada octeto:
1. Restar el valor del octeto de la máscara de 255
2. El resultado es el octeto de la wildcard

Ejemplo: Máscara 255.255.255.192

```
Octeto 1: 255 - 255 = 0
Octeto 2: 255 - 255 = 0
Octeto 3: 255 - 255 = 0
Octeto 4: 255 - 192 = 63

Wildcard: 0.0.0.63
```

### Método Binario

1. Convertir máscara a binario
2. Invertir todos los bits (0→1, 1→0)
3. Convertir de vuelta a decimal

Ejemplo: Máscara 255.255.255.224 (/27)

```
Máscara:   11111111.11111111.11111111.11100000
Invertir:  00000000.00000000.00000000.00011111
Wildcard:  0.0.0.31
```

---

## Notación CIDR

### ¿Qué es CIDR?

**CIDR (Classless Inter-Domain Routing)** es una notación que indica cuántos bits se usan para la porción de red.

Formato: `IP_Address/Prefix_Length`

Ejemplos:
- `192.168.1.0/24` → 24 bits para red, 8 bits para host
- `10.0.0.0/8` → 8 bits para red, 24 bits para host
- `172.16.0.0/16` → 16 bits para red, 16 bits para host

### Tabla de Prefijos Comunes

| Prefijo | Máscara | Hosts Totales | Hosts Usables | Uso |
|---------|---------|---------------|---------------|-----|
| /8      | 255.0.0.0 | 16,777,216 | 16,777,214 | Redes muy grandes |
| /16     | 255.255.0.0 | 65,536 | 65,534 | Redes grandes |
| /24     | 255.255.255.0 | 256 | 254 | LANs estándar |
| /25     | 255.255.255.128 | 128 | 126 | Subredes medianas |
| /26     | 255.255.255.192 | 64 | 62 | Subredes pequeñas |
| /27     | 255.255.255.224 | 32 | 30 | Subredes muy pequeñas |
| /28     | 255.255.255.240 | 16 | 14 | Subredes pequeñas |
| /29     | 255.255.255.248 | 8 | 6 | Subredes muy pequeñas |
| /30     | 255.255.255.252 | 4 | 2 | Enlaces punto a punto |
| /31     | 255.255.255.254 | 2 | 2* | Enlaces punto a punto (RFC 3021) |
| /32     | 255.255.255.255 | 1 | 1 | Host específico |

*Nota: /31 permite usar ambas direcciones como host en enlaces punto a punto (RFC 3021).

---

## Clases de Redes (Histórico)

Aunque las clases están obsoletas (reemplazadas por CIDR), es útil conocerlas:

| Clase | Rango | Máscara por Defecto | Uso |
|-------|-------|---------------------|-----|
| A     | 1.0.0.0 - 126.255.255.255 | 255.0.0.0 (/8) | Redes muy grandes |
| B     | 128.0.0.0 - 191.255.255.255 | 255.255.0.0 (/16) | Redes medianas |
| C     | 192.0.0.0 - 223.255.255.255 | 255.255.255.0 (/24) | Redes pequeñas |
| D     | 224.0.0.0 - 239.255.255.255 | N/A | Multicast |
| E     | 240.0.0.0 - 255.255.255.255 | N/A | Reservado/Experimental |

**Nota**: Las direcciones que comienzan con 127.x.x.x están reservadas para loopback (127.0.0.1).

---

## Direcciones IP Privadas vs Públicas

### Direcciones Privadas (RFC 1918)

Estas direcciones **NO** son enrutables en Internet y se usan en redes internas:

| Rango | Máscara por Defecto | Uso |
|-------|---------------------|-----|
| 10.0.0.0 - 10.255.255.255 | 255.0.0.0 (/8) | Redes grandes privadas |
| 172.16.0.0 - 172.31.255.255 | 255.255.0.0 (/16) | Redes medianas privadas |
| 192.168.0.0 - 192.168.255.255 | 255.255.255.0 (/24) | Redes pequeñas privadas (hogar/pequeña oficina) |

### Direcciones Públicas

Cualquier dirección IP que **NO** esté en los rangos privados es pública y puede ser enrutada en Internet.

### Direcciones Especiales

| Dirección | Nombre | Uso |
|-----------|--------|-----|
| 0.0.0.0/0 | Ruta por defecto | Representa todas las redes |
| 127.0.0.1 | Loopback | Interfaz local del dispositivo |
| 255.255.255.255 | Broadcast limitado | Broadcast en la red local |
| 169.254.0.0/16 | APIPA (Automatic Private IP Addressing) | Autoasignación cuando DHCP falla |

---

## Tablas de Referencia Rápida

### Tabla de Máscaras Comunes

| CIDR | Máscara | Wildcard | Hosts Usables | Incremento de Red |
|------|---------|----------|---------------|-------------------|
| /24  | 255.255.255.0 | 0.0.0.255 | 254 | 1 |
| /25  | 255.255.255.128 | 0.0.0.127 | 126 | 128 |
| /26  | 255.255.255.192 | 0.0.0.63 | 62 | 64 |
| /27  | 255.255.255.224 | 0.0.0.31 | 30 | 32 |
| /28  | 255.255.255.240 | 0.0.0.15 | 14 | 16 |
| /29  | 255.255.255.248 | 0.0.0.7 | 6 | 8 |
| /30  | 255.255.255.252 | 0.0.0.3 | 2 | 4 |

### Tabla de Valores de Octeto

| Valor Decimal | Binario | Bits de Host |
|---------------|---------|--------------|
| 0             | 00000000 | 8 |
| 128           | 10000000 | 7 |
| 192           | 11000000 | 6 |
| 224           | 11100000 | 5 |
| 240           | 11110000 | 4 |
| 248           | 11111000 | 3 |
| 252           | 11111100 | 2 |
| 254           | 11111110 | 1 |
| 255           | 11111111 | 0 |

---

## Ejemplos Prácticos

### Ejemplo 1: ACL con Wildcard

Permitir toda la red 192.168.10.0/24:

```cisco
access-list 10 permit 192.168.10.0 0.0.0.255
```

Explicación:
- IP: 192.168.10.0 (red base)
- Wildcard: 0.0.0.255 (coincide con cualquier host en el último octeto)
- Resultado: Permite 192.168.10.0 - 192.168.10.255

### Ejemplo 2: OSPF Network Statement

Anunciar la red 10.1.1.0/24 en OSPF:

```cisco
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
```

Explicación:
- Red: 10.1.1.0
- Wildcard: 0.0.0.255 (equivalente a /24)
- Área: 0

### Ejemplo 3: ACL para Rango Específico

Permitir solo hosts 192.168.10.16 - 192.168.10.31:

```cisco
access-list 20 permit 192.168.10.16 0.0.0.15
```

Explicación:
- IP base: 192.168.10.16
- Wildcard: 0.0.0.15 (permite variación en los últimos 4 bits)
- Resultado: 192.168.10.16 - 192.168.10.31 (16 hosts)

### Ejemplo 4: Subnetting - Crear 4 Subredes

Red original: 192.168.1.0/24

Necesitamos 4 subredes:
- Bits prestados: 2 (2^2 = 4)
- Nueva máscara: /24 + 2 = /26 (255.255.255.192)
- Hosts por subred: 2^6 - 2 = 62

Subredes resultantes:

| Subred | Rango de Hosts | Broadcast |
|--------|----------------|-----------|
| 192.168.1.0/26 | .1 - .62 | .63 |
| 192.168.1.64/26 | .65 - .126 | .127 |
| 192.168.1.128/26 | .129 - .190 | .191 |
| 192.168.1.192/26 | .193 - .254 | .255 |

### Ejemplo 5: VLSM (Variable Length Subnet Mask)

Red base: 192.168.1.0/24
Requisitos:
- Red A: 100 hosts
- Red B: 50 hosts
- Red C: 20 hosts

Solución:

1. **Red A (100 hosts)**: Necesita /25 (126 hosts)
   - Asignar: 192.168.1.0/25 (hosts .1 - .126)

2. **Red B (50 hosts)**: Necesita /26 (62 hosts)
   - Asignar: 192.168.1.128/26 (hosts .129 - .190)

3. **Red C (20 hosts)**: Necesita /27 (30 hosts)
   - Asignar: 192.168.1.192/27 (hosts .193 - .222)

### Ejemplo 6: Enlace Punto a Punto

Para un enlace entre dos routers, usar /30:

```cisco
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 10.0.0.1 255.255.255.252

R2(config)# interface GigabitEthernet0/0
R2(config-if)# ip address 10.0.0.2 255.255.255.252
```

Explicación:
- Red: 10.0.0.0/30
- Hosts: 10.0.0.1 (R1), 10.0.0.2 (R2)
- Broadcast: 10.0.0.3
- Red no utilizable: 10.0.0.0

---

## Fórmulas Útiles

### Calcular Hosts Usables
```
Hosts Usables = 2^(32 - prefix_length) - 2
```

### Calcular Número de Subredes
```
Subredes = 2^(bits_prestados)
```

### Calcular Incremento de Red
```
Incremento = 256 - valor_octeto_máscara
```

### Convertir Máscara a Wildcard
```
Wildcard = 255.255.255.255 - Subnet_Mask
```

---

## Tips y Mejores Prácticas

1. **Siempre planifica el direccionamiento** antes de configurar dispositivos
2. **Usa VLSM** para optimizar el uso de direcciones IP
3. **Reserva direcciones** para gateways, servidores y dispositivos de red
4. **Documenta** tus asignaciones de subredes
5. **Usa direcciones privadas** para redes internas
6. **Verifica cálculos** con calculadoras de subnetting si es necesario
7. **En ACLs**, recuerda que el orden importa (primera coincidencia aplica)
8. **Para enlaces punto a punto**, usa /30 o /31 según soporte del dispositivo

---

## Comandos de Verificación en Cisco

```cisco
# Ver configuración de interfaces
show ip interface brief

# Ver tabla de enrutamiento
show ip route

# Ver ACLs aplicadas
show access-lists
show ip access-lists

# Ver configuración de OSPF/EIGRP
show ip protocols
show ip ospf interface
show ip eigrp interfaces
```
