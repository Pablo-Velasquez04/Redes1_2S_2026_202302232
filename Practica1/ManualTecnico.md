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



# Manual Técnico - QuetzalDev S.A.

## 1. Inventario de equipos
Para la implementación de esta red, se utilizarán los siguientes equipos y materiales en base al plano de distribución:

*   **Computadoras:** 30 PCs de escritorio y 12 Laptops.
*   **Servidores:** 6 servidores en total (3 en el MDF, 1 en Recepción, 1 en Diseño y 1 en Backend)[cite: 3].
*   **Switches:** 9 switches en total (1 switch principal en el MDF y 8 switches secundarios, uno por cada cuarto/departamento)[cite: 3].
*   **Tomas de red:** Cajas rosetas unitarias, dobles y triples instaladas en los escritorios y paredes según la cantidad de equipos por departamento.
*   **Patch Panels:** 1 panel principal en el MDF y paneles pequeños en cada departamento para organizar los cables.
*   **Racks y Gabinetes:** 1 Rack de piso principal (para el MDF) y gabinetes de pared pequeños en cada cuarto para resguardar los switches.
*   **Canalización:** Tramos de escalerilla metálica abierta para la ruta del pasillo central.

## 2. Justificación de la ubicación del cuarto de telecomunicaciones (MDF)
Decidí colocar el Cuarto de Telecomunicaciones (MDF) dentro de la habitación etiquetada como "Data Center"[cite: 3]. Aunque las instrucciones sugerían buscar el punto más céntrico del edificio para ahorrar cable[cite: 3], me pareció mucho más lógico y realista aprovechar un cuarto que ya está destinado para equipos. Este espacio garantiza una mayor seguridad física (acceso restringido) para el switch principal y los 3 servidores alojados ahí, lo cual compensa el tener que tirar unos metros extra de cable troncal por el pasillo.

## 3. Justificación de la topología física seleccionada
Para todos los departamentos seleccioné la **Topología en Estrella**[cite: 3]. 
La elegí porque es la forma más segura y práctica de conectar los equipos a nivel de oficina. Con este diseño, cada computadora tiene su propio cable directo al switch del cuarto. La gran ventaja es su tolerancia a fallos[cite: 3]: si a un usuario se le rompe su cable o se desconecta su laptop, el resto del departamento sigue teniendo red sin ningún problema. Además, es muy escalable; si la empresa contrata a alguien más, solo instalo un nuevo cable desde el switch al nuevo escritorio sin apagar la red.

## 4. Tipo y categoría de cable utilizado por segmento
A continuación detallo el medio de transmisión que elegí para cada parte de la red y por qué[cite: 3]:

| Segmento de Red | Tipo/Categoría de Cable | Justificación de la elección |
| :--- | :--- | :--- |
| **Cableado Horizontal** (De las PCs al switch del cuarto) | **Cobre - UTP Categoría 6** | Es el estándar actual para oficinas. Nos da velocidades de hasta 1 Gbps, lo cual es más que suficiente para que las computadoras trabajen rápido. Como las distancias dentro de cada cuarto son muy cortas (mucho menores al límite de 90 metros), este cable funciona perfecto y es económico de instalar. |
| **Cableado Troncal** (Del switch de cada cuarto al MDF) | **Fibra Óptica (Multimodo OM3)** | Decidí usar fibra óptica para unir los cuartos con el cerebro principal. Como por estos cables va a pasar de golpe toda la información de un departamento entero hacia los servidores principales, necesitamos muchísimo ancho de banda. La fibra evita cuellos de botella y nos asegura que la red no se ponga lenta aunque todos estén descargando cosas al mismo tiempo. |

## 5. Tabla de distancias estimadas y cálculo de bobinas
Para calcular la cantidad de cable UTP necesario para el cableado horizontal (las líneas verdes), estimé un promedio de metros por cada punto de red dentro de los cuartos. Como el edificio no es tan grande y cada departamento tiene su propio switch, las distancias de cableado son cortas.

| Concepto | Cálculo / Valor |
| :--- | :--- |
| **Total de puntos de red** | 48 puntos (42 computadoras + 6 servidores)[cite: 3]. |
| **Distancia promedio por punto** | 15 metros (contemplando bajadas por la pared y holgura). |
| **Total de cable horizontal estimado** | 720 metros (48 puntos * 15 metros). |
| **Metros por bobina estándar** | 305 metros[cite: 3]. |
| **Cálculo de bobinas requeridas** | 720 / 305 = 2.36 bobinas. |
| **Total a comprar** | **3 bobinas de cable UTP Categoría 6**. |

