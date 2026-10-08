<div align="center">

<img src="Imagenes/logoUSAC.png" width="150">

## UNIVERSIDAD DE SAN CARLOS DE GUATEMALA

### FACULTAD DE INGENIERÍA

### REDES DE COMPUTADORAS 1

<br><br>

# MANUAL TÉCNICO

<br><br>

**Catedrático:** Ing. Pedro Pablo Hernández Ramirez

**Auxiliar:** César Fernando Sazo Quisquinay

**Nombre:** Pablo Daniel Velásquez Hernández

**Carnet:** 202302232

**Sección:** N

**Semestre:** Segundo Semestre 2026

<br><br>

</div>



# Manual Técnico - Red de la Ciudad Comercial Cayalá

# 1. Análisis de Zonas Seleccionadas

Para el desarrollo de esta infraestructura, se seleccionaron 5 áreas estratégicas del complejo Cayalá. Cada una fue analizada según su rol operativo, volumen de tráfico y nivel de criticidad para justificar el diseño de la topología de red:

---

## Zona 1: Distrito Empresarial y Financiero (60 hosts)

| **Aspecto** | **Descripción** |
|---|---|
| **Rol Operativo** | Área administrativa y financiera. Incluye establecimientos como BAC Credomatic, Banco Industrial y oficinas de Regus. Sus usuarios son principalmente oficinistas, ejecutivos y cajeros bancarios. |
| **Volumen de Tráfico** | Alto. Se requiere capacidad para 60 hosts simultáneos realizando transacciones financieras, videoconferencias y manejo de bases de datos. |
| **Nivel de Criticidad** | Crítico (Alto). La pérdida de conectividad paralizaría las transacciones bancarias y operaciones corporativas, generando pérdidas económicas inmediatas. |
| **Justificación de Diseño** | Por su alto tráfico y criticidad, esta zona requiere Alta Disponibilidad con al menos 2 enlaces físicos redundantes hacia el switch core. Además, se justifica el uso de EtherChannel para sumar el ancho de banda de los enlaces y evitar cuellos de botella. |

---

## Zona 2: Zona Comercial Retail (28 hosts)

| **Aspecto** | **Descripción** |
|---|---|
| **Rol Operativo** | Tiendas de ancla y comercio minorista. Representa áreas como Cemaco y Nike Factory Store. Su función es la venta al detalle y control de inventarios. |
| **Volumen de Tráfico** | Medio. Soporta 28 hosts destinados a cajas registradoras, computadoras de bodega y sistemas de inventario en tiempo real. |
| **Nivel de Criticidad** | Medio. |
| **Justificación de Diseño** | No requiere EtherChannel por la cantidad de hosts, pero sí un enlace directo en fibra o GigabitEthernet hacia la red central para garantizar que las consultas de inventario no tengan latencia. |

---

## Zona 3: Zona de Entretenimiento (12 hosts)

| **Aspecto** | **Descripción** |
|---|---|
| **Rol Operativo** | Áreas recreativas. Incluye establecimientos como Cinépolis, Skyzone y Futeca. Los usuarios son personal de taquilla y administración local. |
| **Volumen de Tráfico** | Bajo-Medio. Con 12 hosts, el tráfico de red interno es bajo (solo venta de tickets y control de acceso). |
| **Nivel de Criticidad** | Bajo. |
| **Justificación de Diseño** | Se conectará a la topología general mediante un enlace simple (topología tipo estrella básica). No requiere redundancia, ya que una caída de red solo afecta la venta momentánea de boletos electrónicos. |

---

## Zona 4: Zona Gastronómica Fast-Food (50 hosts)

| **Aspecto** | **Descripción** |
|---|---|
| **Rol Operativo** | Área de restaurantes de comida rápida y cafés. Incluye locales como Taco Bell, Pollo Campero, McDonald's y Café Barista. Los usuarios son empleados en puntos de venta (POS) y clientes conectados a redes Wi-Fi públicas. |
| **Volumen de Tráfico** | Medio-Alto. Maneja 50 hosts enfocados en facturación electrónica rápida, terminales de cobro y puntos de acceso inalámbrico. |
| **Nivel de Criticidad** | Medio. Si la red falla, los restaurantes no pueden emitir facturas ni cobrar con tarjeta, aunque el riesgo operativo del complejo a nivel macro no colapsa. |
| **Justificación de Diseño** | Requiere conectividad estable pero no prioritaria a nivel core. Se recomienda una topología de estrella conectada al backbone principal, con un enlace robusto, pero sin necesidad estricta de redundancia extrema frente a otras áreas. |

---

## Zona 5: Amenidades, Parqueos y Seguridad (7 hosts)

