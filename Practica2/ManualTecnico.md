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