# Tolerancia a fallas

## Sistemas confiables

### A qué nos referimos?

- Tolerar fallas parciales
  
- Tener la capacidad de recuperarse

![[Pasted image 20251023114552.png]]

1. Availability (Disponibilidad): La probabilidad de que el sistema esté operativo (disponible para usar) en un momento dado. 
   
2. Reliability (Fiabilidad): La capacidad del sistema para funcionar continuamente sin fallos durante un período de tiempo.
   
3. Safety (Seguridad o Inocuidad): La propiedad de que, cuando el sistema falla, no ocurra un desastre catastrófico (ej. pérdidas de vidas, económicas o ambientales).
   
4. Maintainability (Mantenibilidad): La facilidad con la que se puede reparar, adaptar o mejorar el sistema y que se detectan las fallas.


### Métricas
- Mean Time To Failure (MTTF): Tiempo medio hasta el fallo. 
  
- Mean Time To Repair (MTTR): Tiempo medio para reparar.
  
- Mean Time Between Failures (MTBF): MTTF + MTTR.
  
- Disponibilidad (A): MTTF / (MTTF + MTTR).

![[Pasted image 20251023115037.png]]

## Consistencia, disponibilidad y particionamiento

### Teorema de CAP

Solo se pueden garantizar 2 de las 3 siguientes:

1. C - Consistencia (Consistency): Todos los nodos del sistema ven la misma versión de los datos al mismo tiempo. Es como si hubiera una única copia actualizada de los datos.
   
2. A - Disponibilidad (Availability): Todas las solicitudes reciben una respuesta (no un error), aunque esa respuesta no siempre contenga la versión más reciente de los datos. 
   
3. P - Tolerancia a Particiones (Partition Tolerance): El sistema continúa funcionando incluso si algunos nodos no puedan comunicarse entre sí (una "partición" de la red).

En los sistemas distribuidos, la tolerancia a particiones es algo que tenemos que asumir ya que no depende de nosotros la fiabilidad de la red. Es por esto que nos queda elegir entre un sistema CP o uno AP. Se caracterizan por:

- **Sistemas CP**: Priorizan la Consistencia. Si hay una partición, el sistema puede volverse no disponible para garantizar que las respuestas sean correctas.
  
- **Sistemas AP**: Priorizan la Disponibilidad. Si hay una partición, el sistema puede devolver datos potencialmente inconsistentes para seguir respondiendo.

## Fallas

> El desafío es diseñar sistemas que puedan tolerar diferentes combinaciones de estos tipos de fallos.

### Qué son?

- Una **defecto** (fault) es la causa de un error (ej: un programador que introduce un bug, un disco rayado, etc.).
  
- Un **error** es una parte del estado del sistema que puede llevar a un fallo (ej: bug de programación).

- Un sistema **falla** (failure) cuando no cumple sus promesas / especificaciones.

defecto -> error -> falla

#### Categorías

1. **Por caída** (Crash failures): se detiene y no responde.
   
2. **De omisión** (Omission failures): 
	1. **De envío** (Send-omission): falla al enviar un mensaje que debía enviar.
	2. **De recepción** (Receive-omission): falla al recibir mensajes que llegan.
	
3. **De tiempos** (Timing failures): timeout.
   
   ![[Pasted image 20251023121400.png]]
   
4. **De respuesta** (Response failures): respuesta es incorrecta.
	1. **De valor** (Value failure): La respuesta contiene un valor incorrecto.
	2. **De transición de estado** (State-transition failure): Se desvía el correcto flujo de control. (un sistema produce la salida correcta, pero **en el momento equivocado** o **después de que el estado interno ya ha cambiado**, haciéndola obsoleta o incorrecta para el contexto actual.)
5. **Arbitrarios** (Arbitrary failures): puede producir respuestas incorrectas en en momentos arbitrarios (ej: respuestas inconsistentes).
	1. **Por omisión**: Un componente no realiza una acción que debería haber realizado.
	2. **Por comisión**: Un componente realiza una acción que no debería haber realizado.

![[Pasted image 20251023121423.png]]

Está bueno ver esto, los errores que estamos viendo son tanto de capa física (se cae), de capa de red (falla en comunicación) como posiblemente de aplicación (arbitrarios).


### Detección

- **Dificultad en Sistemas Asíncronos**: En un sistema puramente asíncrono, es imposible distinguir si un proceso se ha caído o si sólo está respondiendo muy lento.

- **Mecanismos Comunes**: La práctica se basa en mecanismos de tiempo de espera (timeouts) para sospechar que un proceso ha fallado.
  
