
# Naming

### Conceptos básicos

1. Juegan un papel importante en cualquier sistema informático.
2. Se utilizan para:
	1. Compartir recursos
	2. Identificar identidades de forma única
	3. Hacer referencia a ubicaciones

Si una entidad ofrece más de un punto de acceso, no está claro qué dirección utilizar como referencia.

![[Pasted image 20251024100915.png]]

Un nombre tiene que poder resolver el acceso a la entidad a la que se refiere. De esta forma, la permitimos que un proceso acceda a la entidad nombrada. Para poder lograr esto, debemos armar un **sistema de nombres**.

![[Pasted image 20251024101002.png]]

### Nombres

1. Se utilizan para designar entidades en un sistema distribuido.
2. Necesitamos acceder a ella mediante un punto de acceso (entidades que se nombran mediante una dirección).
3. Una entidad puede tener más de un punto de acceso (el punto de acceso es un dispositivo que hace de puente entre una red virtual y una física).

### Puntos de acceso

Una entidad puede ofrecer más de un punto de acceso, en este caso no está claro que dirección utilizar como referencia. Es por esto, que se vuelve mucho más sencillo asociar la entidad a un nombre independiente de su dirección. Además brinda mayor flexibilidad a los puntos de acceso.

Una característica del nombre es que son independientes de la ubicación.

### Tipos de nombres
* Direcciones
* Identificadores
* Nombres amigables (como DNS): hechos para el acceso y comprensión sencilla de los humanos.



## Sistemas de nombres

### Lista de sistemas

1. Flat Naming System
2. Structured names
3.  Named Based Routing

### Flat naming system

No contiene información sobre la ubicación del punto de acceso, no sabe como localizarlo. Debido a esto, los nombres no tienen estructura y, en principio, tampoco significado.

Tenemos 2 approaches para este mecanismo:
1. Forwarding pointers (punteros de difusión)
2. Broadcasting (envío masivo): 

Ambos sistemas sirven únicamente para redes locales.
#### Broadcasting

 Funciona como ARP, le pregunta a todos los nodos a quien le corresponde el nombre y que mande su dirección. Todos los procesos tienen que estar escuchando posibles peticiones.

#### Forwarding pointers

Se va appendeando a la cadena la dirección nueva cada vez que una entidad se mueve. Desreferenciar es sencillo y transparente porque simplemente es seguir la cadena hasta llegar a la nada. Sin embargo, crecer la cadena genera problemas de escalabilidad al tener mayor latencia de búsqueda innecesaria.

#### Home based approach

Se introduce una ubicación de origen para cada dispositivo, puede ser la ubicación donde fue creado el dispositivo. Luego, cuando el dispositivo cambia de ubicación, deja rastro de su nueva ubicación (dirección de cuidado o foreign). De esta forma, siempre que no se sepa la ubicación actual del dispositivo, se la busca en la dirección de origen y, si no está ahí, se translada a la nueva dirección. Este approach es muy útil para dispositivos móviles que cambian de ubicación constantemente.

Este sistema es muy utilizado para dispositivos móviles ya que poseen direcciones volátiles.

![[Pasted image 20251020122326.png]]


#### Distributed Hash Tables

En home based approach, vimos que se tiene que contactar con el origen cuando no se sabe la ubicación actual. Esto, nos podemos imaginar que genera mucha latencia por el paso extra.

Por lo que entiendo, dentro de cada red, cada nodo es responsable de tener una tabla de dirección de cierta cantidad de nodos. De esta forma, todas las redes más pequeñas contienen, de forma distribuida y descentralizada, la información de las direcciones. De esta forma, cuando pedimos una dirección a un nodo vecino, vamos saltando entre nodos hasta llegar al nodo que tiene la dirección que buscamos.

Reduce la latencia porque, si bien el nodo recurso se puede encontrar lejos geográficamente, el nodo que almacena su dirección se encuentra dentro de una red acotada, esto hace que pedir la dirección sea más rápido.

#### ==Esquemas de ubicación jerarquica==

Lo que yo entendí es:

Este ahora tiene más lógica por detrás, se dividen las redes en árboles, donde cada subred tiene su nodo padre y los nodos hijos forman redes subyacentes. Las direcciones de las entidades se guardan los nodos hoja. Los nodos intermedios contienen punteros a otros nodos únicamente si dentro de esas redes subyacentes se encuentran las direcciones de las entidades que le interesan.

![[Pasted image 20251022101058.png]]

La búsqueda ocurre de la siguiente forma:

1. Un cliente que busca un dispositivo envía una consulta a su propio nodo hoja (este es un servidor local que se encarga de administrar un área, esto genera eficiencia geográfica).

