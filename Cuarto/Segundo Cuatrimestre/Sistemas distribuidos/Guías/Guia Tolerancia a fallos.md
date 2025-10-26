
CONFIABILIDAD no cumple con lo que dice que tiene que tener.


**1 - Explique la diferencia fundamental entre los conceptos de Availability y Reliability. A continuación, ilustre estas diferencias proporcionando un ejemplo claro de un sistema o situación de la vida real para cada una de las siguientes combinaciones y justifiqué:**

Cuando hablamos de availability es cuanto promete el sistema, en cierto periodo de tiempo, estar disponible. Mientras que reliability es, efectivamente, cuanto cumple y cuan seguro se puede estar del funcionamiento correcto del sistema en este periodo.

**a. High Reliability and High Availability**

Sistema de salud

**b. High Reliability and Low Availability (Un ejemplo análogo podría ser un auto de**
**Formula 1: Durante la carrera debe ser super confiable y rendir bien. Pero al término de la carrera puede que tenga que estar en el taller durante un tiempo lo que produce una baja disponibilidad.)**

Sistema de banco

**c. Low Reliability and High Availability**

Campus de estudio

**d. Low Reliability and Low Availability**

Una aplicación poco crítica en beta

Chat:

**Availability (Disponibilidad)** mide el porcentaje de tiempo que un sistema o recurso está operativo y accesible para su uso. Se enfoca en el **tiempo de actividad** (_uptime_) del sistema durante un periodo de observación. Es una métrica de **estado** (arriba/abajo).

**Reliability (Fiabilidad o Confiabilidad)** mide la probabilidad de que un sistema funcione correctamente **sin fallos** durante un periodo de tiempo determinado, bajo condiciones específicas. Se enfoca en la **frecuencia de las fallas** y la **calidad del servicio** cuando está activo. Es una métrica de **rendimiento sin errores**.

**2 - Durante el último cyber monday el sistema de venta de merchandising de la Universidad Austral tuvo las siguientes caídas. Calcular MTTF, MTTR y MTBF:**


| Hora caída | Hora recuperación | Tiempo indisponible |
| ---------- | ----------------- | ------------------- |
| 08:00 am   | 08:30 am          | 30m                 |
| 10:30 am   | 11:30 am          | 60m                 |
| 16:15 am   | 16:30 am          | 15m                 |

MTTR = 105m / 3 = 35m

MTBF = (2h + 4h 45m) / 2 = (405m) / 2

MTBF = 35m + (405m) / 2

**3 - Tengo un sistema que recibe un tráfico de 100.000 RPM (requests per minute). ¿Cuántos requests podrían quedar sin respuesta o recibir un status code 5xx para asegurar un uptime del 99,99% durante el lapso de 1 minuto?**

100 % --- 100.000 requests
0.01% --- x = 10 requests

**4 - Manejo de fallos mediante Redundancia. Busque un ejemplo para cada uno de los 3 tipos de redundancia (Información, tiempo y física) que se utilicen en sistemas reales y especifique dicho sistema.**

1. **De información**: Se añade información redundante - bit de paridad para validar integridad de mensaje
   
2. **De tiempo**: Se realiza una acción varias veces - Actores AKKA ante operaciones idempotentes envian mensajes repetidos
   
3. **Física**: Se usan componentes duplicados - memorias extra en data centers 

**5 - Teorema de CAP. Indique qué tipo de sistema son los siguientes (CP o AP) y justifique:**

**a. Youtube**

AP

**b. Home banking**

CP

**c. Feed de Instagram**

AP

**d. Red Bitcoin**

CP

**6 - En la resiliencia de procesos existen dos tipos de grupo, explique cuáles y qué ventajas y desventajas tiene cada uno.**

Pueden ser:


| Tipo                               | Ventajas                     | Desventajas                                                    |
| ---------------------------------- | ---------------------------- | -------------------------------------------------------------- |
| Decisiones Colectivas              | Equidad, tolerancia a fallas | Complejidad, costo de mensajes                                 |
| Un coordinador, resto trabajadores | Simplicidad, menor costo     | Baja tolerancia a fallas si cae coordinador, decisiones únicas |

**7 - ¿Por qué existen diferentes tipos de algoritmos de consenso? ¿Cuál es la problemática mayor? ¿Podemos conseguir consenso en un sistema asíncrono?**

Los algoritmos de consenso tienen un tradeoff entre tolerancia a fallos y costos. Es por esto, que dependiendo de la dilatación del sistema se pueden usar distintos algoritmos de consensos. La mayor problemática es: "¿Cómo conseguir consenso sobre el comando concreto a ejecutar?". Es decir, cómo se ponen de acuerdo los procesos de quien ejecuta. Es muy difícil en procesos asíncronos ya que, si un proceso no responde, es imposible detectar si se está demorando o si está caído.

**8 - Comunicación Cliente Servidor Confiable. Indique en cuál de las siguientes opciones (consulta a otro microservicio) podría configurar un retry sin más preocupaciones y justifiqué:**

**a. Actualizar información del usuario como edad.**

Se puede sin problema, es idempotente 😀

**b. Obtener la Geolocalización a partir de una IP.**

Se puede sin problema, es lectura 😀

**c. Realizar pago de un pasaje de avión.**

No es tan sencillo 😩 puede que haya quedado la transacción a medias

**d. Eliminar usuario de la base de datos.**

Se puede sin problema 😀 pensemos una sentencia SQL "Delete where id = 2", si no existe no hace nada

**9 - Multicast confiable, defina qué tipo de multicast necesitan cada uno de los siguientes sistemas y justifiqué:**

- No ordenado
- Ordenado FIFO
- Ordenado Causal
- Atomic Multicast
- Atomic FIFO
- Atomic Causal

a. No ordenado, no me interesa que lleguen en un orden particular ni que le llegue a todos en el mismo orden.

b. Ordenado FIFO ya que si me interesa el orden en el que realiza las transacciones el usuario, pero no la interacción con otros. Se asume que las transacciones de aumento y reducción de dinero dentro de "x le paga a y" ocurren atómicamente.

c. Ordenado causal, donde interesa que aparezca el orden causal pero no que todos los dipositivos tengan todo en el mismo orden.

d. Atomic causal, donde necesito que todos tengan ordenadas las acciones y garantizo que vean lo mismo en tiempo real.

e. Atomic FIFO ya que es importante que todos reciban en el mismo orden que fue generado para no repetir trabajo.

f. Totalmente ordenado, ya que es importante el orden en el que fueron generados para hacer, por ejemplo, un debugging correcto. Es necesario que sea totalmente ya que no solo se necesita el orden de un proceso, si hay logging de muchos procesos importa el orden de todos para entender la interacción.