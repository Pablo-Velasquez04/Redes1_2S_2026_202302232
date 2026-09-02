# Manual Técnico - Tarea #3: Configuración de VLANs y VTP en Cisco Packet Tracer

**Universidad San Carlos de Guatemala**  
**Facultad de Ingeniería**  
**Escuela de Ingeniería en Ciencias y Sistemas**  
**Laboratorio de Redes de Computadoras 1 - Sección A**  
**Semestre:** Segundo Semestre 2026  

---

## Información del Estudiante
- **Nombre:** Pablo Daniel Velásquez Hernández
- **Carnet:** 202302232
- **Fecha de Entrega:** 3/09/2026


# 1. Diseño de la Topología

## 1.1. Descripción del Diseño e Interconexión
Para la elaboración de la topología en Cisco Packet Tracer se agregaron cuatro switches Cisco Catalyst 2960: un switch distribuidor central denominado **Switch0** y tres switches de acceso denominados **ADMIN**, **MERCA** y **VENTAS**. Asimismo, se incorporaron seis computadoras de escritorio (PCs) divididas en pares para cada uno de los switches de acceso.

El procedimiento realizado se resume de la siguiente manera:
1. **Despliegue de Switches y Dispositivos Finales:** Se ubicó el switch central (`Switch0`) en el núcleo de la topología y se distribuyeron los switches de acceso (`ADMIN`, `MERCA`, `VENTAS`) junto a sus dos respectivas PCs finales.
2. **Interconexión entre Switches:** Se conectaron los switches de acceso hacia el switch central mediante **cable cruzado** (*Copper Cross-Over*), preparando los medios físicos para la posterior configuración de enlaces troncales (*Trunk*).
3. **Conexión de Equipos Finales:** Se conectaron las seis PCs a los puertos FastEthernet correspondientes de sus switches de acceso mediante **cable directo** (*Copper Straight-Through*).

---

### 1.b. Conexión de Enlaces Troncales (Trunk) entre Switches

Para garantizar que el tráfico etiquetado de las diferentes VLANs pueda circular entre los switches de acceso y el switch central (**Switch0**), se configuraron manualmente los enlaces de interconexión en modo troncal (*Trunk*). Aunque los switches pueden negociar el enlace de forma automática, forzar la configuración manualmente asegura la estabilidad de la red.

---

#### 1. Configuración del Switch Central (`Switch0`)
Se ingresó a la CLI de `Switch0` y se utilizaron los comandos de rango e interfaz para configurar los puertos conectados hacia los switches de acceso en modo troncal:

```cisconetworking
enable
configure terminal

! Configuración de puertos Gigabit hacia ADMIN y MERCA
interface range gigabitEthernet 0/1 - 2
 switchport mode trunk
 exit

! Configuración de puerto FastEthernet hacia VENTAS
interface fastEthernet 0/24
 switchport mode trunk
 exit
```

#### 2. Configuración en los Switches de Acceso
**Switch ADMIN:**

```cisconetworking
enable
configure terminal
interface gigabitEthernet 0/1
 switchport mode trunk
 exit
```

**Switch MERCA:**

```cisconetworking
enable
configure terminal
interface gigabitEthernet 0/1
 switchport mode trunk
 exit
```

**Switch VENTAS:**

```cisconetworking
enable
configure terminal
interface fastEthernet 0/24
 switchport mode trunk
 exit
```

#### 3. Comprobación de Enlaces Troncales
Para verificar que las interfaces cambiaron correctamente de estado y que los enlaces troncales están operativos, se ejecutó el siguiente comando en el modo `EXEC Privilegiado` (`#`) del switch central (`Switch0`):

```cisconetworking
Switch0# show interfaces trunk
```

#### Evidencia de Verificación de Enlaces Troncales
![Topología de Red Armada](/Tarea3/Imagenes/Trunking.png)

---

