
**1- ¿Por qué replicar?**

Hay 2 motivos por los cuales es bueno replicar:

- **Aumentar confiabilidad del sistema**: si el sistema principal se cae, la réplica lo mantiene.
- **Mejorar el rendimiento**: crecer en tamaño o en alcance geográfico.

chat:

###### Aumentar Confiabilidad del Sistema (Disponibilidad y Tolerancia a Fallos)

- **Función:** La replicación garantiza que la entidad (servicio o datos) permanezca accesible incluso si uno o más de sus componentes fallan.
    
- **Mecanismo:** Si el nodo principal se cae, la carga de trabajo es automáticamente transferida a una de las réplicas (mecanismo conocido como _failover_).
    
- **Ejemplo:** Los servidores DNS autoritativos se replican para que, si el servidor principal deja de responder, las consultas se dirijan a los servidores DNS secundarios y el dominio siga resolviéndose.

###### Mejorar el Rendimiento (Escalabilidad y Baja Latencia) 

- **Función:** La replicación permite que el sistema maneje más carga (escalabilidad) y que las respuestas lleguen más rápido a los usuarios (baja latencia).
    
- **Mecanismo:**
    
    - **Distribución de Carga:** Se distribuyen las peticiones de lectura entre varias réplicas, evitando que un único nodo se sature (crecer en **tamaño** de carga).
        
    - **Localidad (Alcance Geográfico):** Al ubicar réplicas de los datos cerca de los usuarios finales (por ejemplo, en diferentes regiones geográficas), se reduce la distancia física que debe recorrer la señal, disminuyendo así la **latencia** percibida.


**2- ¿Qué problema puede ocurrir cuando se tienen múltiples copias?**

- Falta de consistencia entre réplicas
- Modificar una copia hace que quede distinta al resto => se deben modificar otras copias
- Tenemos que definir cuando y como hacer las réplicas, esto genera un costo

chat:
###### Consecuencias de la Inconsistencia

La necesidad de gestionar y resolver esta inconsistencia temporal genera los siguientes problemas y costos:

1. **Lecturas Obsoletas (_Stale Reads_):**
    
    - Si un cliente lee datos de una réplica que aún no ha recibido la última actualización, obtiene información **incorrecta o desactualizada**.
        
    - Esto es un problema crítico, por ejemplo, en sistemas bancarios o de inventario, donde se requiere una **Consistencia Fuerte** (todas las réplicas deben ser idénticas en todo momento).
        
2. **Conflictos de Actualización:**
    
    - Si dos clientes modifican diferentes réplicas del mismo dato **simultáneamente** antes de que las réplicas se sincronicen, el sistema debe decidir qué actualización es la "correcta" o la final.
        
    - Resolver estos conflictos añade una gran complejidad al sistema.
        
3. **Aumento de la Latencia y Costo Operacional:**
    
    - Para garantizar algún nivel de consistencia (evitar que la inconsistencia dure mucho), el sistema debe incurrir en altos **costos de sincronización**.
        
    - Se deben definir **protocolos de replicación** (como _Primary-Backup_ o _Quorum_) que dictan **cuándo y cómo** se propagan las actualizaciones. Estos protocolos consumen ancho de banda, capacidad de cómputo y aumentan la latencia de escritura, ya que la modificación no se considera completa hasta que se ha propagado a un número suficiente de réplicas.


**3- ¿Existe alguna relación entre la replicación y la escalabilidad? Explicar.**

Si, existe relación. Cuando vamos a mejorar la escalabilidad de un sistema podemos tener 2 approaches:

1. Cacheo
2. Replicación

La replicación permite tener más copias del mismo proceso mejorando la disponibildad (más capacidad de atender y seguridad de datos), la velocidad (se pueden hacer réplicas en zonas geográficas estratégicas y distribuir carga) y la confianza (ante falla pasar a otra réplica).

**4- Defina brevemente qué es un modelo de consistencia.**

Un contrato entre los procesos y el almacén de datos (data store). El contrato consiste en: "Si el cliente se comporta de cierta forma, el centro puede funcionar correctamente".

chat:

Un modelo de consistencia es fundamentalmente un **contrato** (o conjunto de reglas) entre los procesos que interactúan con un almacén de datos (el _data store_ o la base de datos distribuida) y el almacén mismo.

