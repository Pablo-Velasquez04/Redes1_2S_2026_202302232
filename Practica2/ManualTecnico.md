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

# Topología resultante
![Captura de la topologia](Imagenes/Topología.png)

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

# Levantamiento de la Infraestructura de Enlaces Troncales

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

# Configuración de VTP (Dominio 202302232, Servidores y Clientes).   

## 1. Configuración del protocolo VTP

El protocolo VTP permite centralizar la administración de VLANs desde un switch servidor. Para este caso, se configura un dominio común con el carnet del estudiante y una contraseña compartida, de modo que los switches clientes reciban automáticamente las VLANs definidas en el servidor principal.

### 1.1. Configuración de los switches servidores (VTP Server)

Por defecto, todos los switches inician en modo servidor, pero es recomendable forzarlo explícitamente para asegurar el rol, además de definir el dominio y una contraseña de seguridad.

Abre la CLI de SW_CORE_1 y de los switches principales de cada zona (SW_Z1_Principal, SW_Z2_Retail, SW_Z3_Cine, SW_Z4_Principal y SW_Z5_Seguridad), y ejecuta el siguiente bloque en cada uno:

```bash
enable
configure terminal

vtp domain 202302232
vtp password redes1
vtp mode server

end
write memory
```

> Nota: al ingresar el dominio, la consola mostrará un mensaje confirmando el cambio de estado de `NULL` a `202302232`.

### 1.2. Configuración de los switches clientes (VTP Client)

Los switches en modo cliente no pueden crear, eliminar ni modificar VLANs; únicamente reciben las actualizaciones del servidor y las aplican a su base de datos local.

Abre la CLI del Core de respaldo (SW_CORE_2) y de todos los switches de acceso (SW_Z1_Acceso1, SW_Z1_Acceso2, SW_Z1_Acceso3, SW_Z4_Acceso1, SW_Z4_Acceso2), e ingresa este bloque de comandos:

```bash
enable
configure terminal

vtp domain 202302232
vtp password redes1
vtp mode client

end
write memory
```

### Verificación de la configuración

Para comprobar que la configuración fue exitosa, accede a cualquier switch cliente (por ejemplo, SW_CORE_2 o SW_Z1_Acceso1) y ejecuta el siguiente comando en modo privilegiado:

```bash
show vtp status
```

### Resultado esperado

- `VTP Version Capable`: debe indicar `1 to 2` o `1 to 3`.
- `VTP Operating Mode`: debe mostrar `Client`.
- `VTP Domain Name`: debe mostrar `202302232`.
- `Configuration Revision`: en este punto debe estar en `0`; este valor aumentará cuando se creen las VLANs en el siguiente paso.
- 
![Captura de la CLI de SW_CORE_2 con show vtp status](Imagenes/core2_showVTPstatus1.png)

Este procedimiento garantiza que el dominio VTP quede correctamente definido y que todos los switches clientes queden sincronizados con el servidor principal.


# Configuración de la Base de Datos Global de VLANs

Para estructurar la red del proyecto y aislar los dominios de difusión por cada zona de la Ciudad Comercial Cayalá, se definieron 7 VLANs globales en la base de datos central del switch SW_CORE_1. Gracias al protocolo VTP en el dominio 202302232, la creación de estas entidades se propagó automáticamente hacia los switches servidores y clientes de la topología.

## Matriz Definitiva de Segmentación por VLAN

| VLAN ID | Nombre de VLAN | Zona / Propósito | Rango IP Asignado (VLSM) | Estado |
|---|---|---|---|---|
| 12 | Z1_Bancos | Zona 1: Distrito Empresarial (Bancos) | 192.168.10.0/26 | Active |
| 22 | Z2_Retail | Zona 2: Comercio Retail | 192.168.10.128/27 | Active |
| 32 | Z3_Cine | Zona 3: Entretenimiento (Cine) | 192.168.10.160/28 | Active |
| 42 | Z4_Food | Zona 4: Gastronomía Fast-Food | 192.168.10.64/26 | Active |
| 52 | Z5_Seguridad | Zona 5: Amenidades / Seguridad | 192.168.10.176/28 | Active |
| 99 | ADMIN | VLAN Nativa / Tráfico de Gestión | 192.168.10.192/28 | Active |
| 999 | BLACKHOLE | Seguridad (Aislamiento de Puertos Inactivos) | N/A (Sin IP) | Active |

## Comandos CLI de Referencia Aplicados (SW_CORE_1)

```bash
enable
configure terminal

vlan 12
 name Z1_Bancos
vlan 22
 name Z2_Retail
vlan 32
 name Z3_Cine
vlan 42
 name Z4_Food
vlan 52
 name Z5_Seguridad
vlan 99
 name ADMIN
vlan 999
 name BLACKHOLE
exit
end
write memory
```


# Configuración de Spanning Tree Protocol (Rapid PVST+ / IEEE 802.1w) y Prioridades de Puente Raíz