2. Si el dispositivo buscado no está en el mismo dominio, el nodo hoja reenvía la consulta a su nodo padre en la jerarquía.

3. La consulta continúa ascendiendo por el árbol hasta encontrar un nodo que tenga un puntero al subárbol que contiene la dirección del dispositivo.
   
4. Una vez que se encuentra el subárbol correcto, la consulta desciende por la jerarquía hasta que se localiza el nodo hoja que contiene la dirección del dispositivo.
   
5. El nodo hoja envía la dirección del dispositivo directamente al cliente que hizo la consulta.

#### Seguridad

Flat naming no tiene información sobre como resolver un nombre, dependemos plenamente del esquema de resolución de nombres. Esto hace que tengamos que ser muy rigurosos con 1 de 2:
1. Asegurar el proceso de resolución de nombres
2. Asegurar la asociación entre el identificador y la entidad



### Structured naming

Los nombres planos resultan buenos para las máquinas pero no para los humanos.

Como alternativa, existen los nombres estructurados. Estos:

 - Se componen de nombres simples y legibles para humanos.
 - Son utilizados para archivos y hosts en internet.

#### Name spaces

Se puede pensar como un grafo dirigido, donde existen 2 tipos de nodos:

1. **Leaf node**: representa una entidad nombrada, no tiene salidas. Tiene información representativa de la entidad como la ubicación.
2. **Directory node**: varias aristas salientes etiquetadas con un nombre. Cada nodo se considera una entidad.

![[Pasted image 20251020140451.png]]

Como se ve en la imagen, cada nodo almacena una tabla con el par clave (identificador del nodo) - valor (etiqueta de arista). Esta tabla se llama tabla de directorio.


* **Name resolution**: proceso de buscar un recurso en base a una dirección.
* **Mecanismo de clausura**: Saber cómo y donde iniciar la resolución de nombres. Este mecanismo se encarga de seleccionar el nodo inicial donde se debe iniciar la búsqueda en un espacio de nombres.

#### Implementación de espacio de nombres

* Tener en cuenta que es el corazón del servicio de nombres.
* Permite a los usuarios: agregar, remover y buscar nombres.
* En LAN se maneja un solo DNS, en casos WAN se hacen múltiples servidores de nombres (DNS). En el caso de la WAN, están estructurados jerárquicamente.

Para implementarlo, se asume que existe un único nodo raíz. Es conveniente separar la implementación en capas lógicas.

#### Distribución de espacio de nombres

* **Capa global**: son los nodos superiores, representan organizaciones o grupos.
* **Capa administrativa**: son el conjunto de nodos manejados dentro de una misma organización (por ejemplo, dentro de la misma universidad).
* **Capa de dirección**: nodos volátiles, suelen cambiar (host, directorios y archivos).

#### DNS

* Distribuido por capas como mencionado previamente.
* Principalmente utilizado para resolver IPs de hosts y servidores de correo.
* Organizado jerárquicamente como un árbol.

![[Pasted image 20251022140858.png]]

Veamos esta imagen para entender algunos conceptos:
1. Se le llama "dominio" a cualquier subarbol
2. La ruta desde el nodo hasta la raíz es el nombre del dominio. Entonces sería (eng.oracle.org). Para llegar desde la raíz hasta el dominio se lee a la inversa.

DNS gestiona la capa global y la administrativa, la capa managerial no se considera formalmente parte del sistema.

#### Sistema de nombres DNS

- Cada zona se implementa mediante un servidor de nombres, que prácticamente siempre se replica para garantizar la disponibilidad. 
  
- Una base de datos DNS se implementa como una (pequeña) colección de archivos. 
  
- El más importante contiene todos los registros de recursos de todos los nodos en una zona particular. 
  
- Este enfoque permite identificar los nodos de forma sencilla a través de su nombre de dominio.

Con respecto a que el más importante contiene todos los registros, entendí esto:

##### Escenario 1: Sin Delegación (La Mayoría de los Casos Simples)

Si el administrador de `vu.nl` no delega la administración de los subdominios (como `cs.vu.nl`), entonces **SÍ** tiene la información completa:

- El archivo de zona para `vu.nl` contendrá directamente los registros para:
    
    - `vu.nl`
        
    - `cs.vu.nl`
        
    - `www.cs.vu.nl`
        
    - `ftp.cs.vu.nl`
        

En este caso, todos los hijos (`cs.vu.nl` y sus hosts) son simplemente **registros** dentro del archivo de la zona padre (`vu.nl`). El servidor autoritativo de `vu.nl` resuelve todo.

##### Escenario 2: Con Delegación (Común en Organizaciones Grandes)