## 1.2. Cuadro de Conexiones de Red

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino | Tipo de Cable | Tipo de Enlace / Propósito | Descripción de lo Realizado |
| :--- | :---: | :--- | :---: | :--- | :--- | :--- |
| `Switch0` (Central) | `Gig0/1` | `ADMIN` (Acceso) | `Gig0/1` | Cable Cruzado | Enlace Troncal (*Trunk*) | Conexión física entre el switch central y el switch de ADMIN. |
| `Switch0` (Central) | `Gig0/2` | `MERCA` (Acceso) | `Gig0/1` | Cable Cruzado | Enlace Troncal (*Trunk*) | Conexión física entre el switch central y el switch de MERCA. |
| `Switch0` (Central) | `Fa0/24` | `VENTAS` (Acceso) | `Fa0/24` | Cable Cruzado | Enlace Troncal (*Trunk*) | Conexión física entre el switch central y el switch de VENTAS. |
| `ADMIN` | `Fa0/1` | `PC-ADMIN-1` | `FastEthernet0` | Cable Directo | Enlace de Acceso (*Access*) | Conexión de la primera PC al switch del departamento ADMIN. |
| `ADMIN` | `Fa0/2` | `PC-ADMIN-2` | `FastEthernet0` | Cable Directo | Enlace de Acceso (*Access*) | Conexión de la segunda PC al switch del departamento ADMIN. |
| `MERCA` | `Fa0/1` | `PC-MERCA-1` | `FastEthernet0` | Cable Directo | Enlace de Acceso (*Access*) | Conexión de la primera PC al switch del departamento MERCA. |
| `MERCA` | `Fa0/2` | `PC-MERCA-2` | `FastEthernet0` | Cable Directo | Enlace de Acceso (*Access*) | Conexión de la segunda PC al switch del departamento MERCA. |
| `VENTAS` | `Fa0/1` | `PC-VENTAS-1` | `FastEthernet0` | Cable Directo | Enlace de Acceso (*Access*) | Conexión de la primera PC al switch del departamento VENTAS. |
| `VENTAS` | `Fa0/2` | `PC-VENTAS-2` | `FastEthernet0` | Cable Directo | Enlace de Acceso (*Access*) | Conexión de la segunda PC al switch del departamento VENTAS. |

---

## 1.3. Evidencia de la Topología Armada en Packet Tracer

![Topología de Red Armada](/Tarea3/Imagenes/Topologia.png)


# 2. Configuración del Protocolo VTP

## 2.1. Asignación de Roles y Dominio VTP
Para automatizar y centralizar la administración de las VLANs en la red, se configuró el protocolo VTP asignando el dominio **`usac`** en todos los switches. Cada equipo fue configurado con un rol específico según los requerimientos del diseño:

- **Switch Central (`Switch0`):** Configurado como **VTP Server** (administra y propaga la base de datos de VLANs).
- **Switches `ADMIN` y `MERCA`:** Configurados como **VTP Client** (reciben y aplican las VLANs anunciadas por el servidor).
- **Switch `VENTAS`:** Configurado como **VTP Transparent** (ignora las VLANs del servidor para su propia base de datos, pero reenvía los anuncios VTP por sus enlaces troncales).

---

### Scripts de Configuración Ejecutados

#### 1. Switch Central (`Switch0` - Servidor VTP)
```cisconetworking
enable
configure terminal
vtp domain usac
vtp mode server
exit
```

#### 2. Switch ADMIN (`ADMIN` - Cliente VTP)
```cisconetworking
enable
configure terminal
vtp domain usac
vtp mode client
exit
```

#### 3. Switch MERCA (`MERCA` - Cliente VTP)
```cisconetworking
enable
configure terminal
vtp domain usac
vtp mode client
exit
```

#### 4. Switch VENTAS (`VENTAS` - Transparente VTP)
```cisconetworking
enable
configure terminal
vtp domain usac
vtp mode transparent
exit
```

## 2.2. Verificación del Estado de VTP (`show vtp status`)

Para validar la correcta aplicación del dominio `usac` y el modo de operación en cada dispositivo, se ejecutó el comando de verificación en el modo `EXEC Privilegiado` (`#`) de cada switch:

```cisconetworking
show vtp status
```

### Evidencias de Verificación por Switch

#### 1. Switch Central (`Switch0`)

- **Parámetros Verificados:** `VTP Domain Name: usac` | `VTP Operating Mode: Server`
![Verificación](/Tarea3/Imagenes/VTP-Switch0.png)

#### 2. Switch ADMIN (`ADMIN`)

- **Parámetros Verificados:** `VTP Domain Name: usac` | `VTP Operating Mode: Client`
![Verificación](/Tarea3/Imagenes/VTP-ADMIN.png)

#### 3. Switch MERCA (`MERCA`)

- **Parámetros Verificados:** `VTP Domain Name: usac` | `VTP Operating Mode: Client`
![Verificación](/Tarea3/Imagenes/VTP-MERCA.png)

#### 4. Switch VENTAS (`VENTAS`)

- **Parámetros Verificados:** `VTP Domain Name: usac` | `VTP Operating Mode: Transparent`
![Verificación](/Tarea3/Imagenes/VTP-VENTAS.png)

---