| **Tier (Nivel)**           | **Nombre de la Falla**         | **Comportamiento**                                                                                                                       | **Costo/Complejidad del Sistema**                                                                                                  |
| -------------------------- | ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Tier 1 (Más Benigna)**   | **Fail-Stop**                  | El proceso se detiene limpiamente.                                                                                                       | **Bajo.** Protocolos simples basados en _timeouts_ confiables.                                                                     |
| **Tier 2**                 | **Fail-Silent**                | El proceso deja de responder (omisión o caída), sin enviar mensajes incorrectos.                                                         | **Moderado.** Se requiere consenso para decidir si el proceso está vivo o muerto (ej. **Fail-Stop** sin comunicación garantizada). |
| **Tier 3**                 | **Fail-Noisy**                 | El proceso puede responder erráticamente o lento antes de fallar.                                                                        | **Alto.** Se requiere monitoreo constante y filtros para identificar y descartar respuestas ruidosas.                              |
| **Tier 4**                 | **Fail-Safe**                  | El proceso puede fallar arbitrariamente, pero el sistema está diseñado para que el resultado de la falla sea siempre seguro (no dañino). | **Alto.** Requiere diseño estricto de seguridad del estado final, a menudo a costa de disponibilidad.                              |
| **Tier 5 (Más Maliciosa)** | **Fail-Arbitrary (Bizantina)** | El proceso actúa de manera contradictoria o maliciosa (miente a diferentes observadores).                                                | **Muy Alto.** Requiere protocolos BFT, que necesitan $\geq 3f + 1$ réplicas para tolerar $f$ fallas.                               |

### Redundancia

1. **De información**: Se añade información redundante (ej. códigos de detección/corrección de errores). 
   
2. **De tiempo**: Se realiza una acción varias veces (ej. retransmisión de mensajes). 
   
3. **Física**: Se usan componentes duplicados (ej. replicación de procesos o datos).

## Recuperación ante fallas

### Objetivo

>Analizar la dependencias entre servicios para entender cómo reaccionar ante un evento de recuperación de uno de los miembros.

Me puedo encontrar con problemas del tipo:

- Aplicaciones con problemas
- Pérdida de datos

La recuperación se refiere al proceso de devolver un componente fallido (o todo el sistema) a un estado correcto después de que ha ocurrido un fallo y ha sido detectado/reparado. 

**Objetivo**  = Minimizar el impacto del fallo y restaurar el servicio. 

**Tipos de Recuperación**:

- Recuperación hacia atrás (Backward Recovery): Volver a un estado anterior correcto.
  
- Recuperación hacia adelante (Forward Recovery): Hace falta conocer errores con antelación.

### Backward Recovery

- El sistema debe guardar su estado periódicamente: checkpoint.
  
- En caso de fallo, un proceso puede retroceder a su último checkpoint válido en lugar de reiniciar desde cero.
  
- La recuperación requiere construir un estado global consistente a partir de los estados locales guardados por cada proceso. (**Esto no es trivial!**)
  
- El checkpoint se puede lograr de forma **coordinada** (más simple) o **independiente** (más complejo, todos los sistemas tienen que tener coherencia sin coordinarse).
  
- La línea de recuperación es el conjunto de _checkpoints_ (uno de cada proceso) que cumplen dos condiciones clave para garantizar la corrección de la recuperación.
  
- Es recomendable volver el estado a una línea de recuperación.

#### Checkpoint independiente

- Cada proceso graba su estado local ocasionalmente y de manera no coordinada. 
  
- Para descubrir una línea de recuperación, cada proceso debe retroceder a su estado guardado más reciente.
  
- Si estos estados locales conjuntamente no forman un estado global consistente requiere que otro proceso retroceda a un estado anterior. 
  
- Esto puede desencadenar un efecto dominó.

#### Checkpoint coordinado

- **Costo Elevado**: La toma de checkpoints es una operación costosa y puede penalizar severamente el rendimiento.
  
- **No Determinismo**: Si solo se usa checkpointing, el comportamiento después de la recuperación puede ser diferente al original debido a que pueden recibirse los mensajes en orden o tiempos diferentes de los que se había recibido antes de la falla.

Entonces vemos que no es una decisión trivial cuál de los 2 usar.

#### Message logging

- **Idea Clave**: Si la transmisión de mensajes puede ser "reproducida" (replayed), se puede restaurar un estado consistente global.
  
- **Mecanismo**: 
	- Se parte de un estado previamente checkpointed.
	- Todos los mensajes enviados desde ese checkpoint se retransmiten y manejan/ejecutan de nuevo.
	  
- **Beneficios**: 
	- Permite restaurar un estado más allá del checkpoint más reciente sin el alto costo de nuevos checkpoints. 
	- Facilita una reproducción exacta de los eventos.
	- Mayor eficiencia en la práctica, al requerir menos checkpoints.

## Resiliencia de procesos

**Objetivo** = hacer que un grupo de procesos se comporte como un único proceso más robusto.