El contrato define las **garantías** que el almacén de datos ofrece a los procesos sobre **el orden y la visibilidad de las operaciones de lectura y escritura**.

**En términos simples:** Define qué tan actualizada y coherente es la información que los distintos procesos ven al mismo tiempo, después de que se ha realizado una escritura o modificación.

- **Proceso $\rightarrow$ Contrato (Reglas de Visibilidad) $\rightarrow$ Almacén de Datos**
    

Si los procesos (clientes) y el almacén de datos (servidores replicados) cumplen con estas reglas, se asegura que el sistema se comporte de una manera predecible, gestionando la **inconsistencia temporal** inherente a la replicación.

###### Componentes Clave

1. **Reglas de Escritura:** Cuándo se considera que una escritura ha sido completada (ej., después de que se ha actualizado la réplica principal, o solo después de que se ha propagado a todas las réplicas).
    
2. **Reglas de Lectura:** Cuándo una lectura debe ver la última escritura (ej., inmediatamente, o después de un retraso).
    

Diferentes modelos de consistencia (como la Consistencia Fuerte, la Consistencia Secuencial o la Consistencia Final) varían según la estrictez de este contrato.

**5- En general, ¿en qué se diferencian los modelos de consistencia centrados en datos de aquellos modelos de consistencia centrados en el cliente?**

Cuando hablamos de consistencia basada en datos lo que queremos asegurar es que el almacen de datos y sus replicas manejen una version consistente. Buscamos manejar los eventos concurrentes entre procesos para garantizar que haya consistencia.

Cuando hablamos de consistencia basada en cliente, lo que nos interesa es que el cliente y sus operaciones sean consistentes. Esto significa que la prioridad esta puesta en que sus acciones tengan consistencia, pero no existe interes en que las replicas de los distintos clientes tengan consistencia.


chat:

La distinción clave está en el **alcance** de la garantía:

- Los modelos **Centrados en Datos** garantizan el orden de las operaciones para **todos los procesos** y **todas las réplicas**.
    
- Los modelos **Centrados en el Cliente** solo garantizan el orden de las operaciones para un **proceso individual**.

###### 1. Modelos de Consistencia Centrados en Datos (Data-Centric Consistency) 

Estos modelos se centran en establecer un **orden global y estricto** en las operaciones que ven _todos_ los procesos del sistema.

|**Aspecto**|**Descripción**|
|---|---|
|**Alcance de la Garantía**|**Global.** El objetivo es que todas las réplicas del almacén de datos (el _data store_) parezcan ser una única copia, única y consistente, sin importar qué proceso la consulte.|
|**Foco**|**El Almacén de Datos.** Aseguran que los eventos concurrentes sean gestionados para que las escrituras se propaguen y se vean en el mismo orden por todos.|
|**Ejemplos**|**Consistencia Estricta** (la más fuerte y difícil), **Consistencia Secuencial**, **Consistencia Causal**.|
|**Costo**|**Alto.** Requieren protocolos de sincronización complejos (ej., _quorums_, bloqueos) que limitan la escalabilidad y aumentan la latencia.|

###### 2. Modelos de Consistencia Centrados en el Cliente (Client-Centric Consistency) 

Estos modelos relajan el requisito de un orden global estricto y se enfocan en las garantías que son importantes para un **único cliente** (o proceso) a lo largo de sus interacciones con el sistema.

|**Aspecto**|**Descripción**|
|---|---|
|**Alcance de la Garantía**|**Local.** El objetivo es que las operaciones de un cliente individual sean coherentes, independientemente de lo que vean otros clientes. Esto es crucial cuando se usa **replicación débil/perezosa** (como la _consistencia final_).|
|**Foco**|**El Cliente/Usuario.** Garantizan que las acciones de ese cliente tengan sentido para él.|
|**Ejemplos**|**Consistencia de Lecturas Monotónicas** (si un cliente ve un valor $X$, nunca verá un valor anterior a $X$ en el futuro), **Consistencia de Escrituras Monotónicas** (las escrituras de un cliente se ejecutan en el orden que él las hizo), **Consistencia de Lecturas después de Escribir**.|
|**Costo**|**Bajo.** Permiten una alta escalabilidad y disponibilidad porque no fuerzan la sincronización global. Las inconsistencias se resuelven a nivel de cliente, a menudo rastreando las operaciones del cliente con _tokens_ o _timestamps_.|