# 3. Creación de VLANs y Asignación de Puertos de Acceso

## 3.1. Creación de VLANs en el Switch Servidor (`Switch0`)

Se crearon las tres VLANs requeridas (**ADMIN**, **MERCA** y **VENTAS**) en el switch servidor (`Switch0`). Se asignaron las siguientes IDs de VLAN:
- **VLAN 10:** ADMIN
- **VLAN 20:** MERCA
- **VLAN 30:** VENTAS

### Script Ejecutado en `Switch0`:
```cisconetworking
enable
configure terminal
vlan 10
 name ADMIN
vlan 20
 name MERCA
vlan 30
 name VENTAS
exit
```

## 3.2. Verificación de Propagación VTP en Switches Clientes (`ADMIN` y `MERCA`)
Debido al funcionamiento del protocolo VTP en modo **Client**, las VLANs creadas en el servidor se propagan automáticamente hacia los switches clientes sin necesidad de crearlas manualmente en ellos.

Para verificar la recepción de las VLANs, se ejecutó en ambos switches el comando:

```cisconetworking
show vlan brief
```

### Evidencias de Propagación (`show vlan brief`)

#### 1. Switch ADMIN (Cliente VTP)
![Verificación](/Tarea3/Imagenes/clienteVLAN-ADMIN.png)

#### 2. Switch MERCA (Cliente VTP)
![Verificación](/Tarea3/Imagenes/VLAN-MERCA.png)

## 3.3. Caso Especial: Configuración en Switch Transparente (`VENTAS`)
El switch `VENTAS`, al estar configurado en modo **VTP Transparent**, no guarda ni sincroniza las VLANs recibidas del servidor en su propia tabla de conmutación. Por esta razón, se procedió a crear manualmente las tres VLANs en su CLI local para que los dispositivos finales puedan operar correctamente.

### Script Ejecutado en `VENTAS`:
```cisconetworking
enable
configure terminal
vlan 10
 name ADMIN
vlan 20
 name MERCA
vlan 30
 name VENTAS
exit
```

### Evidencia: Verificación de VLANs en Switch Transparente

#### Switch VENTAS (`show vlan brief`)
![Verificación](/Tarea3/Imagenes/VLAN-VENTAS.png)

---

# 4. Asignación de Puertos de Acceso

## 4.1. Configuración de Puertos en Modo Acceso (`mode access`)
Para asociar los dispositivos finales (PCs) con sus respectivas VLANs, se configuraron las interfaces del rango `FastEthernet 0/1 - 2` de cada switch en modo acceso y se asignaron a su VLAN departamental correspondiente.

### 1. Switch ADMIN (Asignación a VLAN 10 - ADMIN)
```cisconetworking
enable
configure terminal
interface range fastEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 10
 exit
```

### 2. Switch MERCA (Asignación a VLAN 20 - MERCA)
```cisconetworking
enable
configure terminal
interface range fastEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 20
 exit
```

### 3. Switch VENTAS (Asignación a VLAN 30 - VENTAS)
```cisconetworking
enable
configure terminal
interface range fastEthernet 0/1 - 2
 switchport mode access
 switchport access vlan 30
 exit
```

## 4.2. Verificación de Asignación de Puertos
Para comprobar que las interfaces `Fa0/1` y `Fa0/2` quedaron vinculadas a sus respectivas VLANs, se volvió a ejecutar el comando de comprobación en cada switch de acceso:

```cisconetworking
show vlan brief
```

### Evidencias de Asignación de Puertos por Switch

#### 1. Switch ADMIN (Puertos Fa0/1 y Fa0/2 asignados a VLAN 10)
![Verificación](/Tarea3/Imagenes/Puertos-ADMIN.png)

#### 2. Switch MERCA (Puertos Fa0/1 y Fa0/2 asignados a VLAN 20)
![Verificación](/Tarea3/Imagenes/Puertos-MERCA.png)

#### 3. Switch VENTAS (Puertos Fa0/1 y Fa0/2 asignados a VLAN 30)
![Verificación](/Tarea3/Imagenes/Puertos-VENTAS.png)

---

# 5. Asignación de Direcciones IP y Pruebas de Conectividad

## 5.1. Esquema de Direccionamiento IP
Para realizar las pruebas de comunicación, se estableció un direccionamiento IP estático utilizando subredes de Clase C con máscara predeterminada `/24` (`255.255.255.0`). Cada subred se relacionó directamente con el identificador de su correspondiente VLAN para mantener orden y claridad en la topología.