## 6. Justificación de equipo activo y dimensionamiento
*   **Switches de departamento:** Se utilizó un switch en cada cuarto para concentrar las conexiones locales. Esto evita tener que tirar 48 cables largos por todo el pasillo.
*   **Patch Panel y Switch Principal (MDF):** El requerimiento de la práctica indica que el panel del edificio debe dimensionarse según el total de puntos de red[cite: 3]. Como tenemos un total de 48 dispositivos finales, **dimensioné el Patch Panel principal para 48 puertos**. Por consiguiente, el **Switch Principal del MDF también es de 48 puertos**[cite: 3], lo que nos deja margen de maniobra y cumple exactamente con la regla de capacidad.

## 7. Justificación del medio de transmisión para cableado troncal
Para el cableado troncal (las líneas rojas que unen el MDF con los switches de cada departamento)[cite: 3], decidí utilizar **Fibra Óptica (Multimodo)**. La razón principal es el ancho de banda y la escalabilidad[cite: 3]. El enlace entre un switch de departamento y el MDF (uplink) es un "embudo" por donde pasa toda la información de esos usuarios al mismo tiempo. Si usáramos cable de cobre normal, la red podría saturarse. La fibra nos garantiza altas velocidades (10 Gbps o más) y nos asegura que la red no se quedará corta en el futuro.

## 8. Justificación de la canalización utilizada
Para la ruta principal que atraviesa el edificio, propongo utilizar **escalerilla metálica abierta** instalada sobre el cielo falso del "Pasillo Central"[cite: 3].
Elegí este tipo de canalización por tres razones lógicas:
1.  **Ventilación:** Los cables (especialmente si hay muchos) generan cierto calor, y al estar al aire libre se enfrían mejor.
2.  **Mantenimiento:** Es mucho más fácil identificar un cable dañado a simple vista que si estuviera metido en un tubo de PVC cerrado.
3.  **Escalabilidad:** Si mañana QuetzalDev S.A. necesita pasar 20 cables nuevos, solo tenemos que subirlos a la escalerilla y amarrarlos, sin necesidad de romper o destapar tuberías.

## 9. Justificación del rack o gabinete para el MDF
Para el Cuarto de Telecomunicaciones (Data Center), elegí instalar un **Rack de piso abierto de 42U** (Unidades Rack), en lugar de un gabinete de pared pequeño[cite: 3].
La decisión se basa en el tamaño de los equipos a alojar: en este cuarto estarán el Switch Principal de 48 puertos, el Patch Panel de 48 puertos, los organizadores de cables, los 3 servidores principales de la empresa (que suelen ser equipos grandes y pesados) y el equipo UPS[cite: 3]. Un gabinete de pared simplemente no soportaría el peso ni tendría el espacio físico necesario, mientras que el Data Center (de 8m x 5m) tiene espacio de sobra para colocar un rack de piso cómodamente.


## 10. Estimación del consumo eléctrico y capacidad de UPS requerida
Para garantizar que la red siga funcionando durante un apagón, calculé el consumo de energía del **equipo activo** de nuestra red (el switch central y los switches de los departamentos). 

**Cálculo del consumo (en Watts):**
*   **8 Switches de departamento (24 puertos):** Un switch estándar de este tipo consume en promedio unos 30 Watts. (8 switches * 30W = 240 Watts).
*   **1 Switch Principal en el MDF (48 puertos):** Al ser un equipo más robusto, estimo un consumo máximo de 80 Watts.
*   **Total estimado de equipos de red:** 240W + 80W = **320 Watts**.

**Cálculo del UPS:**
Los UPS no se miden solo en Watts (W), sino en Voltamperios (VA). Para tener un margen de seguridad y tiempo de respaldo (autonomía) de al menos 30 a 45 minutos para que la red no se caiga de golpe, lo ideal es multiplicar el consumo real por un factor de crecimiento y seguridad (usualmente el doble).
*   **Capacidad propuesta:** Recomiendo instalar un **UPS de 1000 VA (1 kVA)** para el rack del MDF (donde se conectará el switch central y los servidores) y pequeños UPS de **500 VA** en cada cuarto para proteger el switch local de picos de voltaje. 

