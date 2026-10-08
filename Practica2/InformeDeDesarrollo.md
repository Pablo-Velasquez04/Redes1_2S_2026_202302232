<div align="center">

<img src="Imagenes/logoUSAC.png" width="150">

## UNIVERSIDAD DE SAN CARLOS DE GUATEMALA

### FACULTAD DE INGENIERÍA

### REDES DE COMPUTADORAS 1

<br><br>

# INFORME DE DESARROLLO

<br><br>

**Catedrático:** Ing. Pedro Pablo Hernández Ramirez

**Auxiliar:** César Fernando Sazo Quisquinay

**Nombre:** Pablo Daniel Velásquez Hernández

**Carnet:** 202302232

**Sección:** N

**Semestre:** Segundo Semestre 2026

<br><br>

</div>



# Informe De Desarrollo - Red de la Ciudad Comercial Cayalá


# Fase 1: Implementación del Backbone, Enlaces Troncales y EtherChannel

## 1. Descripción de la Implementación

Se procedió con el tendido de enlaces físicos y la configuración lógica del núcleo y la capa de distribución para la Zona 1. Para garantizar la simetría y estabilidad del protocolo LACP, se utilizó cableado cruzado (Copper Cross-over) uniendo interfaces con idénticas velocidades de transmisión (FastEthernet con FastEthernet y GigabitEthernet con GigabitEthernet).

Se crearon las VLANs administrativas de soporte (VLAN 99) y se establecieron los canales lógicos Port-Channel 1 y Port-Channel 2 en modo activo.

---

## 2. Problemas Encontrados y Soluciones Adoptadas

### Problema 1: Asimetría en la velocidad de medios de enlace y fallo de agregación LACP

| **Aspecto** | **Descripción** |
|---|---|
| **Inconveniente** | Inicialmente se conectaron interfaces FastEthernet de un switch con puertos GigabitEthernet en el extremo opuesto, ocasionando que LACP suspendiera los enlaces e imposibilitara la formación del canal lógico. |
| **Solución Adoptada** | Se reestructuró el cableado físico en Packet Tracer para asegurar enlaces simétricos en ambos extremos: Fa0/23-24 de SW_CORE_1 hacia Fa0/23-24 de SW_CORE_2, y Gig0/1-2 de SW_CORE_1 hacia Gig0/1-2 de SW_Z1_Principal. |

### Problema 2: Suspensión de puertos por inconsistencia de VLAN Nativa y error de máscara (vlan mask is different)

| **Aspecto** | **Descripción** |
|---|---|
| **Inconveniente** | Al intentar redefinir interfaces en uso en switches multicapa 3560 mediante el comando `default interface`, la consola rechazó la orden debido al modo troncal activo. Al intentar asociar las interfaces físicas al channel-group antes de igualar la VLAN nativa con el Port-Channel, Spanning Tree detectó inconsistencia de PVID (RECV_PVID_ERR) y bloqueó los puertos bajo el estado SD (Layer 2 Down). |
| **Solución Adoptada** | Se estructuró una secuencia CLI determinista: configurar la encapsulación dot1q, establecer la VLAN nativa 99 y definir la lista de VLANs permitidas directamente en las interfaces físicas primero, para posteriormente vincularlas al channel-group 1/2 mode active. Una vez reingresada la secuencia en ambos extremos y acelerada la convergencia del simulador (Fast Forward Time), el canal cambió de manera permanente al estado operativo Po1(SU) y Po2(SU). |

---

## 3. Capturas de Pantalla y Evidencias de Funcionamiento

### Imagen 1: `show etherchannel summary` en SW_CORE_1

![Captura de pantalla de la CLI de SW_CORE_1 ejecutando show etherchannel summary](Imagenes/etherchannelSummary.png)

**Descripción de la evidencia:** Muestra la correcta formación de Group 1 (Po1) y Group 2 (Po2) utilizando el protocolo LACP. Se evidencian las banderas SU (Layer 2 / In use) y la marca (P) (In port-channel) en los miembros Fa0/23, Fa0/24, Gig0/1 y Gig0/2, confirmando que los enlaces están agrupados y transmitiendo tráfico.

