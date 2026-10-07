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