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
> *Explicación técnica de las decisiones de topología, ubicación de la redundancia y del enlace de mayor capacidad hacia el Centro de Datos.*

La red utiliza una organización jerárquica con `Core_DataCenter` como punto central de interconexión. Esta distribución permite separar las áreas de la organización, centralizar los servicios del Centro de Datos y facilitar la administración de la infraestructura.

Los enlaces redundantes y la agregación mediante EtherChannel se documentarán después de verificar cuáles fueron configurados en el archivo de Packet Tracer.

**Pendiente de completar:** describir la ubicación exacta de la redundancia y señalar el enlace de mayor capacidad hacia el Centro de Datos.

### 2.2 Medios de Transmisión
> *Justificación técnica del medio de transmisión elegido (cobre o fibra óptica) para cada enlace, considerando distancia y ancho de banda. (Recuerda que en el archivo de Packet Tracer también deben estar etiquetados).*

**Pendiente de completar con base en el archivo `.pkt`:** registrar cada enlace, su medio de transmisión y la justificación por distancia, ancho de banda e interferencia.

> [Insertar aquí una captura donde se observen las etiquetas de cobre y fibra óptica en la topología.]

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
**Versión Utilizada:** [Completar después de verificar la configuración STP en Packet Tracer]

* **Selección del Root Bridge:**
  * **VLAN [ID]:** [Completar]
  * **Switch Root Bridge:** [Completar]
  * **Justificación técnica:** [Completar según la topología y la salida de `show spanning-tree`.]
* **Evidencia:**  
  > [Insertar aquí la captura de `show spanning-tree` donde se observe el Root Bridge.]

### 3.3 EtherChannel
**Protocolo Utilizado:** [Completar después de verificar si se utilizó LACP o PAgP]

* **Enlaces agrupados:** [Completar con las interfaces y el número de Port-Channel.]
* **Justificación técnica:** [Explicar en qué enlaces se aplicó la agregación y por qué se consideró necesario.]
* **Evidencia:**  
  > [Insertar aquí la captura de `show etherchannel summary`.]

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

| Dispositivo | Puertos Activos | Cantidad de Dominios de Colisión Generados |
| :--- | :---: | :---: |
| `Core_DataCenter` | [Completar puertos activos] | [Completar] |
| `SW_Servidores` | Fa0/1-4 y enlaces activos | [Completar según puertos físicos activos] |
| `ID_SW1`, `ID_SW2`, `ID_SW3` | Fa0/1-3 y enlaces activos | [Completar según puertos físicos activos] |
| `Corp_AlaA` | Fa0/3, Fa0/4 y enlaces activos | [Completar según puertos físicos activos] |
| `Corp_AlaB` | Fa0/3 y enlaces activos | [Completar según puertos físicos activos] |
| `Prod_Legacy` | Fa0/1 y enlaces activos | [Completar según puertos físicos activos] |
| **Hub-Legacy (Planta Prod.)** | [Completar puertos conectados] | **1 dominio compartido** |

---

## 5. Análisis del Segmento Legacy (Planta de Producción)
> *Documentación sobre el impacto que tiene el dominio de colisión compartido en Capa 1 sobre el rendimiento de la red y las medidas de contención aplicadas en el switch de acceso.*

El puerto `Fa0/1` de `Prod_Legacy` se configuró como puerto de acceso en la VLAN `32` y conecta con el Hub de maquinaria Legacy. Los dispositivos conectados al Hub comparten un único dominio de colisión, por lo que pueden producirse colisiones y disminuir el rendimiento, especialmente por el funcionamiento half-duplex del medio compartido. La segmentación mediante la VLAN `32` limita este tráfico al dominio de Producción.

![Imagen](/Proyecto1/Imagenes/legacy-vlan32.png)

---

## 6. Evidencia de Pruebas
> *Capturas de pantalla ejecutando los comandos de verificación.*

**Prueba: `show spanning-tree`**
> [Insertar captura pendiente después de configurar y verificar STP.]

**Prueba: `show etherchannel summary`**
> [Insertar captura pendiente después de configurar y verificar EtherChannel.]

**Prueba: `show interfaces trunk`**
> [Insertar captura donde se observe la VLAN nativa 92 en los enlaces troncales.]

**Verificación de VLANs en el servidor VTP:**
```ios
show vlan brief
```

![Imagen](/Proyecto1/Imagenes/vlan-core.png)

**Verificación de VLANs propagadas en un switch cliente:**
```ios
show vlan brief
```

![Imagen](/Proyecto1/Imagenes/vlan-cliente.png)

**Verificación de puertos de acceso:**
![Imagen](/Proyecto1/Imagenes/vlan-access-switches.png)

**Pruebas adicionales de las fases completadas:**

> [Insertar captura de `show vtp status` en `Core_DataCenter`.]
>
> [Insertar captura de `show vtp status` en un switch cliente.]
>
> [Insertar captura del MOTD al acceder a uno de los switches.]

---

## 7. Presupuesto
> *Presupuesto estimado de los equipos físicos simulados.*

| Dispositivo / Material | Cantidad | Descripción | Costo Unitario Estimado | Costo Total |
| :--- | :---: | :--- | :--- | :--- |
| Switches | 8 | [Modelo utilizado] | [Costo] | [Total] |
| Hub | 1 | [Modelo utilizado] | [Costo] | [Total] |
| Módulos de Fibra | [Completar] | [SFP Transceiver, si aplica] | [Costo] | [Total] |
| Cableado UTP | [Completar] | [Bobina o cantidad de cables] | [Costo] | [Total] |
| Cableado de Fibra | [Completar] | [Patch cord multimodo, si aplica] | [Costo] | [Total] |
| **Total General** | | | | **[Total Final]** |

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
interface range [interfaces troncales reales]
switchport mode trunk
switchport trunk native vlan 92
exit
write memory
```

### `SW_Servidores`, `ID_SW1`, `ID_SW2`, `ID_SW3`, `Corp_AlaA`, `Corp_AlaB` y `Prod_Legacy`
```ios
enable
configure terminal
hostname [nombre correspondiente]
banner motd #Acceso Restringido - TechPark_202302232#
vtp mode client
vtp domain Smart_3
vtp password proyecto12S2026
interface [interfaz troncal real]
switchport mode trunk
switchport trunk native vlan 92
exit
write memory
```

> Nota: sustituir los valores entre corchetes por los nombres e interfaces realmente usados. No agregar comandos de STP, VLAN de usuarios o EtherChannel hasta documentar esas fases.

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