**Metodología** = replicación de procesos.

**Desafío** = mantener la coherencia y coordinación entre los procesos replicados para que actúen como una sola entidad fiable.

### Organización de grupos de procesos

Pueden ser:

- Todos los procesos iguales, decisiones colectivas
  
- Un coordinador y procesos trabajadores

![[Pasted image 20251023200834.png]]

Hay que gestionar la creación y eliminación al igual que el ingresar o explusar procesos del grupo. Pueden ser decisiones centralizadas o descentralizadas.

### Algoritmos de consenso

**Supuesto**: en un sistema tolerante a fallos, todos los procesos ejecutan los mismos comandos, en el mismo orden, de igual manera que todo el resto de los procesos sin errores. 

**Problema**: ¿Cómo conseguir consenso sobre el comando concreto a ejecutar? 

**Enfoques**: El algoritmo de consenso elegido depende del modelo de fallo que el sistema debe tolerar.

#### K-Fault tolerant

> Un sistema es k-fault tolerant si puede sobrevivir a fallos en k componentes y seguir cumpliendo sus especificaciones.

La cantidad de componentes necesarios para lograr la $k$-tolerancia depende directamente de la malignidad de la falla, ya que la "especificación" del sistema cambia según lo que se puede confiar:

| **Protocolo/Enfoque**                              | **Fallos Tolerados**                                                                                | **Requisitos Mínimos**                                              | **Razón / Comportamiento**                                                                                                                              |
| -------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Tolerancia Práctica a Fallos Bizantinos (PBFT)** | Fallos Arbitrarios (Bizantinos)                                                                     | **$3k + 1$** procesos para tolerar $k$ fallos arbitrarios           | El modelo más robusto, requiere una mayoría de dos tercios de réplicas honestas para superar la mentira de $k$ procesos maliciosos.                     |
| **HotStuff**                                       | Fallos Arbitrarios (como una mejora de PBFT)                                                        | **$3k + 1$** procesos                                               | Similar a PBFT, requiere el doble más uno para lograr la finalidad rápida en entornos Bizantinos.                                                       |
| **Paxos**                                          | Fallos por Caída (_crash failures_). Asume comunicación no fiable y sistemas parcialmente síncronos | **$2k + 1$** servidores para tolerar $k$ fallos de caída silenciosa | Requiere una mayoría absoluta ($k+1$) para lograr el consenso y sobrevivir a $k$ fallos de caída (_Fail-Silent_ o _crash_).                             |
| **Raft**                                           | _Fail-Noisy_ (la caída es detectada correctamente en algún momento)                                 | $2k + 1$ (Implícito)                                                | Como Raft es un algoritmo basado en líderes y mayoría, requiere una **mayoría absoluta** ($k+1$) para ser elegido y replicar el _log_, similar a Paxos. |
| **Basado en Inundación (_Flooding_)**              | _Fail-Stop_ (detectables de forma fiable)                                                           | -                                                                   | Protocolo de comunicación que asume la falla más benigna (parada), donde el proceso simplemente deja de comunicarse.                                    |
| **Tolerancia a Fallas Silenciosas**                | _Fail-Silent_ (No miente)                                                                           | **$k + 1$** componentes                                             | Solo se necesita un componente funcional ($k+1$) para que el sistema siga operando (vivacidad).                                                         |
| **Tolerancia a Fallas Fail-Safe**                  | _Fail-Safe_ (Bizantina/Miente)                                                                      | **$2k + 1$** procesos                                               | Se necesita una **mayoría** de componentes funcionales y honestos ($k+1$) para superar a los $k$ componentes fallidos.                                  |

![[Pasted image 20251023202344.png]]

##### Limitaciones

- **Rendimiento**: replicación y organización de procesos implican una pérdida potencial de rendimiento debido a la gran cantidad de mensajes que deben intercambiarse para alcanzar el consenso.
  
- **Proceso Asíncronos**: Es imposible alcanzar el consenso en un sistema asíncrono si existe incluso un único proceso defectuoso (no podemos diferenciar si está caído o está lento).
  
- **Teorema CAP**: Los sistemas distribuidos se espera que tengan particiones lo que obliga a elegir entre C o A.

## Comunicación cliente-servidor confiable

Si hay errores en canales de comunicación, nos interesa:

- poder manejar los errores.

 - asegurar que los mensajes se procesen una sola vez o al menos de manera predecible.

Existen 2 categorías de conexión:

- **Point to point**: confiabilidad basada en protocolo TCP, ante fallo puede intentar establecer conexión de vuelta.
- **RPC**: tiene por objetivo hacer parecer que hacemos llamadas locales.

### RPC

#### Errores

