<div align="center">

<img src="Imagenes/logoUSAC.png" width="150">

## UNIVERSIDAD DE SAN CARLOS DE GUATEMALA

### FACULTAD DE INGENIERÍA

### REDES DE COMPUTADORAS 1

<br><br>

# MANUAL TÉCNICO - Proyecto 1: SmartCity Tech Park

<br><br>

**Catedrático:** Ing. Pedro Pablo Hernández Ramirez

**Auxiliar:** César Fernando Sazo Quisquinay

**Nombre:** Pablo Daniel Velásquez Hernández

**Carnet:** 202302232

**Sección:** N

**Semestre:** Segundo Semestre 2026

<br><br>

</div>

---

## 1. Topología de la Red

### 1.1 Topología Completa
> *En esta sección colocaremos la captura de pantalla de la topología general completamente funcional en Packet Tracer.*

La topología está organizada alrededor del switch principal `Core_DataCenter`, ubicado en el Centro de Datos. La red incluye los switches de Centro de Datos, I+D, Edificio Corporativo y Planta de Producción.

![Imagen](/Proyecto1/Imagenes/Topología.png)

### 1.2 Topologías por Área
> *Se incluyen las capturas individuales de cada una de las 4 áreas requeridas.*

**Centro de Datos (Core):**
![Imagen](/Proyecto1/Imagenes/CentroDatos.png)
**Centro de Investigación y Desarrollo (I+D):**
![Imagen](/Proyecto1/Imagenes/I+D.png)

**Edificio Corporativo (Alas A y B + Visitantes):**
![Imagen](/Proyecto1/Imagenes/EdificioCorporativo.png)

En esta área, las PCs administrativas se conectan por cable y se distribuyen entre ambas alas: una PC en `Corp_AlaA` y otra en `Corp_AlaB`. El Access Point permanece conectado a `Corp_AlaA` exclusivamente para las laptops de los visitantes.

**Planta de Producción (Segmento Legacy):**
![Imagen](/Proyecto1/Imagenes/PProduccion.png)

---

## 2. Decisiones de Diseño Físico y Topológico

### 2.1 Justificación del Diseño y Redundancia
La red utiliza una topología jerárquica con `Core_DataCenter` como nodo principal del centro de datos. Esta distribución permite concentrar los servicios críticos, aislar los segmentos por área y simplificar la administración de VLANs y protocolos de capa 2. La redundancia se ubicó en los enlaces de agregación entre el núcleo y los switches de acceso y distribución, especialmente entre `Core_DataCenter` y `SW_Servidores`, así como entre `Core_DataCenter` y `SW1-I+D`. Esto evita depender de un único enlace físico para el tráfico de datos del campus.

El enlace de mayor capacidad y relevancia en la arquitectura es la conexión del `Core_DataCenter` hacia el segmento de I+D y de Servidores, ya que concentra el tránsito principal de la infraestructura y soporta la agregación de varios puertos mediante `EtherChannel`. El diseño prioriza la disponibilidad del centro de datos y la continuidad del servicio en caso de falla de un enlace físico.

### 2.2 Medios de Transmisión
La topología emplea una combinación de cobre UTP y fibra óptica según la función del enlace. Los equipos finales (PCs, laptops, AP y el hub de la Planta de Producción) se conectan mediante cable de cobre, ya que operan a distancias cortas y requieren una solución económica y funcional. En cambio, los enlaces entre switches principales y los enlaces de agregación hacia el centro de datos se apoyan en fibra óptica para aumentar la capacidad de transferencia, reducir interferencias electromagnéticas y mantener una conexión más estable en tramos críticos.

En el archivo de Packet Tracer la diferencia entre ambos medios se observa en la forma y apariencia de los enlaces físicos; sin embargo, para dejar evidencia formal en el documento, se documenta la clasificación del cable por tramo en la siguiente tabla.