Si el administrador de `vu.nl` quiere que otro departamento (por ejemplo, el departamento de Ciencias de la Computación) administre su propio DNS, puede **delegar la subzona** `cs.vu.nl`.

- **Delegación:** Esto significa que el archivo de zona de `vu.nl` contiene un registro **NS** que apunta a un nuevo servidor de nombres que será el autoritativo para `cs.vu.nl`.
    
- **Resultado:** El servidor de `vu.nl` ya **NO** tiene la información completa (las IP) de `www.cs.vu.nl` o `ftp.cs.vu.nl`. Solo tiene el puntero (NS) hacia el servidor que sí la tiene.

Un registro puede contener

![[Pasted image 20251024093655.png]]

### Attribute-based naming

#### Qué es?

Los nombres planos y estructurados generalmente proporcionan una **forma única e independiente de la ubicación** para referirse a las entidades.

En particular, a medida que se dispone de más información, se vuelve importante buscar entidades de manera efectiva.

>Este enfoque requiere que el usuario pueda proporcionar simplemente una descripción de lo que está buscando.

Entre las formas en que se puede proveer descripciones, una forma popular en sistemas distribuidos es describir una entidad en términos de pares (atributo, valor).

>En este enfoque, se asume que una entidad tiene una colección de atributos asociada y cada atributo dice algo acerca de la entidad.

Para casos simples, por ejemplo, en un sistema de correo electrónico, los mensajes se pueden etiquetar con atributos para el remitente, el destinatario, el asunto, etc.

#### Desventajas

- En la mayoría de los casos, el diseño de atributos debe realizarse manualmente. 
  
- En la práctica, establecer valores de manera consistente por parte de un grupo diverso de personas es un problema en sí mismo. Por ejemplo, búsquedas en bases de datos de música y vídeo en Internet. 
  
- Para aliviar algunos de estos problemas, se han llevado a cabo investigaciones para unificar las formas en que se pueden describir los recursos.

#### Directory services (RDF)

- Fundamental para el modelo RDF es que los recursos se describen como tripletes que constan de un sujeto, un predicado y un objeto. 
  
- Por ejemplo, (Persona, nombre, Alicia) describe un recurso denominado Persona cuyo nombre es Alicia. 
  
- En RDF, cada sujeto, predicado u objeto puede ser un recurso en sí mismo.
  
- Esto significa que Alice puede implementarse como una referencia a un archivo que puede recuperarse posteriormente.
  
- En el caso de un predicado, dicho recurso podría contener una descripción textual de ese predicado.
  
- Los recursos asociados a sujetos y objetos pueden ser cualquier cosa. Las referencias en RDF son esencialmente URLs.

---

# Consistencia y replicación

## Conceptos básicos

> Los datos generalmente se replican para mejorar la confiabilidad o el rendimiento.

Esto genera una dificultad y complejidad importante, mantener la consistencia entre las réplicas.

La complejidad de la consistencia existe cuando múltiples procesos acceden a información compartida.

La consistencia es algo más acordado, que pueden esperar los procesos al realizar operaciones sobre los datos.

### Por qué replicar?

- **Aumentar confiabilidad del sistema**: si el sistema principal se cae, la réplica lo mantiene.
- **Mejorar el rendimiento**: crecer en tamaño o en alcance geográfico.

### Problemas

- Falta de consistencia entre réplicas
- Modificar una copia hace que quede distinta al resto => se deben modificar otras copias
- Tenemos que definir cuando y como hacer las réplicas, esto genera un costo

### Escalabilidad

Para escalar, se suelen utilizar 2 métodos:
1. Replicación
2. Cacheo

Hacer copias de los datos cerca de los procesos que las utilizan ayuda a reducir los tiempos ya que disminuye el tiempo de acceso.

Se requiere mucho ancho de banda, esto se debe a que se tienen que estar mandando muchos datos entre copias para mantenerse actualizadas.

La consistencia se da cuando todas las copias son iguales.

> Una operación de lectura realizada a cualquier copia siempre devolverá el mismo resultado.

Cuando se actualiza una copia, se tiene que propagar el cambio al resto de las copias para que haya consistencia en operaciones posteriores **independientemente** de la copia en la que se realice la operación. Tener esta rigurosidad en la consistencia, se llama "consistencia hermética".

### Atomicidad

* Una actualización ocurre en todas las copias como una sola operación o transacción.
* Cuantas más réplicas tenemos, menos velocidad tendremos a la hora de realizar las operaciones atómicas.

### Almacén de datos

> Objeto que conforma los datos, que pueden estar compartidos.

* Puede estar físicamente distribuida en distintas máquinas.
* Se asume que el proceso que puede acceder a los datos, tiene una copia local o cercana.
* Cuando se hace una modificación, se actualizan las copias.