## 11. Tabla de enlaces: Straight-through vs Crossover
En redes, la regla básica de cableado de cobre (UTP) nos dice que si conectamos **dispositivos diferentes** (ej. Computadora a Switch) usamos un cable Directo (Straight-through). Pero si conectamos **dispositivos iguales** (ej. Switch a Switch), usamos un cable Cruzado (Crossover) para que los pines de envío de un lado coincidan con los de recepción del otro.

Aunque hoy en día muchos equipos modernos traen la tecnología "Auto-MDIX" que cruza los pines automáticamente por software, el diseño físico estricto de Capa 1 nos exige documentarlo de la siguiente manera[cite: 3]:

| Enlace (Extremo A - Extremo B) | Tipo de Cable | Justificación Técnica |
| :--- | :--- | :--- |
| **PC / Laptop a Switch** (Cableado horizontal) | **Straight-through (Directo)** | Son dispositivos de diferente tipo (host final vs equipo intermedio). Los pines de transmisión (Tx) de la PC apuntan directamente a los pines de recepción (Rx) del switch sin necesidad de cruzar los cables internamente. Se usa el mismo estándar en ambos lados (ej. T568B a T568B)[cite: 3]. |
| **Servidor a Switch** (Cableado horizontal) | **Straight-through (Directo)** | Aplica exactamente la misma lógica que con las computadoras. Un servidor es un equipo final que se comunica hacia el puerto de acceso de un switch. |
| **Switch de Depto a Switch Principal** (Si el cableado troncal fuera UTP en lugar de fibra) | **Crossover (Cruzado)** | Al ser dos dispositivos exactamente iguales (Switch a Switch), sus puertos transmiten y reciben por los mismos pines. Si pusiéramos un cable directo, habría una "colisión" (Transmisión chocaría con Transmisión). El cable cruzado invierte físicamente los pares (un lado en T568A y el otro en T568B) para que el envío de uno llegue a la recepción del otro[cite: 3]. |

**Nota sobre la tabla:** Como en mi diseño principal justifiqué el uso de Fibra Óptica para el cableado troncal (Switch a Switch), la regla estricta de Crossover aplica principalmente como contingencia o si se requiere conectar equipos de red mediante cable UTP de cobre como respaldo.

## 12. Disposición de pines documentada (Straight-through y Crossover)
Para que los cables UTP funcionen correctamente, los 8 hilitos de cobre que van por dentro deben acomodarse en un orden de colores específico al momento de poncharlos (armar el conector RJ-45). Para esto, utilicé los estándares internacionales **T568A** y **T568B**[cite: 3]. 

A continuación, documento el orden de los pines (del 1 al 8) para los dos tipos de cables solicitados[cite: 3]:

**A. Cable Directo (Straight-through)**
Este es el cable que usaré para el cableado horizontal (conectar las PCs, Laptops y Servidores a los switches de cada cuarto)[cite: 3]. Para armarlo, ambos extremos del cable deben llevar exactamente el mismo estándar. En este caso, elegí el **T568B**, que es el más utilizado comercialmente hoy en día.

| Pin | Extremo A (T568B) | Extremo B (T568B) |
| :--- | :--- | :--- |
| **1** | Blanco - Naranja | Blanco - Naranja |
| **2** | Naranja | Naranja |
| **3** | Blanco - Verde | Blanco - Verde |
| **4** | Azul | Azul |
| **5** | Blanco - Azul | Blanco - Azul |
| **6** | Verde | Verde |
| **7** | Blanco - Café | Blanco - Café |
| **8** | Café | Café |

**B. Cable Cruzado (Crossover)**
Este cable se utilizaría si conectáramos un switch directamente con otro switch usando cable de cobre[cite: 3]. Para lograr el "cruce" de las señales de envío (Tx) y recepción (Rx), un extremo del cable se poncha con la norma **T568A** y el otro extremo con la norma **T568B**[cite: 3].

| Pin | Extremo A (T568A) | Extremo B (T568B) |
| :--- | :--- | :--- |
| **1** | Blanco - Verde | Blanco - Naranja |
| **2** | Verde | Naranja |
| **3** | Blanco - Naranja | Blanco - Verde |
| **4** | Azul | Azul |
| **5** | Blanco - Azul | Blanco - Azul |
| **6** | Naranja | Verde |
| **7** | Blanco - Café | Blanco - Café |
| **8** | Café | Café |