| Tramo / Segmento | Dispositivos Conectados | Tipo de Conexión | Medio Físico | Justificación Técnica |
| :--- | :--- | :--- | :--- | :--- |
| Acceso a PCs y laptops | PC / Laptop ↔ Switch de acceso | **Copper Straight-Through** | **UTP (cobre)** | Distancias cortas, conectividad local de bajo costo y suficiente ancho de banda para equipos finales. |
| Enlaces entre switches de área | Switch ↔ Switch (línea punteada) | **Copper Cross-Over** | **UTP (cobre)** | Se utiliza para interconexiones directas entre equipos de capa 2 donde se requiere emparejar TX/RX cruzados en el enlace físico. |
| Conexión del AP al switch | Access Point ↔ Switch del Edificio Corporativo | **Copper Straight-Through** | **UTP (cobre)** | El AP se conecta físicamente al switch mediante un cable de cobre para brindar servicio local a los dispositivos inalámbricos. |
| Conexión inalámbrica del AP | Access Point ↔ Laptop 1 / Laptop 2 | **Señal Inalámbrica (Wi‑Fi)** | **Wireless** | Las laptops de visitantes se conectan al AP mediante radiofrecuencia, por lo que la comunicación no se realiza por cable físico. |
| Conexión del hub Legacy | Hub de maquinaria ↔ `Prod_Legacy` | **Copper Straight-Through** | **UTP (cobre)** | El segmento legacy funciona como red compartida con equipos industriales cercanos. |
| Enlaces de distribución / núcleo | `Core_DataCenter` ↔ `SW_Servidores` | **Fibra óptica** | **Fibra óptica** | Enlace de mayor capacidad y mayor estabilidad para el tráfico central de la red. |
| Enlaces de agregación | `Core_DataCenter` ↔ `ID_SW1` / `SW1-I+D` | **Fibra óptica** | **Fibra óptica** | Reduce interferencia, agiliza la agregación de tráfico y soporta enlaces de mayor velocidad. |
| Enlaces entre áreas y core | `Core_DataCenter` ↔ switches de cada área | **Fibra óptica** | **Fibra óptica** | Mantiene el núcleo conectado con mayor rendimiento y continuidad del servicio. |

La imagen visual de la topología se conserva como evidencia gráfica, pero esta tabla es la documentación formal que indica claramente el tipo de medio y la forma de conexión utilizada en cada tramo del diseño, incluyendo el enlace de cobre cruzado, el cobre directo y la conexión inalámbrica del Access Point con las laptops.

> ![Imagen](/Proyecto1/Imagenes/Topología.png)

---

## 3. Protocolos de Capa 2

### 3.1 VTP (VLAN Trunking Protocol)
**Dominio VTP:** `Smart_3`  
**Contraseña:** proyecto12S2026  

* **Switch Servidor Elegido:** `Core_DataCenter`
* **Justificación técnica:** El switch del Centro de Datos se configuró como servidor VTP porque funciona como punto central de la red. Desde este equipo se administra el dominio VTP y se distribuye la información de VLAN hacia los switches clientes.
* **Switches clientes:** `SW_Servidores`, `ID_SW1`, `ID_SW2`, `ID_SW3`, `Corp_AlaA`, `Corp_AlaB` y `Prod_Legacy`.
* **Evidencia:**  
![Imagen](/Proyecto1/Imagenes/Core_DataCenter1.png)
  
![Imagen](/Proyecto1/Imagenes/Switch1-I+D.png)

**Configuración documentada:**

```ios
vtp mode server
vtp domain Smart_3
vtp password proyecto12S2026
```

En los demás switches se utilizó `vtp mode client`.

### 3.2 STP (Spanning Tree Protocol)
**Versión Utilizada:** `PVST`  

* **Selección del Root Bridge:**
  * **VLAN:** `1, 12, 22, 32, 42, 52, 92`
  * **Switch Root Bridge:** `Core_DataCenter`
  * **Justificación técnica:** Se configuró la prioridad del switch central a `4096` para asegurar que sea el puente raíz, centralizar el flujo de datos y evitar bucles. La salida del comando `show spanning-tree summary` confirmaba que `Core_DataCenter` era el Root Bridge para todas las VLANs del dominio.
* **Evidencia:**  
  ![Imagen](/Proyecto1/Imagenes/stp-recalculo1.png)
  ![Imagen](/Proyecto1/Imagenes/stp-recalculo2.png)
  ![Imagen](/Proyecto1/Imagenes/stp-recalculo3.png)
  ![Imagen](/Proyecto1/Imagenes/stp-recalculo4.png)