| **Aspecto** | **Descripción** |
|---|---|
| **Rol Operativo** | Control perimetral del complejo. Abarca los "Puntos de Seguridad", estaciones de pago y garitas de estacionamientos. Sus usuarios son el personal de seguridad y monitoreo. |
| **Volumen de Tráfico** | Bajo. Apenas requiere 7 hosts para equipos DVR, control de talanqueras y computadoras de monitoreo. |
| **Nivel de Criticidad** | Crítico (Alto). Aunque tiene pocos hosts, si la seguridad y las talanqueras pierden conexión, los vehículos no pueden entrar ni salir y el complejo queda vulnerable. |
| **Justificación de Diseño** | A pesar del bajo tráfico, se justifica implementar Alta Disponibilidad (redundancia con 2 enlaces físicos a distintos switches) utilizando STP (Rapid PVST+). No requiere EtherChannel, pero sí enlaces de respaldo vitales. |

---

### Resumen de Zonas

| **Zona** | **Nombre** | **Hosts** | **Criticidad** | **Tráfico** | **Redundancia** |
|---|---|---|---|---|---|
| 1 | Distrito Empresarial y Financiero | 60 | Crítico (Alto) | Alto | Sí (2 enlaces + EtherChannel) |
| 2 | Zona Comercial Retail | 28 | Medio | Medio | No (enlace directo) |
| 3 | Zona de Entretenimiento | 12 | Bajo | Bajo-Medio | No (estrella básica) |
| 4 | Zona Gastronómica Fast-Food | 50 | Medio | Medio-Alto | No (enlace robusto) |
| 5 | Amenidades, Parqueos y Seguridad | 7 | Crítico (Alto) | Bajo | Sí (2 enlaces + STP) |

# Resumen de la Topología Híbrida Resultante

Para el diseño de la red del complejo Cayalá, se implementó una **topología híbrida** que combina diferentes esquemas de conexión según los requerimientos específicos de cada zona. A continuación, se detalla la justificación de cada decisión:

---

## Malla Parcial / Redundante

| **Aspecto** | **Descripción** |
|---|---|
| **Zonas Aplicadas** | Zona 1 (Distrito Empresarial y Financiero) y Zona 5 (Amenidades, Parqueos y Seguridad). |
| **Justificación** | Debido a sus requerimientos de **alta disponibilidad** y su **nivel de criticidad alto**, estas zonas requieren múltiples rutas alternativas para garantizar la continuidad operativa ante fallos en cualquier enlace. |
| **Beneficio** | Si un enlace falla, el tráfico se redirige automáticamente por la ruta secundaria, evitando la interrupción del servicio. |

---

## Estrella Simple

| **Aspecto** | **Descripción** |
|---|---|
| **Zonas Aplicadas** | Zona 2 (Comercial Retail), Zona 3 (Entretenimiento) y Zona 4 (Gastronómica Fast-Food). |
| **Justificación** | Estas zonas presentan una **criticidad media o baja**, lo que permite conectarse al nodo central mediante **enlaces únicos** sin comprometer la operación global del complejo. |
| **Beneficio** | Reduce costos de infraestructura y simplifica la administración, manteniendo una conectividad estable para sus operaciones. |

---

## Uso de EtherChannel

| **Aspecto** | **Descripción** |
|---|---|
| **Zonas Aplicadas** | Exclusivamente en los enlaces troncales de la **Zona 1** hacia el **Core**. |
| **Justificación** | El **alto volumen de tráfico administrativo y financiero** de esta zona requiere sumar el ancho de banda de múltiples enlaces físicos para evitar cuellos de botella. |
| **Beneficio** | Incrementa la capacidad de transferencia y proporciona redundancia a nivel de enlace, ya que si uno de los enlaces del bundle falla, el tráfico continúa fluyendo por los enlaces restantes. |

---

### Resumen de la Topología por Zona

| **Zona** | **Nombre** | **Topología Aplicada** | **Redundancia** | **EtherChannel** |
|---|---|---|---|---|
| 1 | Distrito Empresarial y Financiero | Malla Parcial | Sí | Sí |
| 2 | Zona Comercial Retail | Estrella Simple | No | No |
| 3 | Zona de Entretenimiento | Estrella Simple | No | No |
| 4 | Zona Gastronómica Fast-Food | Estrella Simple | No | No |
| 5 | Amenidades, Parqueos y Seguridad | Malla Parcial | Sí | No |


# 2. Esquema de Direccionamiento IP (VLSM)

Para la asignación de direcciones IP en las diferentes zonas de Ciudad Cayalá, se utilizó la técnica de **Máscara de Subred de Longitud Variable (VLSM)** partiendo de la dirección base `192.168.10.0/24`. Esta segmentación es la más adecuada para el escenario planteado, ya que permite asignar a cada zona únicamente la cantidad de direcciones necesarias, evitando el desperdicio de direcciones IP.

Las redes se ordenaron de **mayor a menor demanda de hosts**, dando como resultado la siguiente tabla de direccionamiento:

Si tomamos una dirección base común como `192.168.10.0/24` (que soporta hasta 254 hosts, suficiente para los 157 que pide la práctica en total), los cálculos quedarían de la siguiente manera:

| **Zona (VLAN)** | **Hosts Solicitados** | **Tamaño de Subred** | **Dirección de Red** | **Rango de IPs Utilizables** | **Máscara / CIDR** | **Default Gateway (Ejemplo)** |
|---|---|---|---|---|---|---|
| Zona 1 (VLAN 12) | 60 | 62 hosts | 192.168.10.0 | .1 a .62 | 255.255.255.192 (/26) | 192.168.10.1 |
| Zona 4 (VLAN 42) | 50 | 62 hosts | 192.168.10.64 | .65 a .126 | 255.255.255.192 (/26) | 192.168.10.65 |
| Zona 2 (VLAN 22) | 28 | 30 hosts | 192.168.10.128 | .129 a .158 | 255.255.255.224 (/27) | 192.168.10.129 |
| Zona 3 (VLAN 32) | 12 | 14 hosts | 192.168.10.160 | .161 a .174 | 255.255.255.240 (/28) | 192.168.10.161 |
| Zona 5 (VLAN 52) | 7 | 14 hosts | 192.168.10.176 | .177 a .190 | 255.255.255.240 (/28) | 192.168.10.177 |
| Admin (VLAN 99) | Tráfico | 14 hosts | 192.168.10.192 | .193 a .206 | 255.255.255.240 (/28) | 192.168.10.193 |

---

# 3. Dominios de Colisión

Como parte fundamental del diseño de esta infraestructura, es necesario identificar la delimitación de los dominios de colisión.

## Identificación y Delimitación

En esta topología, los dispositivos que delimitan los dominios de colisión son los **switches** (Cisco 2960 y 3560). Por la naturaleza de estos equipos operando en **Capa 2**, cada puerto activo del switch constituye un dominio de colisión único e independiente.

Por lo tanto, si la topología final conecta, por ejemplo, **157 hosts finales** y **10 enlaces troncales** entre switches, existirán exactamente **167 dominios de colisión**.

## Justificación de la Segmentación

Esta micro-segmentación es sumamente adecuada y vital para el complejo Cayalá. Al tener un dominio de colisión por puerto, se elimina la posibilidad de colisiones de red (operando en **full-duplex**), lo cual garantiza que zonas de alto tráfico como el **Distrito Empresarial (Zona 1)** o la **Zona Gastronómica (Zona 4)** tengan un ancho de banda dedicado por dispositivo.

Esto es crucial para que los sistemas de **punto de venta (POS)** y las **transacciones financieras** no sufran latencia ni pérdida de paquetes por contención del medio.

# Configuración del Backbone y Agregación de Enlaces (EtherChannel LACP)

Para la interconexión de alta velocidad y alta disponibilidad entre el núcleo de la red (`SW_CORE_1` y `SW_CORE_2`) y la Zona 1 (`SW_Z1_Principal`), se implementó la técnica de agregación de enlaces **EtherChannel** operando bajo el protocolo estándar **LACP (IEEE 802.3ad)**, en cumplimiento con el requerimiento de número de carnet finalizado en dígito par.

---

## Tabla de Interfaces y Agregación LACP

| **Identificador** | **Switch Origen** | **Interfaces Físicas** | **Switch Destino** | **Interfaces Destino** | **Protocolo / Modo** | **Rol / Función** |
|---|---|---|---|---|---|---|
| Port-Channel 1 | SW_CORE_1 | Fa0/23, Fa0/24 | SW_CORE_2 | Fa0/23, Fa0/24 | LACP (active) | Redundancia y troncalización del Núcleo (Core ↔ Core) |
| Port-Channel 2 | SW_CORE_1 | Gig0/1, Gig0/2 | SW_Z1_Principal | Gig0/1, Gig0/2 | LACP (active) | Troncal de Alta Disponibilidad (Core 1 ↔ Zona 1) |

---

## Parámetros de Troncalización en Port-Channels

| **Parámetro** | **Configuración** |
|---|---|
| **Modo Troncal** | Habilitado explícitamente (`switchport mode trunk`). |
| **Encapsulación** | 802.1Q (`switchport trunk encapsulation dot1q` en switches 3560). |
| **VLAN Nativa** | VLAN 99 (ADMIN) configurada para aislar el tráfico de administración. |
| **Poda y Seguridad de VLANs** | Se restringió el paso de tráfico aplicando únicamente las VLANs necesarias (`switchport trunk allowed vlan 12,22,32,42,52,99`), evitando la propagación innecesaria mediante la exclusión del parámetro `allow all`. |

---

## Comandos CLI de Referencia Aplicados

### SW_CORE_1

