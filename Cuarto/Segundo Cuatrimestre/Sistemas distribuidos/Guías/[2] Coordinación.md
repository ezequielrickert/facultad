

### Explicar por qué los relojes de distintos procesos se desincronizan en un sistema distribuido.

-- Respuesta IA

Cada proceso tiene su propio reloj físico independiente, y estos relojes no pueden ser perfectamente idénticos ni funcionar a la misma velocidad debido a errores naturales, desgaste o variaciones de temperatura. Además, la comunicación entre procesos sufre variaciones en el tiempo de respuesta de la red, lo que hace imposible mantener una sincronización exacta de relojes físicos y dificulta la ordenación correcta de eventos, incluso cuando se utilizan relojes lógicos.

-- Sumado

Vimos que eran relojes de cuarzo, lo que tiene es que hay una propiedad del cuarzo (no me acuerdo bien cual), que no se mantiene igual en todos, cada elemento tenía esta propiedad más perfecta. Los que tienen esta propiedad casi casi perfecta y no se desincronizan, son los llamados "relojes atómicos".

### Describir los problemas que puede generar esta desincronización

#### Orden de eventos
Si hay desincronización, no podremos saber que evento ocurrió primero. De esta forma, perdemos el orden en el que se ejecutaron los pasos, llevando a inconsistencia y fallas.

#### Registro de logs

Inconsistencia de tiempos, falta de trazabilidad. No podemos saber que proceso tiene "la razón", no podremos trazar transacciones ocurriendo entre procesos por tiempos cambiados.

#### Coordinación de acceso a recursos

Puede haber invalidez de datos e inconsistencia en los procesos. Acceso a recursos no actualizados puede generar fallas en los sistemas distribuidos.

-- Sobre este ultimo, tengo una corrección por IA

Si los relojes están desincronizados, los mecanismos de coordinación basados en tiempo (ej. expiración de locks, timeouts) fallan. Esto puede dar lugar a accesos concurrentes indebidos, pérdida de exclusión mutua y datos inconsistentes.


### Comparar Lamport clocks y vector clocks ¿Qué problema resuelven cada uno?


-- Respuesta mía

Vimos que Lamport venía a sincronizar los procesos, hacía que si había falta de coherencia en los tiempos (el receptor estaba recibiendo un mensaje "adelantado en el tiempo") se sincronizaba el reloj para garantizar que iba t + 1. Este sin embargo, lo único que logra es generar orden entre 2 procesos. Pero... que pasa si es entre muchos procesos? 

Acá vienen los relojes de vectores, lo que buscan es, NO garantizar pero si sospechar sobre la causalidad de los eventos. Se arma un vector del tamaño de los procesos que interactúan, y cada proceso aumenta en 1 el valor cuando ejecuta una transacción. Cuando la transacción se ejecuta, se envía al otro proceso el valor actualizado y, en caso de tener sentido, se actualiza también este valor. Lo que garantiza este modelo, es que si todos los contadores, excepto el propio, son menores o iguales que el recibido, entonces tenemos un mensaje válido.

-- Sobre este ultimo, tengo una corrección por IA

**Lamport clocks** asignan un número creciente a cada evento de un proceso y lo ajustan al recibir mensajes. Esto garantiza un **orden lógico total**: si un evento A causó B, entonces el timestamp de A < B. Sin embargo, no permiten distinguir entre causalidad real y concurrencia; sólo aseguran consistencia en el orden.

**Vector clocks**, en cambio, mantienen un vector de contadores (uno por proceso). Cada proceso incrementa su propio contador al ejecutar un evento y envía el vector al comunicarse. Al recibirlo, se hace un merge tomando el máximo por componente. Esto permite **detectar relaciones causales**: si V(A) ≤ V(B), entonces A → B; si no son comparables, los eventos son concurrentes.

En síntesis: Lamport resuelve el problema del **orden lógico** en sistemas distribuidos, mientras que los vector clocks resuelven el problema de **detectar causalidad y concurrencia** entre múltiples procesos.

### Comparar los tres algoritmos de exclusión mutua


|             | Centralizado | Basado en Token | Ricart-Agrawala |
| ----------- | ------------ | --------------- | --------------- |
| Responsable | único        | todos           | todos           |
| Token       | no           | si              | no              |


#### Centralizado

Este sistema era el que había un servicio que se encargaba de mediar entre los clientes y el recurso. 
![[Pasted image 20250923223038.png]]

El problema que le encontrabamos a esta solución es que se cae el mediador y nos encontramos sin acceso a al recurso. Otro problema es que tenemos que definir claramente los roles de antemano, el servicio de mediador tiene que saber de antemano que va a cumplir ese rol.

### Basado en token

Vimos que se pensaba como un anillo lógico en el que se pasaba el token al siguiente.

![[Pasted image 20250923223637.png]]

El problema que encontrábamos con este eran dos:

1. Si hay mala suerte, y se cae el nodo que posee, tendremos que asignar y coordinar la responsabilidad de crear un nuevo token.
2. En el caso de que la red siga creciendo, se hacen ciclos demasiado largos de pasaje de token.

### Ricart-Agrawala

Para recordar, este era el algorimo en el que un nodo le preguntaba a todos los nodos si podía acceder y esperaba a que le respondan.

![[Pasted image 20250923224128.png]]

Si pensamos en este algoritmo podemos pensar en muchísimos problemas:
1. Tenemos muchos mensajes, emisión a todos y recepción de todos.
2. Se puede quedar esperando a que un nodo caído conteste
3. No asegura la falta de starvation


### Para cada algoritmo, analizar


|                     | Centralizado                                      | Basado en token                                                                                                                                  | Ricart-Agrawala                                                               |
| ------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------- |
| Mensajes requeridos | 2*(n-1) todos piden, todos reciben grant en orden | n: justo necesito el token despues de pasarlo                                                                                                    | 2*(n-1): uno manda broadcast, todos contestan                                 |
| Tolerancia a fallas | Se cae el nodo central y no hay acceso            | Se cae el nodo con el token y se pierde, como lo levantamos?                                                                                     | Se debe tener noción de cuando un nodo se cae para no esperar a su respuesta. |
| Latencia            | Baja, la comunicación se da 1 a 1                 | Media, se tiene que hacer la comunicación a traves de los intermediarios en el anillo. Puede irse a valores altos con el crecimiento del anillo. | Alta, se tiene que comunicar con todos los nodos de la red.                   |
(Los mensajes se piensan desde el peor caso)

### usando un algoritmo bully