![[Pasted image 20251022184134.png]]

### Modelo de consistencia

> Un contrato entre los procesos y el almacén de datos (data store).

El contrato consiste en: "Si el cliente se comporta de cierta forma, el centro puede funcionar correctamente"

Como vimos previamente con los relojes, si no tenemos un único reloj global, es difícil con operaciones concurrentes definir cuál es la última operación.

Para poder proponer una solución a este problema, nacen los modelos de consistencia.

Cada modelo restringe los valores que puede devolver una operación de lectura.

Como sabemos, hay un tradeoff: 

|                       | Facilidad de uso | Rendimiento |
| --------------------- | ---------------- | ----------- |
| Mayores restricciones | +                | -           |
| Menores restricciones | -                | +           |

### Consistencia continua

La consistencia continua permite a los diseñadores de sistemas **especificar y cuantificar la cantidad máxima de inconsistencia tolerable**. Esto se suma a lo que hablamos del "acuerdo" o "contrato" entre partes.

Algunas formas de desvío son:

- **Desviación en valores numéricos entre las réplicas**: diferencia absoluta entre el valor más actualizado de un dato y el valor que tiene una réplica específica.
- **Desviación en el deterioro entre réplicas**: cantidad de tiempo que ha pasado desde que una réplica recibió (o hizo visible) la última actualización conocida globalmente.
- ==**Desviación con respecto al ordenamiento de operaciones de actualización**==: número máximo de actualizaciones pendientes o conflictivas que una réplica puede tener sin haberlas integrado aún en el orden correcto.

### Conit

> Especifica la unidad con la que se medirá la consistencia.

Hay ventajas y desventajas entre mantener conits de granularidad fina y aquellos de granularidad gruesa 

Si una conit representa muchos datos, tal como una base de datos completa, entonces las actualizaciones se aplican a todos los datos incluidos en la conit. 

Si una conit representa pocos datos, y éstos son independientes, se pueden actualizar por separado y en el momento.

## Modelo de consistencia basado en datos

En la consistencia centrada en datos, se proporciona una vista consistente a lo largo del sistema de un almacén de datos.

### ==Consistencia secuencial==

**Necesario**: cuando se generan actualizaciones en réplicas, éstas tendrán que acordar un ordenamiento global de esas actualizaciones.

> El resultado de cualquier ejecución es el mismo que si las operaciones (de lectura y escritura) de todos los procesos efectuados sobre el almacén de datos se ejecutaran en algún orden secuencial y las operaciones de cada proceso individual aparecieran en esa secuencia en el orden especificado por su programa

Transparente para los usuarios

Aunque las operaciones (lecturas y escrituras) ocurren concurrentemente en diferentes máquinas en tiempos reales distintos, el sistema se comporta como si todas las operaciones se hubieran ejecutado en **algún orden secuencial único y globalmente consistente**, y **todas las máquinas (procesos)** ven y acuerdan este mismo orden.

![[Pasted image 20251022201557.png]]

(a) Almacén de datos secuencialmente consistente.
==(b) Almacén de datos que no es secuencialmente consistente.==

Se define una firma, esta es el output en el orden que definen los procesos. Vamos a ver el siguiente ejemplo:

![[Pasted image 20251022203355.png]]

Vemos que el orden que definen los procesos para garantizar consistencia es:
1. P1
2. P2
3. P3

Es por esto, que desde Execution 2 a 4 encontramos diferencia entre prints y signature. El prints es como realmente sucedieron las transacciones y el signature es como lógicamente se organizan.


IMPORTANTE:
Para que una ejecución sea **secuencialmente consistente**, **no interesan los tiempos de cambio reales (el tiempo físico)** de las operaciones; únicamente interesa que **se respete el _signature_ globalmente en el sistema**.

### ==Consistencia causal==

> Diferencia entre eventos que potencialmente están relacionados por la causalidad y los que no lo están.

Si el evento b es causado o influenciado por un evento previo a, la causalidad requiere que todos los demás eventos vean primero al evento a, y después a b.

Si un evento y de lectura depende de otro evento x de escritura, ya sea porque accedan a la misma pieza de datos, entonces están causalmente relacionados.

Si dos procesos escriben espontánea y simultáneamente dos diferentes elementos de datos, éstos no están causalmente relacionados. Son operaciones concurrentes.

Para mantener un almacén de datos causalmente consistente debe cumplir con que:

*Escrituras que potencialmente están relacionadas por la causalidad, deben ser vistas por todos los procesos en el mismo orden. Las escrituras concurrentes pueden verse en un orden diferente en diferentes máquinas*

![[Pasted image 20251023100558.png]]