1. No se puede conectar con el servidor 
2. Se pierde el mensaje enviado por el cliente 
3. El servidor muere luego de recibir el mensaje 
4. El mensaje de respuesta del servidor se pierde 
5. El cliente muere luego de enviar el mensaje

#### Soluciones

1. Handlear una excepción. Pero se tendría la ilusión de estar usando un servidor local. 
   
2. Configurar un Timeout en el cliente con un retry. 
   
3. Timeout con retry pero como aseguras que no haya reproceso? 
	1. At least once (al menos una vez) 
	2. At most once (a lo sumo una vez) 
	3. No guarantees (sin garantías)
	   
4. Timeout con retry 
	1. Retransmisión 
	2. Operaciones **idempotentes**. No siempre se puede (ver saldo vs transferir plata) 
	3. Número de secuencia en los mensajes. Requiere mantener estado. 
	4. Bit de retransmisión agregado a la solicitud.

5. Existen algunas propuesta complejas y con limitaciones. Se recomienda no hacer nada y que luego del reinicio se pueda volver a un estado anterior a su reinicio (ver Recuperación).

## Comunicación multicast confiable

**Objetivo** = Asegurar que los mensajes sean entregados a todos los miembros de un grupo.

### Approaches

#### Punto a punto

![[Pasted image 20251023204037.png]]

No escala!

#### En paralelo

![[Pasted image 20251023204105.png]]

Escalable

### Orden de mensajes

Un mensaje enviado a un grupo debe ser entregado a cada miembro no defectuoso de ese grupo, o a ninguno de ellos si el remitente falla durante la transmisión. Eso se llama: sincronía virtual establece "**todo o nada**".

Pero no aseegura el orden de entrega entre mensajes. Para ello, se distinguen cuatro tipos principales de ordenamiento. 

| **Protocolo/Enfoque**              | **Nivel de Ordenación**      | **Garantía Adicional de Atomicidad (Tolerancia a Fallas)** | **Restricción Lógica Impuesta**                                                                                                                                                                         |
| ---------------------------------- | ---------------------------- | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Multicast No Ordenado**          | **Ninguno**                  | No garantiza atomicidad.                                   | Los mensajes pueden ser recibidos en cualquier orden por cualquier receptor.                                                                                                                            |
| **Multicast Ordenado FIFO**        | **Parcial (Por Emisor)**     | No garantiza atomicidad.                                   | Los mensajes de un _mismo_ emisor ($P$) se reciben en el orden en que $P$ los envió.                                                                                                                    |
| **Multicast Ordenado Causalmente** | **Parcial (Por Causalidad)** | No garantiza atomicidad.                                   | Si un mensaje es la causa de otro, la causa ($M_1$) se entrega antes que el efecto ($M_2$) a todos.                                                                                                     |
| **Entrega Totalmente Ordenada**    | **Total y Única**            | No garantiza atomicidad _per se_.                          | Todos los mensajes de _todos_ los emisores se entregan a _todos_ los procesos en la **misma secuencia global**.                                                                                         |
| **Atomic Multicast**               | **Total y Única**            | **Sí** (Todo o Nada)                                       | La misma que la Entrega Totalmente Ordenada, pero con la **garantía de que la entrega es a todos o a nadie**, a pesar de fallas (_Fail-Stop_).                                                          |
| **Atomic FIFO**                    | **Total y FIFO**             | **Sí** (Todo o Nada)                                       | Garantiza la **ordenación total** de todos los mensajes, _y_ mantiene la propiedad FIFO de los mensajes de un mismo emisor. (Es más fuerte que la Atomicidad pura, que solo garantiza el orden global). |
| **Atomic Causal**                  | **Total y Causal**           | **Sí** (Todo o Nada)                                       | Garantiza el **orden causal** (causa antes que efecto), y **también** garantiza la atomicidad de entrega y orden total para los mensajes concurrentes.                                                  |

Repasar diferencia entre atómico y no atómico más allá de la tolerancia a fallas

## Transacciones en servicios

### Contexto

Sistemas complejos con relaciones entre ellos, muchas veces tiene que operar sincronizados 

**Ejemplo**: reservar hotel y vuelo ¿Cómo aseguro que se haga todo o nada? Cada uno es una operación distinta 

**Dos opciones en principio**: 
- Commit distribuído y atómico 
- Compensaciones

### Commit distribuido

Extensión del problema de Atomic Multicast. 
- Atomic multicast se enfoca en el envío. 
- Commit se enfoca en que la operación se “ejecute” en cada uno de los procesos del grupo.
- 
El protocolo más conocido para abordar esto es el Protocolo de Confirmación de Dos Fases (2PC). Este involucra a un coordinador y varios participantes.

### Compensaciones

Deshacer una operación ya realizada 

Esto es una gestión para deshacer algo ya realizado. 
- Envío un mail para avisar 
- Hago un contramovimiento

---