**Comando de verificación:**
```ios
show spanning-tree summary
```

**Salida esperada (verificada):**
```ios
Switch is in pvst mode
Root bridge for: default Gerencia Investigacion Produccion Servidores Visitantes NATIVA
Extended system ID           is enabled
Portfast Default             is disabled
...
VLAN0012                     0         5        0          6         11
VLAN0022                     0         5        0          6         11
VLAN0032                     0         5        0          6         11
VLAN0042                     0         5        0          6         11
VLAN0052                     0         5        0          6         11
VLAN0092                     0         5        0          6         11
```

### 3.3 EtherChannel
**Protocolo Utilizado:** `LACP`  
**Justificación del uso:** El carné termina en número par, por lo que la agregación exige LACP. Se utilizó para unir enlaces redundantes y aumentar el ancho de banda sin depender de un solo cable físico.

* **Enlaces agrupados:**
  * **Port-Channel 1:** `Core_DataCenter` ↔ `SW_Servidores` usando `Gig0/1` y `Gig0/2`.
  * **Port-Channel 2:** `Core_DataCenter` ↔ `SW1-I+D` usando `Fa0/1-4` en el Core y `Fa0/5-8` en el switch de I+D.
* **Justificación técnica:** La agregación evita la dependencia de un único enlace, suma capacidad y mantiene el enlace lógico como una sola conexión troncal con VLAN nativa `92`.
* **Evidencia:**  
  ![Imagen](/Proyecto1/Imagenes/etherchannel-summary-core.png)

  ![Imagen](/Proyecto1/Imagenes/etherchannel-summary-i+d.png)

  **Comando de verificación:** `show etherchannel summary`

  En la salida esperada se observan los grupos `Po1` y `Po2` con estado `SU` (Layer2 + In Use), protocolo `LACP` y puertos con estado `P` (participating).

---

## 4. Segmentación y Tablas Lógicas

### 4.1 Tabla de VLANs
> *Tabla que detalla cada VLAN creada basándose en el último dígito del carné.*

| Edificio / Ubicación | VLAN ID | Nombre de la VLAN |
| :--- | :---: | :--- |
| Edificio Corporativo | 12 | Gerencia |
| Centro de I+D | 22 | Investigacion |
| Planta de Producción | 32 | Produccion |
| Centro de Datos | 42 | Servidores |
| Edificio Corporativo | 52 | Visitantes |
| Enlaces troncales | 92 | NATIVA |

La VLAN nativa `92` fue creada en `Core_DataCenter` y se configuró en los enlaces troncales. Las VLAN de usuarios `12`, `22`, `32`, `42` y `52` fueron creadas en el servidor VTP y propagadas automáticamente hacia los switches clientes.

### 4.2 Tabla de Asignación de Puertos
> *Tabla con la asignación de puertos por cada switch utilizado.*

| Switch | Interfaz / Puerto | Modo (Access/Trunk) | VLAN Asignada / Permitidas | Dispositivo Conectado |
| :--- | :--- | :--- | :--- | :--- |
| `Core_DataCenter` | FA 0/1 - 24 y Gig 0/1 - 2 | Trunk | VLAN nativa 92 | `Switch0-Servers, Switch1-I+D, Switch Ala-A y B, Switch-Produccion` |
| `SW_Servidores` | Fa0/1-4 | Access | VLAN 42 | Servidores |
| `ID_SW1` | Fa0/1-3 | Access | VLAN 22 | PCs de I+D |
| `ID_SW2` | Fa0/1-3 | Access | VLAN 22 | PCs de I+D |
| `ID_SW3` | Fa0/1-3 | Access | VLAN 22 | PCs de I+D |
| `Corp_AlaA` | Fa0/4 | Access | VLAN 12 | PC administrativa PC9 |
| `Corp_AlaA` | Fa0/3 | Access | VLAN 52 | Access Point para laptops de visitantes |
| `Corp_AlaB` | Fa0/3 | Access | VLAN 12 | PC administrativa PC8 |
| `Prod_Legacy` | Fa0/1 | Access | VLAN 32 | Hub de maquinaria Legacy |

