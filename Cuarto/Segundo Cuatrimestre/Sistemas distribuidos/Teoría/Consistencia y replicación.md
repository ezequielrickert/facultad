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
- **Desviación con respecto al ordenamiento de operaciones de actualización**: número máximo de actualizaciones pendientes o conflictivas que una réplica puede tener sin haberlas integrado aún en el orden correcto.

### Conit

> Especifica la unidad con la que se medirá la consistencia.

Hay ventajas y desventajas entre mantener conits de granularidad fina y aquellos de granularidad gruesa 

Si una conit representa muchos datos, tal como una base de datos completa, entonces las actualizaciones se aplican a todos los datos incluidos en la conit. 

Si una conit representa pocos datos, y éstos son independientes, se pueden actualizar por separado y en el momento.

## Modelo de consistencia basado en datos

En la consistencia centrada en datos, se proporciona una vista consistente a lo largo del sistema de un almacén de datos.

### Consistencia secuencial

**Necesario**: cuando se generan actualizaciones en réplicas, éstas tendrán que acordar un ordenamiento global de esas actualizaciones.

> El resultado de cualquier ejecución es el mismo que si las operaciones (de lectura y escritura) de todos los procesos efectuados sobre el almacén de datos se ejecutaran en algún orden secuencial y las operaciones de cada proceso individual aparecieran en esa secuencia en el orden especificado por su programa

Transparente para los usuarios

Aunque las operaciones (lecturas y escrituras) ocurren concurrentemente en diferentes máquinas en tiempos reales distintos, el sistema se comporta como si todas las operaciones se hubieran ejecutado en **algún orden secuencial único y globalmente consistente**, y **todas las máquinas (procesos)** ven y acuerdan este mismo orden.

![[Pasted image 20251022201557.png]]

(a) Almacén de datos secuencialmente consistente.
(b) Almacén de datos que no es secuencialmente consistente.

Se define una firma, esta es el output en el orden que definen los procesos. Vamos a ver el siguiente ejemplo:

![[Pasted image 20251022203355.png]]

Vemos que el orden que definen los procesos para garantizar consistencia es:
1. P1
2. P2
3. P3

Es por esto, que desde Execution 2 a 4 encontramos diferencia entre prints y signature. El prints es como realmente sucedieron las transacciones y el signature es como lógicamente se organizan.


IMPORTANTE:
Para que una ejecución sea **secuencialmente consistente**, **no interesan los tiempos de cambio reales (el tiempo físico)** de las operaciones; únicamente interesa que **se respete el _signature_ globalmente en el sistema**.

### Consistencia causal

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
  
  Una conversación con el chat ya que la segunda afirmación puede ser un poco fuerte
  
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