Bien, este es un poco más complejo de entender. En este caso (a) viola la consistencia mientras que (b) no. Hay que distinguir 2 casos importantes:

- **Concurrente**: no existe causalidad, por lo tanto, cumple con la consistencia.
- **Causal**: donde puede o no ser consistente.

En el caso de (a), vemos que existe una causalidad ya que P2 lee R(x)a antes de realizar la operación W(x)b. En el caso de (b), no existe tal operación de lectura del proceso 2 lo que hace que no exista causalidad, simplemente es un evento concurrente.

Ahora veamos el caso de (a), un evento causal que no cumple con la consistencia. Podemos ver que el proceso 4 lee R(x)a y luego R(x)b, esto cumple con el orden de los eventos. Sin embargo, si vemos el proceso 3, vemos que hace una lectura R(x)b y luego R(x)a. Acá se puede ver que no es consistente, expliquemos un poco por qué. La causalidad en este caso es:

W(x)a -> W(x)b

Ya que para que W(x)b suceda, se tiene que primero leer el valor R(x)a. Entonces, un ejemplo de un sistema causalmente consistente sería aquel en el que P3 lee, por ejemplo:

* R(x)a | R(x)b
* R(x)b | R(x)b

Como podemos imaginar, la consistencia causal presenta una gran complejidad que es mantener registro de que operación de escritura dependió de que operación de lectura.

### Operaciones de agrupamiento

- La consistencia secuencial y la causal están definidas al nivel de operaciones de lectura y escritura.
  
- Estos modelos se desarrollaron inicialmente para sistemas de multiprocesador de memoria compartida.

- La concurrencia entre programas que comparten datos generalmente se mantiene bajo control a través de mecanismos de sincronización para exclusión mutua y transacciones.

- Los datos manejados mediante una serie de operaciones de lectura y escritura están protegidos contra accesos concurrentes. “Unidad Atómica”


Esto es medio sobre entendido por prog concurrente, pero por las dudas lo incluyo:

- Se utilizan variables de sincronización compartidas.
  
- Cuando un proceso entra en su sección crítica, debe adquirir las variables importantes de sincronización.
  
- De igual manera, cuando abandona la sección crítica, debe liberar estas variables.
  
- Cada variable de sincronización tiene un propietario actual, a saber, el último proceso que la adquirió.


![[Pasted image 20251023105811.png]]
==Entiendo que la U es una operación rechazada por estar siendo usada, también entiendo que acá no hay consistencia, simplemente mostramos el comportamiento de los locks.==

## Consistencia centrada en el cliente

En la consistencia centrada en el cliente se verá una clase especial de almacenes de datos distribuidos. Se caracterizan por: 

* La falta de actualizaciones simultáneas. O bien. 

* La facilidad para resolver actualizaciones simultáneas

### Características

* La mayoría de las operaciones involucran la lectura de datos.
  
* Estos almacenes de datos ofrecen un modelo de consistencia muy débil, llamado de consistencia momentánea.
  
* Al introducir modelos de consistencia centrada en el cliente, es posible ocultar muchas inconsistencias de una manera relativamente barata.

### Consistencia momentánea

- Las bases de datos replicadas y distribuidas (de gran escala) suelen tolerar un relativamente alto grado de inconsistencia.
  
- Si no ocurren actualizaciones durante mucho tiempo, todas las réplicas gradualmente se volverán inconsistentes.

- En ausencia de actualizaciones, todas las réplicas convergen en copias idénticas unas de otras.

- Sólo requiere la garantía de que las actualizaciones se propaguen a todas las réplicas. 
  
- Se asume que sólo un pequeño grupo de procesos puede realizar actualizaciones.
  
- Funcionan bien siempre y cuando los clientes siempre accedan a la misma réplica.
  
- Los problemas surgen cuando en un periodo corto se accede a réplicas diferentes.

Además, hay un **supuesto muy importante**, es que se considera que es un sistema donde la mayoría de operaciones que se realizan son de lectura y hay muy pocas de escritura.

![[Pasted image 20251023111112.png]]

- La consistencia centrada en el cliente proporciona garantías para un solo cliente, para el almacén de datos de ese cliente.
  
- No se proporciona garantía alguna con respecto a accesos concurrentes de diferentes clientes.
  
  ==Una conversación con el chat ya que la segunda afirmación puede ser un poco fuerte:==
  
  "Si simplemente asegura garantía de concurrencia con el cliente. Como se manejan las operaciones concurrentes entre réplicas? Pienso que si, por ejemplo en whatsapp, se mandan mensajes al chat hay una actualización de los mensajes que se tiene que dar"

Respuesta:

"Cuando múltiples réplicas aceptan escrituras concurrentes (como dos usuarios enviando mensajes a un chat al mismo tiempo o editando el mismo documento), esas réplicas terminarán con estados diferentes.