Los puertos que conectan switches entre sí permanecen configurados como troncales con VLAN nativa `92`, según la configuración documentada en la sección 3.1.

![Imagen](/Proyecto1/Imagenes/trunk-server.png)

![Imagen](/Proyecto1/Imagenes/trunk-switch-I+D.png)

![Imagen](/Proyecto1/Imagenes/vlan-access-switches.png)

### 4.3 Tabla de Dominios de Broadcast
> *Se detalla un dominio por cada VLAN activa.*

| VLAN ID | Nombre | Descripción del Dominio de Broadcast |
| :---: | :--- | :--- |
| 12 | Gerencia | Incluye los puertos de acceso de las computadoras administrativas del Edificio Corporativo. |
| 22 | Investigacion | Incluye los puertos de acceso de las PCs de los switches de I+D. |
| 32 | Produccion | Incluye el puerto de acceso conectado al Hub de maquinaria Legacy. |
| 42 | Servidores | Incluye los puertos de acceso de los servidores del Centro de Datos. |
| 52 | Visitantes | Incluye el puerto de acceso conectado al Access Point de visitantes. |
| 92 | NATIVA | Dominio utilizado para la VLAN nativa de los enlaces troncales |

### 4.4 Tabla de Dominios de Colisión
> *Identifica cuántos dominios de colisión genera cada switch y cuál es el dominio de colisión compartido.*

En una red Ethernet con switches, cada puerto de acceso se considera un dominio de colisión independiente, salvo cuando varios dispositivos comparten un mismo medio físico en un hub o un segmento half-duplex. En esta topología, el único segmento compartido corresponde al Hub de la Planta de Producción, mientras que los switches segmentan el tráfico con VLANs y evitan la colisión entre subredes.

| Dispositivo | Puertos Activos | Cantidad de Dominios de Colisión Generados |
| :--- | :---: | :---: |
| `Core_DataCenter` | `Gig0/1-2` + `Fa0/1-4` + enlaces internos activos | **6 puertos activos / 6 dominios de colisión independientes** |
| `SW_Servidores` | `Fa0/1-4` + uplinks activos | **4 puertos de acceso + enlaces activos** |
| `ID_SW1`, `ID_SW2`, `ID_SW3` | `Fa0/1-3` + uplinks activos | **3 puertos de acceso por switch + enlaces activos** |
| `Corp_AlaA` | `Fa0/3`, `Fa0/4` + uplinks activos | **2 puertos de acceso + enlaces activos** |
| `Corp_AlaB` | `Fa0/3` + uplinks activos | **1 puerto de acceso + enlaces activos** |
| `Prod_Legacy` | `Fa0/1` + enlaces activos | **1 puerto de acceso + enlaces activos** |
| **Hub-Legacy (Planta Prod.)** | Puertos conectados a maquinaria | **1 dominio compartido** |

> En síntesis, los switches reducen los dominios de colisión al segmentar cada puerto, mientras que el Hub de la Planta de Producción mantiene un único medio compartido, afectando rendimiento por contención y colisiones.

---

## 5. Análisis del Segmento Legacy (Planta de Producción)
> *Documentación sobre el impacto que tiene el dominio de colisión compartido en Capa 1 sobre el rendimiento de la red y las medidas de contención aplicadas en el switch de acceso.*

El puerto `Fa0/1` de `Prod_Legacy` se configuró como puerto de acceso en la VLAN `32` y conecta con el Hub de maquinaria Legacy. Los dispositivos conectados al Hub comparten un único dominio de colisión, por lo que pueden producirse colisiones y disminuir el rendimiento, especialmente por el funcionamiento half-duplex del medio compartido. La segmentación mediante la VLAN `32` limita este tráfico al dominio de Producción y reduce su impacto en el resto de la red.

![Imagen](/Proyecto1/Imagenes/legacy-vlan32.png)

---

## 6. Evidencia de Pruebas
> *Capturas de pantalla ejecutando los comandos de verificación y validando el funcionamiento de la red.*