### Imagen 2: `show interfaces trunk`

![Captura de pantalla de la CLI ejecutando show interfaces trunk](Imagenes/interfacesTrunk1.png)

**Descripción de la evidencia:** Demuestra la presencia de las interfaces lógicas Po1 y Po2 operando bajo encapsulación 802.1q, con la VLAN Nativa asignada a la 99 y restringiendo el tráfico de datos estrictamente a las VLANs 12, 22, 32, 42, 52, 99.


# Despliegue de Enlaces Troncales de Acceso y Distribución

## 1. Descripción de la Implementación

Se procedió con la configuración de las interfaces troncales entre los switches de distribución y acceso de todas las zonas de Ciudad Cayalá. Se aseguró que cada enlace mantuviera la coherencia en la VLAN Nativa (VLAN 99) y en la lista de VLANs autorizadas para evitar discrepancias de tráfico a nivel de Capa 2.

---

## 2. Problemas Encontrados y Soluciones Adoptadas

### Problema: Puerto en estado de bloqueo temporal (Luz Naranja) entre SW_CORE_1 y SW_Z4_Principal

| **Aspecto** | **Descripción** |
|---|---|
| **Inconveniente** | Al aplicar la configuración de tronco en la interfaz Fa0/15 de SW_CORE_1, el enlace se mantuvo en color naranja por varios segundos, impidiendo la transmisión inmediata. |
| **Solución Adoptada** | Se identificó que el puerto se encontraba en el proceso estándar de convergencia de Spanning Tree (Listening / Learning). Se avanzó el tiempo en el simulador (Fast Forward Time) para permitir que el estado cambiara a Forwarding (luz verde). |

### Problema: Estado `none` en las secciones de VLANs activas de `show interfaces trunk`

| **Aspecto** | **Descripción** |
|---|---|
| **Inconveniente** | Al verificar los troncales en SW_Z4_Principal, las secciones `Vlans allowed and active` y `Vlans in spanning tree forwarding state` mostraban `none`. |
| **Solución Adoptada** | Se confirmó que esto es un comportamiento normal de la Capa 2 cuando las VLANs aún no existen localmente en la base de datos del switch ni se han propagado por VTP. Se verificó que la línea `Vlans allowed on trunk` mostrara correctamente `12,22,32,42,52,99`, validando que la sintaxis del tronco estaba correctamente aplicada a la espera de la creación de las VLANs. |

---

## 3. Capturas de Pantalla y Evidencias de Funcionamiento

### Imagen 1: `show interfaces trunk` en SW_Z4_Principal

![Captura de la CLI de SW_Z4_Principal ejecutando show interfaces trunk](Imagenes/sw_z4_show_interfaces_trunk.png)

**Descripción de la evidencia:** Muestra las interfaces Fa0/1, Fa0/2 y Fa0/24 operando en modo trunking bajo encapsulación 802.1q, confirmando que la VLAN Nativa está asignada a la 99 y que únicamente las VLANs 12, 22, 32, 42, 52, 99 están permitidas.

### Imagen 2: `show interfaces trunk` en SW_Z1_Principal

![Captura de la CLI de SW_Z1_Principal ejecutando show interfaces trunk](Imagenes/sw_z1_show_interfaces_trunk.png)

**Descripción de la evidencia:** Demuestra los enlaces troncales activos hacia los switches de acceso (Fa0/1, Fa0/2, Fa0/3) y el canal Po2 hacia el Core, confirmando que la VLAN 99 figura como activa en el dominio de administración.


## Resumen: Configuración de VTP (Dominio 202302232)

Se configuró el protocolo **VTP (VLAN Trunking Protocol)** con el objetivo de centralizar la administración de VLANs en la red de Ciudad Cayalá, utilizando un dominio común basado en el carnet del estudiante (`202302232`) y una contraseña compartida (`redes1`).