El sistema utiliza un protocolo de **sincronización y resolución de conflictos** para converger hacia un estado final acordado."

#### Lectura monotónica

> Si un proceso lee el valor de un elemento de datos x, cualquier operación de lectura sucesiva sobre x que haga ese proceso devolverá siempre el mismo valor o un valor más reciente”

Dicho simple, maneja consistencia de versiones, no da valores más viejos que el último valor visto por el cliente
#### Escritura monotónica

> Una operación de escritura hecha por un proceso sobre un elemento x se completa antes que cualquier otra operación sucesiva de escritura sobre x realizada por el mismo proceso.

Dicho simple, las escrituras realizadas por un mismo cliente se aplican en todas las réplicas en el mismo orden en que fueron emitidas por ese cliente.

---

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
	2. **De transición de estado** (State-transition failure): Se desvía el correcto flujo de control. (==un sistema produce la salida correcta, pero **en el momento equivocado** o **después de que el estado interno ya ha cambiado**, haciéndola obsoleta o incorrecta para el contexto actual.==)
5. **Arbitrarios** (Arbitrary failures): puede producir respuestas incorrectas en en momentos arbitrarios (ej: respuestas inconsistentes).
	1. **Por omisión**: Un componente no realiza una acción que debería haber realizado.
	2. **Por comisión**: Un componente realiza una acción que no debería haber realizado.

![[Pasted image 20251023121423.png]]

==Está bueno ver esto, los errores que estamos viendo son tanto de capa física (se cae), de capa de red (falla en comunicación) como posiblemente de aplicación (arbitrarios).==


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

==Repasar diferencia entre atómico y no atómico más allá de la tolerancia a fallas==

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

# Seguridad

## Conceptos clave

- **Integridad**: las modificaciones de los datos sólo deben ser realizadas por clientes autorizados. Esto es para asegurar completitud y precisión de los datos.
  
- **Confidencialidad**: la información se divulga solo a partes autorizadas. 

Estas propiedades además permite cumplir con las normas de privacidad como las de la Ley de Protección de datos Personales de Argentina o la regulación Europea.

1. **Security Threat** (amenaza): 
	1. Unauthorized information disclosure 
	2. Unauthorized information modification 
	3. Unauthorized denial of use 
2. **Security Policy** (políticas): describe qué acciones tienen permitido o prohibido realizar las entidades dentro de un sistema (usuarios, servicios, datos, máquinas).

### Principios de seguridad

1. **Fail-safe defaults** (Valores por defecto seguros): el acceso se deniega por defecto. 
   
2. **Open design** (Diseño abierto): todos los aspectos deben ser revisables, no seguridad por oscuridad. Cualquier pueda saber qué mecanismo usa, cómo se usa, cómo se implementa, etc. 
   
3. **Separation of privilege** (Separación de privilegios): aspectos críticos no controlados por una única entidad. 
   
4. **Least privilege** (Mínimo privilegio): un proceso opera con los mínimos privilegios posibles.

![[Pasted image 20251023215340.png]]


## Criptografía

![[Pasted image 20251023215446.png]]


### Simétrica

> Se utiliza la misma clave para encriptar y desencriptar.

Casos de uso:

- Cifrado
- Claves de sesión
### Asimétrica

>Se utilizan claves diferentes pero ambas forman un par único (clave pública y privada).

Casos de uso:

- Demostrar autoría de un documento
- Establecer un canal seguro entre dos partes (HTTPS)

### Hashing

> Recibe un mensaje m de un largo arbitrario y produce un string h de tamaño fijo.

Caracterísiticas:

- **Función one-way**: es computacionalmente inviable encontrar la entrada original de un mensaje m a partir de un hash h.
  