### 6.1 Prueba de conectividad intra-VLAN
**Comando a ejecutar:**
```ios
ping <IP de otra PC en la misma VLAN>
```

**Resultado esperado:** respuestas del tipo `Reply from ...` con tiempo en milisegundos.

**Lugar para la captura:**
```text
Proyecto1/Imagenes/conectividad-intra-vlan.png
```

![Imagen](/Proyecto1/Imagenes/conectividad-intra-vlan.png)

### 6.2 Prueba de aislamiento inter-VLAN
**Comando a ejecutar:**
```ios
ping <IP de un equipo en otra VLAN>
```

**Resultado esperado:** cuatro respuestas con `Request timed out`.

**Lugar para la captura:**
```text
Proyecto1/Imagenes/aislamiento-inter-vlan.png
```

![Imagen](/Proyecto1/Imagenes/aislamiento-inter-vlan.png)

### 6.3 Validación de STP
**Comando a ejecutar:**
```ios
show spanning-tree
```

**Resultado esperado:** línea `This bridge is the root` en las VLANs del dominio.

**Lugar para la captura:**
```text
Proyecto1/Imagenes/stp-root-core.png
```

![Imagen](/Proyecto1/Imagenes/stp-root-core.png)

### 6.4 Validación de EtherChannel
**Comando a ejecutar:**
```ios
show etherchannel summary
```

**Resultado esperado:** `Po1` y `Po2` con estado `(SU)` y protocolo `LACP` activo.

**Lugar para la captura:**
```text
Proyecto1/Imagenes/etherchannel-summary-core.png
Proyecto1/Imagenes/etherchannel-summary-i+d.png
```

![Imagen](/Proyecto1/Imagenes/etherchannel-summary-core.png)

![Imagen](/Proyecto1/Imagenes/etherchannel-summary-i+d.png)

### 6.5 Validación de enlaces troncales y VLAN nativa
**Comando a ejecutar:**
```ios
show interfaces trunk
```

**Resultado esperado:** puertos en `trunking` y `Native vlan` mostrando `92`.


![Imagen](/Proyecto1/Imagenes/show-interfaces-trunk.png)

### 6.6 Verificación del dominio VTP y propagación de VLANs
**Comando a ejecutar en el servidor:**
```ios
show vtp status
show vlan brief
```

**Lugar para la captura:**
```text
Proyecto1/Imagenes/vtp-status-core.png
Proyecto1/Imagenes/vlan-core.png
```

![Imagen](/Proyecto1/Imagenes/vtp-status-core.png)

![Imagen](/Proyecto1/Imagenes/vlan-core.png)

**Comando a ejecutar en un cliente VTP:**
```ios
show vtp status
show vlan brief
```

**Lugar para la captura:**
```text
Proyecto1/Imagenes/vtp-status-client.png
Proyecto1/Imagenes/vlan-cliente.png
```

![Imagen](/Proyecto1/Imagenes/vtp-status-client.png)

![Imagen](/Proyecto1/Imagenes/vlan-cliente.png)

### 6.7 Verificación de puertos de acceso y MOTD
**Comando a ejecutar:**
```ios
show running-config
show interfaces status
```

**Lugar para la captura:**
```text
Proyecto1/Imagenes/vlan-access-switches.png
Proyecto1/Imagenes/motd-core.png
```

![Imagen](/Proyecto1/Imagenes/vlan-access-switches.png)

![Imagen](/Proyecto1/Imagenes/motd-core.png)

---

## 7. Presupuesto
> *Presupuesto estimado de los equipos físicos simulados.*

| Dispositivo / Material | Cantidad | Descripción | Costo Unitario Estimado | Costo Total |
| :--- | :---: | :--- | :--- | :--- |
| Switches Catalyst | 8 | Switches de acceso y distribución con puertos Ethernet/Gigabit | Q 1,200.00 | Q 9,600.00 |
| Hub | 1 | Hub legacy de la Planta de Producción | Q 180.00 | Q 180.00 |
| Módulos de Fibra | 4 | SFP/GBIC para enlaces de fibra según la topología | Q 90.00 | Q 360.00 |
| Cableado UTP | 25 | Cables de cobre para PCs, AP y enlaces locales | Q 25.00 | Q 625.00 |
| Cableado de Fibra | 6 | Patch cords de fibra para enlaces troncales principales | Q 75.00 | Q 450.00 |
| **Total General** | | | | **Q 11,215.00** |