Para prevenir bucles de conmutación en la Capa 2 (bucles de difusión) y garantizar tiempos de convergencia ultra rápidos ante cualquier eventualidad o fallo físico en los enlaces, se implementó el protocolo **Rapid Per-VLAN Spanning Tree Plus (Rapid PVST+)** en la totalidad de los switches de la topología.

Se determinó una jerarquía determinista para la selección del **Root Bridge (Puente Raíz)**, distribuyendo las prioridades de la siguiente manera:

## Tabla de Jerarquía y Prioridades Spanning Tree

| Dispositivo | Rol Spanning Tree | Prioridad Base Configurada | Prioridad Calculada (VLAN 12) | Estado del Dispositivo |
|---|---|---|---|---|
| SW_CORE_1 | Root Bridge Primario | 4096 | 4108 (4096 + 12) | Root Principal (Todos sus puertos en estado Desg FWD) |
| SW_CORE_2 | Root Bridge Secundario (Backup) | 8192 | 8204 (8192 + 12) | Backup Root (Acepta SW_CORE_1 vía Po1) |
| Switches de Distribución / Acceso | Nodos Clientes STP | 32768 (Por defecto) | 32780 (32768 + 12) | Nodos Hoja (Apuntan su Root Port hacia la jerarquía Core) |

## Comandos CLI de Referencia Aplicados

```bash
! En SW_CORE_1 (Root Bridge Primario)
spanning-tree mode rapid-pvst
spanning-tree vlan 12,22,32,42,52,99 priority 4096

! En SW_CORE_2 (Root Bridge Secundario)
spanning-tree mode rapid-pvst
spanning-tree vlan 12,22,32,42,52,99 priority 8192

! En Switches de Distribución y Acceso
spanning-tree mode rapid-pvst
```


# Asignación de Puertos de Acceso y Seguridad de Red (VLAN 999 Blackhole)

Para completar el esquema de conmutación de Capa 2 y garantizar la seguridad física de los switches de acceso, se aplicaron dos políticas fundamentales en todas las zonas de la red:

## Configuración de Puertos de Acceso y Optimización con PortFast

Los puertos conectados directamente a estaciones de trabajo (PCs) se asignaron explícitamente a su respectiva VLAN de zona en modo `access`. Adicionalmente, se habilitó el parámetro `spanning-tree portfast`, permitiendo que las interfaces pasen inmediatamente al estado de reenvío (Forwarding) sin atravesar los estados de transición de Spanning Tree (Listening/Learning), optimizando el tiempo de conexión de los clientes.

## Hardening de Puertos No Utilizados (VLAN 999 - Blackhole)

Como medida de mitigación frente a accesos no autorizados a la infraestructura física, todos los puertos que no cumplen funciones de enlace troncal ni están asignados a terminales finales fueron reubicados en la VLAN 999 (BLACKHOLE) y desactivados administrativamente mediante la orden `shutdown`.

## Matriz de Asignación de Puertos y Seguridad por Switch de Acceso (Ejemplo Zona 4)

| Switch | Interfaz | Modo de Puerto | VLAN Asignada | Función / Estado |
|---|---|---|---|---|
| SW_Z4_Acceso1 | Fa0/1 | Trunk (802.1Q) | Native 99 / Allowed 12,22,32,42,52,99 | Enlace de enlace ascendente hacia SW_Z4_Principal |
| SW_Z4_Acceso1 | Fa0/2 – Fa0/3 | Access | VLAN 42 (Z4_Food) | Conexión a clientes (PC24, PC25) con PortFast activo |
| SW_Z4_Acceso1 | Fa0/4 – Fa0/24, Gig0/1 – Gig0/2 | Access | VLAN 999 (BLACKHOLE) | Puertos inactivos / Aislados administrativamente (shutdown) |

## Comandos CLI de Referencia Aplicados (SW_Z4_Acceso1)

```bash
enable
configure terminal

! Asignación de puertos de acceso a terminales de usuario
interface range FastEthernet0/2 - 3
 switchport mode access
 switchport access vlan 42
 spanning-tree portfast
exit

! Aseguramiento de puertos no utilizados (VLAN Blackhole)
interface range FastEthernet0/4 - 24, GigabitEthernet0/1 - 2
 switchport mode access
 switchport access vlan 999
 shutdown
exit

end
write memory
```


# Enrutamiento Inter-VLAN, Direccionamiento IP y Gestión

Para habilitar la comunicación entre las distintas zonas de la Ciudad Comercial Cayalá manteniendo el aislamiento de broadcast de Capa 2, se activó la función de enrutamiento IP de Capa 3 (`ip routing`) en el switch multicapa central SW_CORE_1 (Cisco Catalyst 3560). Se configuraron Interfaces Virtuales de Switch (SVIs) para cada VLAN, actuando como las puertas de enlace predeterminadas (Default Gateways) de toda la topología.

## Tabla Definitiva de Esquema de Direccionamiento IP (VLSM)

