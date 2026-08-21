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

# Informe de Desarrollo - QuetzalDev S.A.

## 1. Proceso de diseño y retos de planificación física
Al iniciar esta práctica, el primer reto fue interpretar el plano arquitectónico en blanco y decidir cómo distribuir las 30 computadoras de escritorio, 12 laptops y 6 servidores sin saturar el espacio visual ni la infraestructura. 

Uno de los mayores desafíos físicos fue la ubicación del Cuarto de Telecomunicaciones (MDF). Aunque la teoría sugiere buscar el centro geométrico del edificio para ahorrar cable, decidí utilizar el cuarto etiquetado como "Data Center". Mi razonamiento fue que este espacio ya está diseñado para alojar los 3 servidores principales de la empresa, brindando la seguridad física y el espacio necesario para un rack de 42U y el equipo UPS. 

Durante la etapa de diagramación, me enfrenté a una gran duda técnica: cómo representar gráficamente las conexiones sin que el plano se volviera incomprensible. Al principio no sabía si debía dibujar los Patch Panels y conectar cada equipo hacia ellos. Tras analizar el diseño, comprendí que en un diagrama de topología física es mejor trazar el cableado horizontal (que diferencié con color verde) directamente desde las tomas de red hasta los switches de cada departamento. Aprendí a separar el diagrama visual de la instalación real, entendiendo que físicamente esos cables verdes terminarán ponchados en un Patch Panel para proteger los puertos de los switches, aunque en el dibujo la línea vaya directo al switch.

## 2. Criterios de selección de topologías y equipo activo
Para conectar los equipos dentro de cada departamento, evalué varias opciones y me decidí por la **topología en estrella**. 

*   **Tolerancia a fallos:** Si un usuario daña su cable de red o su toma falla, el problema queda aislado en ese único enlace; el resto del departamento sigue conectado.
*   **Escalabilidad:** Instalar un nuevo nodo solo requiere tirar un cable nuevo desde el switch local, sin interrumpir el servicio de los demás.
*   **Equipo Activo:** Decidí colocar un switch secundario en cada cuarto. Esto resolvió un gran problema de planificación física: evitó que tuviera que tirar 48 cables individuales por todo el pasillo central, manteniendo la canalización ordenada.
*   **Cableado Horizontal:** Seleccioné cable de cobre UTP Categoría 6, ya que las distancias desde las PCs al switch de su propio cuarto son bastante cortas, y este cable soporta sin problemas velocidades Gigabit.

## 3. Justificación del medio utilizado en el cableado troncal
El cableado troncal es la columna vertebral de la red, representado en mi plano con líneas rojas que viajan desde el Switch Principal del MDF hacia los switches de cada departamento. Para enrutar estos cables, propuse usar una escalerilla metálica abierta a lo largo del techo falso del pasillo central, facilitando la ventilación y el mantenimiento.

Para el medio de transmisión de este segmento, elegí **Fibra Óptica Multimodo** en lugar de cobre. La razón principal es el manejo del ancho de banda y la prevención de cuellos de botella. Como el tráfico de red de un departamento entero (por ejemplo, 10 usuarios en la Sala de Capacitación o los analistas de Diseño e Innovación) va a viajar simultáneamente por ese único enlace troncal hacia los servidores principales, el cobre UTP tradicional podría saturarse. La fibra óptica asegura velocidades de 10 Gbps o más, garantizando que el diseño de QuetzalDev S.A. sea robusto y a prueba de futuro.