| Departamento / VLAN | ID VLAN | Red | Dispositivo | Puerto Switch | Dirección IP | Máscara de Subred |
| :--- | :---: | :---: | :--- | :---: | :---: | :---: |
| **ADMIN** | `10` | `192.168.10.0/24` | `PC-ADMIN-1` | `Fa0/1` | `192.168.10.1` | `255.255.255.0` |
| **ADMIN** | `10` | `192.168.10.0/24` | `PC-ADMIN-2` | `Fa0/2` | `192.168.10.2` | `255.255.255.0` |
| **MERCA** | `20` | `192.168.20.0/24` | `PC-MERCA-1` | `Fa0/1` | `192.168.20.1` | `255.255.255.0` |
| **MERCA** | `20` | `192.168.20.0/24` | `PC-MERCA-2` | `Fa0/2` | `192.168.20.2` | `255.255.255.0` |
| **VENTAS** | `30` | `192.168.30.0/24` | `PC-VENTAS-1` | `Fa0/1` | `192.168.30.1` | `255.255.255.0` |
| **VENTAS** | `30` | `192.168.30.0/24` | `PC-VENTAS-2` | `Fa0/2` | `192.168.30.2` | `255.255.255.0` |

---

## 5.2. Procedimiento de Configuración IP en los Dispositivos Finales
En cada una de las 6 computadoras dentro de Cisco Packet Tracer, se llevó a cabo el siguiente procedimiento de asignación estática:
1. Selección de la PC y acceso a la pestaña **Desktop**.
2. Ingreso al módulo **IP Configuration**.
3. Marcación de la opción **Static**.
4. Asignación de la **IPv4 Address** según la tabla de direccionamiento.
5. Asignación de la **Subnet Mask** (`255.255.255.0`).

*(Nota: No se configuró dirección de Gateway Predeterminado / Default Gateway debido a que las pruebas se realizan de forma local a nivel de Capa 2 y no se cuenta con un dispositivo de enrutamiento).*

---

## 5.3. Pruebas de Conectividad (Ping)

Para verificar el cumplimiento del diseño de red, se ejecutaron pruebas de conectividad mediante el comando `ping` desde la terminal (**Command Prompt**) de los equipos finales.

### Prueba A: Conectividad Intra-VLAN (Misma VLAN - ÉXITO ESPERADO)
Se realizó una prueba de comunicación local enviando paquetes desde `PC-ADMIN-1` (`192.168.10.1`) hacia `PC-ADMIN-2` (`192.168.10.2`), ambas pertenecientes a la **VLAN 10 (ADMIN)**.

**Comando ejecutado en `PC-ADMIN-1`:**
```text
ping 192.168.10.2
```

**Salida de la terminal:**

```
Pinging 192.168.10.2 with 32 bytes of data:
Reply from 192.168.10.2: bytes=32 time<1ms TTL=128
Reply from 192.168.10.2: bytes=32 time<1ms TTL=128
Reply from 192.168.10.2: bytes=32 time<1ms TTL=128
Reply from 192.168.10.2: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.10.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

**Resultado:** Exitoso (0% de pérdida de paquetes). Se confirma la correcta conmutación de tramas a nivel de Capa 2 a través del enlace troncal entre switches.

#### Evidencia: Ping Exitoso entre PCs de la Misma VLAN
![Ping Exitoso](/Tarea3/Imagenes/Ping-Exito.png)

### Prueba B: Conectividad Inter-VLAN (Distinta VLAN - FALLO ESPERADO)
Se probó la transmisión de paquetes desde `PC-ADMIN-1` (`192.168.10.1` - VLAN 10) hacia `PC-MERCA-1` (`192.168.20.1` - VLAN 20) para comprobar el aislamiento del tráfico entre subredes.

**Comando ejecutado en `PC-ADMIN-1`:**

```text
ping 192.168.20.1
```

**Salida de la terminal:**

```
Pinging 192.168.20.1 with 32 bytes of data:
Request timed out.
Request timed out.
Request timed out.
Request timed out.

Ping statistics for 192.168.20.1:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

**Resultado:** Fallido (100% de pérdida de paquetes).

**Justificación Técnica:** El tráfico de broadcast y unicast queda confinado dentro de cada dominio de difusión definido por la VLAN. Al no existir un elemento de Capa 3 (Router o Switch Capa 3) que realice el enrutamiento Inter-VLAN, la separación lógica funciona correctamente impidiendo la comunicación entre distintas áreas.

#### Evidencia: Ping Fallido entre PCs de Distintas VLANs
![Ping Fallido](/Tarea3/Imagenes/Ping-Fallo.png)