> El presupuesto anterior es estimado con fines de documentación y laboratorio; la red simulada en Packet Tracer está basada en equipos Cisco de nivel de acceso y distribución, con enlaces de agregado y segmentos críticos bajo fibra óptica para mayor rendimiento y estabilidad. Todos los valores se expresan en Quetzales (Q).

---

## 8. Lista de Comandos por Dispositivo
> *Detalle de todos los comandos utilizados en las configuraciones, agrupados por dispositivo.*

### `Core_DataCenter`
```ios
enable
configure terminal
hostname Core_DataCenter
banner motd #Acceso Restringido - TechPark_202302232#
vtp mode server
vtp domain Smart_3
vtp password proyecto12S2026
vlan 92
name NATIVA
vlan 12
name Gerencia
vlan 22
name Investigacion
vlan 32
name Produccion
vlan 42
name Servidores
vlan 52
name Visitantes
spanning-tree mode pvst
spanning-tree vlan 1,12,22,32,42,52,92 priority 4096
interface range gig0/1-2
channel-group 1 mode active
exit
interface port-channel 1
switchport mode trunk
switchport trunk native vlan 92
exit
interface range fa0/1-4
channel-group 2 mode active
exit
interface port-channel 2
switchport mode trunk
switchport trunk native vlan 92
exit
write memory
```

### `SW_Servidores`
```ios
enable
configure terminal
hostname SW_Servidores
banner motd #Acceso Restringido - TechPark_202302232#
vtp mode client
vtp domain Smart_3
vtp password proyecto12S2026
spanning-tree mode pvst
interface range gig0/1-2
channel-group 1 mode active
exit
interface port-channel 1
switchport mode trunk
switchport trunk native vlan 92
exit
interface range fa0/1-4
switchport mode access
switchport access vlan 42
exit
write memory
```

### `SW1-I+D`
```ios
enable
configure terminal
hostname ID_SW1
banner motd #Acceso Restringido - TechPark_202302232#
vtp mode client
vtp domain Smart_3
vtp password proyecto12S2026
spanning-tree mode pvst
interface range fa0/5-8
channel-group 2 mode active
exit
interface port-channel 2
switchport mode trunk
switchport trunk native vlan 92
exit
write memory
```

### `ID_SW2`, `ID_SW3`, `Corp_AlaA`, `Corp_AlaB` y `Prod_Legacy`
```ios
enable
configure terminal
hostname [nombre correspondiente]
banner motd #Acceso Restringido - TechPark_202302232#
vtp mode client
vtp domain Smart_3
vtp password proyecto12S2026
spanning-tree mode pvst
interface [interfaz troncal real]
switchport mode trunk
switchport trunk native vlan 92
exit
write memory
```

> Nota: sustituir los valores entre corchetes por los nombres e interfaces realmente usados en cada switch. El bloque de comandos puede ser reutilizado en todos los clientes que forman parte del dominio VTP.

### Asignación de puertos de acceso

#### `SW_Servidores` - VLAN 42
```ios
interface range fa0/1-4
switchport mode access
switchport access vlan 42
exit
write memory
```

#### `ID_SW1`, `ID_SW2` e `ID_SW3` - VLAN 22
```ios
interface range fa0/1-3
switchport mode access
switchport access vlan 22
exit
write memory
```

#### `Corp_AlaA` - VLAN 12 y VLAN 52
```ios
interface fa0/4
switchport mode access
switchport access vlan 12
exit
interface fa0/3
switchport mode access
switchport access vlan 52
exit
write memory
```

#### `Corp_AlaB` - VLAN 12
```ios
interface fa0/3
switchport mode access
switchport access vlan 12
exit
write memory
```

#### `Prod_Legacy` - VLAN 32
```ios
interface fa0/1
switchport mode access
switchport access vlan 32
exit
write memory
```