## 13. Tabla de etiquetado de cables
Un diseño de red profesional no sirve de nada si cuando falla un cable no sabemos a dónde va. Por ello, apliqué un esquema de etiquetado riguroso siguiendo las instrucciones de la práctica[cite: 3]. 

Las etiquetas se colocan en ambos extremos de cada cable físico para facilitar el mantenimiento.

**Reglas de formato aplicadas:**
*   **Cableado Horizontal (Líneas verdes):** `[Área/Departamento]-[Número de Punto de Red]`[cite: 3].
*   **Cableado Troncal (Líneas rojas):** `MDF-[Área/Departamento]`[cite: 3].

A continuación presento la tabla representativa con ejemplos de las etiquetas que coloqué en el diagrama para los diferentes departamentos de QuetzalDev S.A.[cite: 3]:

| Segmento / Tipo de Cable | Origen de la Conexión | Destino de la Conexión | Ejemplo de Etiqueta Aplicada |
| :--- | :--- | :--- | :--- |
| **Horizontal** | Switch de Recepción | PC 1 en Recepción | `Recepcion-PR01`[cite: 3] |
| **Horizontal** | Switch de Recepción | Servidor en Recepción | `Recepcion-PR04` |
| **Horizontal** | Switch Legal | PC 3 en Legal | `Legal-PR03`[cite: 3] |
| **Horizontal** | Switch Backend | Laptop 2 en Backend | `Backend-PR02` |
| **Horizontal** | Switch Diseño e Innovación | Servidor en Diseño | `Diseño-PR08` |
| **Horizontal** | Switch MDF (Data Center) | Servidor Principal 1 | `DataCenter-PR01` |
| **Troncal** | Switch Principal MDF | Switch de Recepción | `MDF-Recepcion`[cite: 3] |
| **Troncal** | Switch Principal MDF | Switch de Backend | `MDF-Backend`[cite: 3] |
| **Troncal** | Switch Principal MDF | Switch de Rec. Humanos | `MDF-RecursosHumanos` |
| **Troncal** | Switch Principal MDF | Switch Dir. General | `MDF-DireccionGeneral` |

## 14. Comparación con el estándar TIA/EIA-606
Para esta práctica, utilicé un esquema de etiquetado simplificado y descriptivo (ej. `Legal-PR03` o `MDF-Backend`), el cual es fácil de leer a simple vista. Sin embargo, al investigar el estándar oficial **TIA/EIA-606** (Administración de Infraestructura de Telecomunicaciones)[cite: 3], noté que hay diferencias importantes respecto a cómo se haría en una red de clase mundial.

**Dos diferencias concretas entre mi esquema y el estándar 606[cite: 3]:**
1.  **Formato de Identificadores:** Mi esquema usa nombres de uso común (como "Recepcion"), lo cual puede ser ambiguo si la empresa decide cambiar el uso del cuarto mañana. El estándar TIA/EIA-606 exige identificadores alfanuméricos estrictos que indican la ubicación física exacta sin importar el departamento. Por ejemplo, en lugar de `Legal-PR03`, usaría algo como `1A-B01` (Piso 1, Cuarto A, Rack B, Puerto 01).
2.  **Alcance del etiquetado:** En mi diseño me limité a etiquetar los cables horizontales y troncales. El estándar TIA/EIA-606 va mucho más allá y exige etiquetar *todo* el ecosistema: las rutas de canalización (la escalerilla), las barras de conexión a tierra, los espacios cortafuegos y hasta cada posición específica dentro del Patch Panel.

**¿Por qué en un entorno real se optaría por el estándar completo?[cite: 3]**
En un Data Center real, el administrador de la red no siempre es la misma persona que instaló los cables. Si usamos el estándar oficial, cualquier ingeniero del mundo (o una empresa externa) puede llegar, ver una etiqueta y saber exactamente de qué rack y de qué edificio viene ese cable sin necesidad de adivinar a qué llamábamos "Departamento Legal". Esto reduce drásticamente el tiempo de resolución de fallas y mantiene el orden a largo plazo.