```bash
interface range FastEthernet0/23 - 24
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
 channel-group 1 mode active
exit

interface port-channel 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit
```

### SW_CORE_2 - Switch 3560

```bash
! Configurar interfaces físicas Fa0/23-24 y asociar al Port-Channel 1 (Hacia Core 1)
interface range FastEthernet0/23 - 24
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
 channel-group 1 mode active
exit

! Configuración de la interfaz lógica Port-Channel 1
interface port-channel 1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit
```

### SW_Z1_Principal - Switch 2960

```bash
! Configurar interfaces físicas Gig0/1-2 y asociar al Port-Channel 2 (Hacia Core 1)
interface range GigabitEthernet0/1 - 2
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
 channel-group 2 mode active
exit

! Configuración de la interfaz lógica Port-Channel 2
interface port-channel 2
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

! Nota: En el switch 2960 SW_Z1_Principal no se incluye la orden encapsulation dot1q porque los switches de Capa 2 de esta serie solo soportan el estándar 802.1Q de forma nativa.
```

# Paso 2: Levantamiento de la Infraestructura de Enlaces Troncales

El presente paso define la configuración de los enlaces troncales restantes dentro de la topología, con la finalidad de permitir la propagación de las VLAN 12, 22, 32, 42, 52 y la VLAN nativa 99. La estructura resultante garantiza la segmentación lógica requerida y la interoperabilidad entre los switches del núcleo, distribución y acceso.

---

## 1. Troncales desde el núcleo

La siguiente configuración se aplica en los switches de núcleo para habilitar los enlaces de interconexión hacia las zonas de servicio y acceso.

### 1.1 SW_CORE_1 (Switch 3560)

Se habilitan los troncales hacia la Zona 2, la Zona 4 y la Zona 5:

```bash
enable
configure terminal

interface range FastEthernet0/10, FastEthernet0/15, FastEthernet0/22
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

### 1.2 SW_CORE_2 (Switch 3560)

Se habilitan los troncales hacia la Zona 3 y la Zona 5:

```bash
enable
configure terminal

interface range FastEthernet0/10, FastEthernet0/21
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

---

## 2. Troncales internos de la Zona 1

En la Zona 1, SW_Z1_Principal actúa como dispositivo de distribución hacia los switches de acceso.

### 2.1 SW_Z1_Principal (Switch 2960)

```bash
enable
configure terminal

interface range FastEthernet0/1 - 3
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

### 2.2 SW_Z1_Acceso1, SW_Z1_Acceso2 y SW_Z1_Acceso3 (Switches 2960)

Para cada switch de acceso de la Zona 1, se configura el enlace de retorno hacia SW_Z1_Principal:

```bash
enable
configure terminal

interface FastEthernet0/1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

---

## 3. Troncales de las zonas 2, 3, 4 y 5

### 3.1 SW_Z2_Retail (Switch 2960)

```bash
enable
configure terminal

interface FastEthernet0/24
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

### 3.2 SW_Z3_Cine (Switch 2960)

```bash
enable
configure terminal

interface FastEthernet0/24
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

### 3.3 SW_Z4_Principal (Switch 2960)

Se habilita el enlace hacia el núcleo y los enlaces hacia los switches de acceso:

```bash
enable
configure terminal

interface range FastEthernet0/1 - 2, FastEthernet0/24
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

### 3.4 SW_Z4_Acceso1 y SW_Z4_Acceso2 (Switches 2960)

Se configura el enlace de retorno hacia SW_Z4_Principal en ambos switches de acceso:

```bash
enable
configure terminal

interface FastEthernet0/1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

### 3.5 SW_Z5_Seguridad (Switch 2960)

Se habilitan los enlaces redundantes hacia ambos switches núcleo:

```bash
enable
configure terminal

interface range FastEthernet0/23 - 24
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 12,22,32,42,52,99
exit

end
write memory
```

---

## 4. Verificación del funcionamiento

Para verificar la correcta activación de los enlaces troncales, se ejecuta el siguiente comando en cualquier switch del dominio de capa 2:

```bash
show interfaces trunk
```
### Resultado en SW_Z1_Principal

![Captura de la CLI de SW_Z1_Principal ejecutando show interfaces trunk](Imagenes/sw_z1_show_interfaces_trunk.png)

**Descripción de la evidencia:** Demuestra los enlaces troncales activos hacia los switches de acceso (Fa0/1, Fa0/2, Fa0/3) y el canal Po2 hacia el Core, confirmando que la VLAN 99 figura como activa en el dominio de administración.

### Resultado esperado

- La columna Port debe mostrar los interfaces físicos activos en modo trunk.
- La columna Mode debe indicar `on`.
- La columna Encapsulation debe mostrar `802.1q`.
- La columna Native vlan debe corresponder a `99`.
- La columna Vlans allowed on trunk debe reflejar únicamente: `12,22,32,42,52,99`.
```