- **Resistencia a colisiones débiles**: dado un mensaje de entrada m y su hash h = H(m), es computacionalmente inviable encontrar otra entrada diferente m' (donde m' ≠ m) tal que H(m) = H(m').
  
- **Resistencia a colisiones fuertes**: Esta propiedad es más estricta. Significa que, dada solo la función hash H, es computacionalmente inviable encontrar dos valores de entrada diferentes cualesquiera m y m' (donde m' ≠ m) tales que H(m) = H(m').


Permite detectar modificaciones de manera simple.

**Algunos casos de uso**:
- Firma digital 
- Guardado de passwords 
- Asegurarse que un descargable no fue modificado 
- Dificultad de minado en algoritmos PoW

### Firma digital

![[Pasted image 20251023220016.png]]

Sin embargo, puede suceder que se haya dañado la integridad del dato en por alguna incosistencia, por ejemplo de red.

Por esto tenemos un approach más complejo:

![[Pasted image 20251023220147.png]]

donde además mandamos el hash de la información para que luego se valide la integridad.


## Autenticación

- Validar la identidad que una persona, software o en genérico cliente dice tener. Asegurarse que estamos tratando con el usuario real. 
  
- Hoy no basta con solo un factor de autenticación sino que necesitamos múltiples capas de autenticación
  
### Métodos de autenticación

Autenticación basada en lo que un cliente: 
1. **Conoce**: contraseña o un número de identificación del cliente. 
2. **Tiene**: Tarjeta, token, teléfono. 
3. **Es**: biometría como reconocimiento facial o huella dactilar. 
4. **Hace**: biometría dinámica como un patrón de voz o de tipeo.

**Autenticación continúa**: no solo se pide validación al ingresar sino dentro de la sesión ante operaciones sensibles.

### Protocolos

#### Principio

Autenticación e integridad deben ir juntos, no le sirve de nada a Bob saber que un mensaje vino de Alice si no se puede asegurar que no fue modificado.

#### Protocolos
- Challenge Response (Desafío) 
- Key Distribution Center 
- public-key cryptography

Luego de la autenticación se usan Sessions Key. 
- Se utiliza solo durante el tiempo de vida del canal de comunicación. Al término se destruye. 
- Permite preservar las claves de mayor vida como las que se usan para autenticar. Un atacante con suficiente información encriptada podría deducirlas.

##### Challenge Response

![[Pasted image 20251023220701.png]]

##### Key Distribution Center

> Si tenemos muchos hosts se tienen que almacenar muchísimas claves compartidas. Se utiliza un trusted Party que genera claves de sesión. 

- Utilización de ticket, un mensaje cifrado de naturaleza temporal. 
  
- Existe un protocolo más avanzado: Needham-Schroeder.

![[Pasted image 20251023220933.png]]


##### Public-key cryptography

- Cada parte posee la public key del otro. 
- Alice envía su identidad y challenge firmado con pub key de Bob. 
- Importante tener garantía que Alice tiene pub key de Bob y no de alguien que lo impersonó.

![[Pasted image 20251023221120.png]]

##### Desafíos de los protocolos

1. Garantizar la integridad del mensaje y la autenticación mutua. 
2. Suplantación de identidad. 
3. Ataques de replicación. 
	1. ataque de red en el que un atacante intercepta una transmisión de datos válida (o parte de ella) y la **retransmite fraudulentamente** en un momento posterior para engañar al sistema receptor y hacer que ejecute una acción autorizada.
4. Gestionar eficientemente las claves secretas compartidas en sistemas grandes. 
5. Autenticidad de las claves públicas. 
6. No revelar información sensible antes de la autenticación de las partes.

## Autorización

> Conceder acceso a los recursos del sistema una vez autenticada una entidad.

![[Pasted image 20251023221513.png]]

 **Reference monitor**: 
 - Almacena quién puede hacer qué. 
 - Decide cuándo un sujeto puede hacer una operación.
   
### ==Enfoques==

1. Protección de la información o data, en su integridad. 
2. Protección sobre las operaciones a realizar. 
3. Protección sobre quién tiene acceso a la información.

### Políticas de control de acceso

1. **Control de Acceso Obligatorio** (Mandatory Access Control - MAC): la administración central define las políticas de acceso (ej. niveles de secreto: desde acceso público a top secret). 
2. **Control de Acceso Discrecional** (Discretionary Access Control - DAC): el propietario de un objeto decide quién tiene acceso (ej. permisos de archivos Unix). 
3. **Control de Acceso Basado en Roles** (Role-Based Access Control - RBAC): la autorización se basa en el rol del usuario en la organización (ej. profesor, estudiante). 
4. **Control de Acceso Basado en Atributos** (Attribute-Based Access Control - ABAC): control de acceso más granular basado en atributos de usuarios, objetos, entorno, conexión y administrativos.

### Delegation

#### Problema
- ¿Cómo delegar derechos de acceso sin compartir las credenciales principales? 
- ¿Cómo hacerlo sin tener que estar consultando al usuario que delegó todo el tiempo?

#### Solución
-  Un proxy permite a su poseedor operar con derechos y privilegios iguales o restringidos en comparación con el sujeto que lo concedió. 
- Incluso le puede permitir delegar a su vez alguno o todos los derechos a otro usuario.

#### Estructura proxy

![[Pasted image 20251024084914.png]]

![[Pasted image 20251024085100.png]]

1. Alice le da a Bob la lista de derechos firmada junto con la parte pública del secreto. También le da la secret key cifrada. 
2. Bob ejerce sus derechos, entrega lista firmada. 
3. Se le pide la respuesta a un nonce N firmado con la pub key. 
4. Si Bob la proporciona, el Server sabrá que los derechos listados fueron delegados a Bob por Alice.

#### OAuth

- Protocolo de delegación utilizado por organizaciones como Amazon, Google, Facebook, Microsoft y Twitter. 
  
- Propósito es permitir que una aplicación acceda a recursos a los que un usuario normalmente solo accedería a través de una interfaz web (por ejemplo, un cliente de correo local actuando en nombre de su propietario).

## Confianza

>La confianza es la certeza que una entidad tiene de qué otra se comportará de acuerdo con una expectativa específica.

- **Complemento a la Autenticación**: Si bien la autenticación verifica una identidad, la pregunta clave es: ¿cuánto vale esa autenticación si no se puede confiar en la persona? 
  
- **Limitación de Daños**: una autorización adecuada es vital, ya que puede usarse para limitar cualquier daño. 
  
- **Dependencia y Expectativas**: Si las expectativas se hacen lo suficientemente explícitas (es decir, especificadas), es posible que ya no sea necesario depender de la confianza.

### Ataques bizantinos

- Volviendo a procesos resilientes si queremos soportar ataques bizantinos debemos construir un sistema donde no haya confianza. 
- Esto se logra especificando algoritmos de consenso que no dependen de un individuo sino de un grupo. 
- Para lograr k-tolerancia a fallos bizantinos, se requiere un total de 3k + 1 servidores. 
	- Si quiero soportar 1 servidor dañino se requiere 4 servidores en total.

### Confianza en una identidad

- **Enfoque**: Se centra en la relación entre una identidad lógica y una entidad física. 
  
- **Problemática**: El problema fundamental es el ataque Sybil, donde un atacante presenta múltiples identidades lógicas para controlar una parte desproporcionada del sistema. 
  
- **Posible solución**: Se requieren mecanismos de contabilidad y prueba de coste que hagan que la clonación de identidades no sea rentable, como (Proof of Work, PoW) o (Proof of Stake, PoS) en blockchains.

Un ejemplo bajado a tierra es un usuario (identidad lógica) interactuando con una base de datos (entidad física)

>Especialmente relevante en sistemas descentralizados donde la reputación es crucial para la toma de decisiones.

|**Concepto**|**Ejemplo Práctico**|**Rol en la Red**|
|---|---|---|
|**Identidad Lógica**|La dirección de la billetera ($0xABCD\dots$) con la criptomoneda en _stake_.|Es el identificador que el protocolo usa para asignar bloques y recompensas.|
|**Entidad Física**|La persona o el servidor real que opera el nodo validador.|Es la entidad única que tiene el control sobre la clave privada y el _stake_.|
### Confianza en un sistema

- **Enfoque**: Se centra en cómo una estructura de datos distribuida puede generar confianza sin depender de terceros. 

- **Problemática**: La necesidad de asegurar la integridad de los datos y su inmutabilidad. 
  
- **Posible solución**: La confianza se deriva de la transparencia y la protección criptográfica. En blockchain se logra a través de la vinculación mediante hashes de los bloques. Asegura que cualquier cambio en un bloque se propague y sea detectable en toda la cadena.

> Garantía técnica de que la información almacenada en el sistema es correcta y no ha sido manipulada.

## Monitoreo y auditoría

**Objetivo** = garantizar que las políticas de seguridad se cumplen.

**Auditoría**: herramienta pasiva para saber qué fue lo que sucedió. Pero no ayuda a prevenir.

**Intrusion detection**: busca detectar actividades no autorizadas.


### Herramientas - Firewall

- Filtro de tráfico. 
  
- Puerta de entrada o salida hacia el mundo exterior. 
  
- Decide qué tráfico es permitido o descartado. 
  
- Categorías: 
	- **packet-filtering**: funciona como router y decide según el contenido de los headers del paquete. 
	- **application-level**: decide según el contenido del mensaje. Ejemplos: mail gateway o proxy gateway.


### Intrusion detection

- **SIDS - Signature-based Intrusion Detection Systems** 
	- Funcionan comparando patrones de intrusiones ya conocidas a nivel de red. 
	- Limitación: resultan menos útiles ante ataques nuevos o desconocidos 
	
- **AIDS - Anomaly-based Intrusion Detection Systems** 
	- Se basa en la premisa que se puede modelar un comportamiento típico del sistema para luego detectar cualquier comportamiento anómalo. 
	- Se basa especialmente en herramientas de IA como Machine Learning. 
	- Desafío: disminuir los falsos-positivos (etiquetado incorrecto como intrusión) manteniendo un número bajo de falsos-negativos (intrusiones omitidas).