**Switches Servidores (VTP Server):** Se configuraron explícitamente como servidores `SW_CORE_1` y los switches principales de cada zona (`SW_Z1_Principal`, `SW_Z2_Retail`, `SW_Z3_Cine`, `SW_Z4_Principal` y `SW_Z5_Seguridad`), aplicando los comandos `vtp domain 202302232`, `vtp password redes1` y `vtp mode server`.

**Switches Clientes (VTP Client):** Se configuraron como clientes el Core de respaldo `SW_CORE_2` y todos los switches de acceso (`SW_Z1_Acceso1`, `SW_Z1_Acceso2`, `SW_Z1_Acceso3`, `SW_Z4_Acceso1`, `SW_Z4_Acceso2`), aplicando los comandos `vtp domain 202302232`, `vtp password redes1` y `vtp mode client`. Estos switches no pueden crear, eliminar ni modificar VLANs; únicamente reciben las actualizaciones del servidor.

**Verificación:** Se utilizó el comando `show vtp status` en los switches clientes, confirmando que el `VTP Operating Mode` es `Client`, el `VTP Domain Name` es `202302232` y el `Configuration Revision` permanece en `0` hasta la creación de las VLANs en el paso siguiente.

Este procedimiento garantiza que el dominio VTP quede correctamente definido y que todos los switches clientes queden sincronizados con el servidor principal.

![Captura de la CLI de SW_CORE_2 con show vtp status](Imagenes/core2_showVTPstatus1.png)

# Creación de la Base de Datos de VLANs y Verificación de Estado Troncal

## 1. Descripción de la Implementación

Se ejecutó la creación centralizada de la base de datos de VLANs en el switch principal de distribución SW_CORE_1. La infraestructura respondió sincronizando las bases de datos locales en el resto de los dispositivos, lo cual incrementó la revisión de configuración de VTP a 20 y elevó el número total de VLANs existentes a 12 (5 por defecto de fábrica + 7 administradas). Esto permitió activar inmediatamente la transmisión de tráfico etiquetado 802.1Q a través de los enlaces troncales físicos y agregados (Po2).

## 2. Problemas Encontrados y Soluciones Adoptadas

### Problema: Enlaces Troncales inactivos para VLANs específicas en la fase previa

| **Aspecto** | **Descripción** |
|---|---|
| **Inconveniente** | Previo a la creación de las VLANs, la verificación mediante `show interfaces trunk` reportaba el parámetro `none` en las secciones `Vlans allowed and active in management domain` y `Vlans in spanning tree forwarding state`, a pesar de contar con la regla `allowed vlan` bien configurada. |
| **Solución Adoptada** | Al inyectar la base de datos de VLANs centralmente y propagarse vía VTP, el motor de conmutación de los switches reconoció la existencia de las IDs 12, 22, 32, 42, 52, 99, pasando las interfaces automáticamente al estado activo y de reenvío de tramas (forwarding state). |

## 3. Capturas de Pantalla y Evidencias de Funcionamiento

### Imagen 1: `show vlan brief` en SW_Z1_Principal

![Captura de la CLI de SW_Z1_Principal ejecutando show vlan brief](Imagenes/sw1_showVLANbrief1.png)

**Descripción de la evidencia:** Se comprueba que el switch ha registrado satisfactoriamente en estado `active` todas las VLANs correspondientes a la topología (Z1_Bancos, Z2_Retail, Z3_Cine, Z4_Food, Z5_Seguridad, ADMIN y BLACKHOLE).

### Imagen 2: `show vtp status` en SW_Z1_Principal

![Captura de la CLI de SW_Z1_Principal ejecutando show vtp status](Imagenes/sw1_showVTPstatus1.png)

**Descripción de la evidencia:** Muestra el estado del protocolo VTP confirmando un Configuration Revision en 20 y un total de 12 VLANs existentes, validando que la propagación desde el VTP Server se realizó de forma consistente sin pérdidas de sincronización.

### Imagen 3: `show interfaces trunk` en SW_Z1_Principal