## 15. Flujo de conexión End-to-End
Para entender cómo funciona físicamente toda esta topología[cite: 3], describo el flujo de conexión paso a paso. Imaginemos que un usuario en la **PC 1 del Departamento Legal** necesita acceder a una base de datos alojada en el **Servidor 1 del Data Center (MDF)**. 

El recorrido físico de esos datos (Capa 1) sería el siguiente:
1.  La información sale de la tarjeta de red de la PC 1 en el Depto. Legal.
2.  Viaja por un cable de red (patch cord) hasta la **toma de red** (roseta) instalada en la pared/escritorio.
3.  Desde esa roseta, los impulsos eléctricos viajan por el **cableado horizontal (UTP Cat 6, ponchado en T568B)**, subiendo por la pared hasta llegar al pequeño gabinete de telecomunicaciones del cuarto Legal[cite: 3].
4.  El cable se conecta por detrás al **Patch Panel local** del cuarto, y de ahí, a través de un patch cord corto, entra a un puerto de acceso del **Switch de Legal**.
5.  El Switch de Legal procesa la señal, la convierte en pulsos de luz y la envía por su puerto de uplink hacia el **cableado troncal de Fibra Óptica**[cite: 3].
6.  La luz viaja a toda velocidad por la fibra óptica, recorriendo la **escalerilla metálica abierta** sobre el cielo falso del pasillo central[cite: 3], hasta llegar al cuarto del Data Center (MDF).
7.  Entra al rack principal, pasa por el organizador de fibra (ODF) y se conecta directamente al **Switch Principal de 48 puertos**[cite: 3].
8.  Finalmente, el Switch Principal envía la información a través de un cable directo (Straight-through)[cite: 3] hacia la tarjeta de red del **Servidor 1**, completando así el flujo end-to-end.

## 16. Presupuesto estimado de equipo y materiales
A continuación, presento una estimación de los costos de la infraestructura física de red (Capa 1 y Capa 2), considerando precios aproximados del mercado actual en Guatemala (en Quetzales, GTQ)[cite: 3]. 

*Nota: Este presupuesto contempla únicamente la infraestructura de conectividad de red y respaldo eléctrico. No incluye el costo de adquisición de las 30 PCs, 12 Laptops y 6 servidores, ya que se asume que QuetzalDev S.A. ya adquirió ese equipo informático.*

| Cantidad | Descripción del Artículo | Precio Unitario Estimado | Subtotal Estimado |
| :---: | :--- | :--- | :--- |
| **3** | Bobinas de Cable UTP Cat 6 (305 metros c/u) puro cobre | Q 1,400.00 | Q 4,200.00 |
| **48** | Módulos Jack RJ45, faceplates y cajas (Tomas de red) | Q 85.00 | Q 4,080.00 |
| **8** | Switches Administrables 24 puertos Gigabit + puertos SFP (Departamentos) | Q 1,800.00 | Q 14,400.00 |
| **1** | Switch Principal Core 48 puertos Gigabit + puertos SFP+ (MDF) | Q 8,500.00 | Q 8,500.00 |
| **8** | Gabinetes de pared de 9U (Para switches locales) | Q 950.00 | Q 7,600.00 |
| **1** | Rack de piso abierto de 42U (Para el MDF) | Q 3,200.00 | Q 3,200.00 |
| **9** | Patch Panels Cat 6 (Ocho de 24 puertos y Uno de 48 puertos) | Q 600.00 (Promedio) | Q 5,400.00 |
| **8** | Tramos de Fibra Óptica OM3 pre-conectorizada + Transceivers (SFP) | Q 1,200.00 | Q 9,600.00 |
| **1** | UPS de 1000 VA / 1kVA (Para el Rack Principal MDF) | Q 1,800.00 | Q 1,800.00 |
| **8** | UPS de 500 VA (Para resguardar gabinetes de cada departamento) | Q 450.00 | Q 3,600.00 |
| **1** | Lote de Canalización: Tramos de escalerilla metálica, curvas y soportes | Q 5,500.00 | Q 5,500.00 |
| **1** | Insumos varios (Patch cords cortos, cinchos, organizadores de cables) | Q 2,500.00 | Q 2,500.00 |
| | | **TOTAL ESTIMADO:** | **Q 70,380.00** |

*El trabajo de instalación o mano de obra técnica externa dependerá si se trabaja por administración o mediante licitación con un proveedor integrador.*