| Zona / Segmento | VLAN | Subred Base | Máscara de Subred | Default Gateway (SVI Core 1) | Rango IP de Hosts / Dispositivos |
|---|---|---|---|---|---|
| Zona 1: Bancos | 12 | 192.168.10.0/26 | 255.255.255.192 | 192.168.10.1 | 192.168.10.2 – 192.168.10.62 |
| Zona 4: Fast-Food | 42 | 192.168.10.64/26 | 255.255.255.192 | 192.168.10.65 | 192.168.10.66 – 192.168.10.126 |
| Zona 2: Retail | 22 | 192.168.10.128/27 | 255.255.255.224 | 192.168.10.129 | 192.168.10.130 – 192.168.10.158 |
| Zona 3: Cine | 32 | 192.168.10.160/28 | 255.255.255.240 | 192.168.10.161 | 192.168.10.162 – 192.168.10.174 |
| Zona 5: Seguridad | 52 | 192.168.10.176/28 | 255.255.255.240 | 192.168.10.177 | 192.168.10.178 – 192.168.10.190 |
| Administración | 99 | 192.168.10.192/28 | 255.255.255.240 | 192.168.10.193 | 192.168.10.194 – 192.168.10.206 |
| Blackhole | 999 | N/A | N/A | Sin Enrutamiento | N/A (Aislamiento Total) |

## Comandos CLI de Referencia Aplicados (SW_CORE_1)

```bash
enable
configure terminal

! Activación del motor de enrutamiento
ip routing

! SVIs para Enrutamiento Inter-VLAN
interface Vlan 12
 description Gateway Z1 Bancos
 ip address 192.168.10.1 255.255.255.192
 no shutdown
exit

interface Vlan 22
 description Gateway Z2 Retail
 ip address 192.168.10.129 255.255.255.224
 no shutdown
exit

interface Vlan 32
 description Gateway Z3 Cine
 ip address 192.168.10.161 255.255.255.240
 no shutdown
exit

interface Vlan 42
 description Gateway Z4 Fast-Food
 ip address 192.168.10.65 255.255.255.192
 no shutdown
exit

interface Vlan 52
 description Gateway Z5 Seguridad
 ip address 192.168.10.177 255.255.255.240
 no shutdown
exit

interface Vlan 99
 description Gateway ADMIN
 ip address 192.168.10.193 255.255.255.240
 no shutdown
exit

end
write memory
```



### Análisis de Dominios de Colisión y Difusión

En la topología implementada para la Ciudad Comercial Cayalá, la segmentación de la red se analiza bajo dos esquemas fundamentales de la Capa de Enlace de Datos:

1. **Dominios de Difusión (Broadcast Domains):**
   * **Cantidad Total:** 7 Dominios de Difusión.
   * **Delimitación:** Están delimitados en la Capa 3 por las Interfaces Virtuales de Switch (SVIs) del switch multicapa `SW_CORE_1`.
   * **Desglose:**
     * VLAN 12 (Zona 1 - Bancos): `192.168.10.0/26`
     * VLAN 22 (Zona 2 - Retail): `192.168.10.128/27`
     * VLAN 32 (Zona 3 - Cine): `192.168.10.160/28`
     * VLAN 42 (Zona 4 - Fast-Food): `192.168.10.64/26`
     * VLAN 52 (Zona 5 - Seguridad): `192.168.10.176/28`
     * VLAN 99 (Administración Central): `192.168.10.192/28`
     * VLAN 999 (Blackhole / Aislada): Sin enrutamiento IP
   * **Justificación:** Esta separación limita la propagación de tramas de difusión (como solicitudes ARP o DHCP) únicamente al segmento donde se originan, reduciendo el tráfico innecesario en los enlaces troncales y elevando la seguridad entre zonas comerciales.

2. **Dominios de Colisión (Collision Domains):**
   * **Cantidad Total:** Un dominio de colisión por cada puerto físico activo operando en modo Full-Duplex (aproximadamente 35 a 40 dominios de colisión activos en la topología total).
   * **Delimitación:** Delimitados individualmente por cada puerto micro-segmentado de los switches Cisco Catalyst 2960 y 3560.
   * **Justificación:** Al operar exclusivamente con switches mediante conexiones Ethernet dedicadas en modo Full-Duplex, se eliminan por completo las colisiones de tramas en la red.



### Estándares de Cableado y Medios Físicos de Enlace

La infraestructura física simulada cumple con la norma de cableado estructurado **TIA/EIA-568B**:

* **Cable Directo (Straight-Through / UTP Cat 6):** Utilizado para interconectar dispositivos de distinta capa OSI, específicamente desde las tarjetas de red de las estaciones de trabajo (PCs) hacia los puertos de acceso de los switches de cada zona (`FastEthernet`).
* **Cable Cruzado / Agregado LACP (Crossover / UTP Cat 6a / Fibra):** Utilizado para las interconexiones directas Switch-a-Switch en el núcleo y distribución (`Po1`, `Po2`, troncales `GigabitEthernet` y `FastEthernet`), garantizando la velocidad de conmutación de tramas 802.1Q.