![Captura de la CLI de SW_Z1_Principal ejecutando show interfaces trunk](Imagenes/sw1_showInterfacesTrunk1.png)

**Descripción de la evidencia:** Muestra que las interfaces Po2, Fa0/1, Fa0/2 y Fa0/3 tienen activas y en estado de conmutación Spanning Tree (Forwarding state and not pruned) todas las VLANs requeridas (12, 22, 32, 42, 52, 99).




# Implementación de Rapid PVST+ y Control Jerárquico de Root Bridges

## 1. Descripción de la Implementación

Se migró la topología completa del modo Spanning Tree tradicional 802.1D al modo de convergencia rápida Rapid PVST+ (rstp). Se forzó manualmente a SW_CORE_1 a convertirse en el Root Bridge de todas las VLANs activas (12, 22, 32, 42, 52, 99) mediante el establecimiento de su prioridad en 4096. Asimismo, se aseguró la alta disponibilidad configurando a SW_CORE_2 con prioridad 8192 como nodo de respaldo automático.

## 2. Problemas Encontrados y Soluciones Adoptadas

### Problema: Tiempos de bloqueo prolongados (30-50s) durante fallos de enlace en STP por defecto

| **Aspecto** | **Descripción** |
|---|---|
| **Inconveniente** | Con el protocolo Spanning Tree estándar (802.1D), la conmutación entre puertos activos y bloqueados demoraba hasta 50 segundos, generando pérdida de paquetes prolongada. |
| **Solución Adoptada** | Se activó de manera global el comando `spanning-tree mode rapid-pvst` en todos los switches, reduciendo los estados de transición a menos de 2 segundos mediante mecanismos de propuesta/acuerdo (proposal/agreement). |

### Problema: Elección impredecible del Root Bridge basada en la dirección MAC más baja

| **Aspecto** | **Descripción** |
|---|---|
| **Inconveniente** | Si no se especifican las prioridades, la red selecciona como Root Bridge el switch con la dirección MAC más baja, el cual podría ser un switch de acceso de menor capacidad. |
| **Solución Adoptada** | Se asignó explícitamente la prioridad 4096 a SW_CORE_1, garantizando centralización del tráfico, rendimiento óptimo del backend y que todas sus interfaces permanezcan en estado designado y reenvío (Desg FWD). |

## 3. Capturas de Pantalla y Evidencias de Funcionamiento

### Imagen 1: `show spanning-tree vlan 12` en SW_CORE_1

![Captura de la CLI de SW_CORE_1 ejecutando show spanning-tree vlan 12](Imagenes/sw1_spanning-tree.png)

**Descripción de la evidencia:** Muestra la leyenda explícita `This bridge is the root`, confirmando una prioridad de 4108 (4096 + 12) con la dirección MAC 000A.411A.5535. Todas sus interfaces físicas e interfaces de canal (Po1, Po2) se visualizan en estado designado y reenvío (Desg FWD).

### Imagen 2: `show spanning-tree vlan 12` en SW_CORE_2

![Captura de la CLI de SW_CORE_2 ejecutando show spanning-tree vlan 12](Imagenes/sw2_spanning-tree.png)

**Descripción de la evidencia:** Comprueba la función de Root Secundario con una prioridad asignada de 8204 (8192 + 12). Identifica correctamente a SW_CORE_1 como el Root ID a través de su interfaz Po1 (Root Port) con costo 12.

### Imagen 3: `show spanning-tree vlan 12` en SW_Z4_Acceso2

![Captura de la CLI de SW_Z4_Acceso2 ejecutando show spanning-tree vlan 12](Imagenes/sw_z4_acceso2_spanning-tree.png)

**Descripción de la evidencia:** Demuestra el comportamiento de un switch de acceso operando en modo rstp, con la prioridad por defecto de 32780 (32768 + 12), reconociendo a SW_CORE_1 como la raíz del árbol a través del puerto troncal Fa0/1 (Root FWD) con costo acumulado 38.