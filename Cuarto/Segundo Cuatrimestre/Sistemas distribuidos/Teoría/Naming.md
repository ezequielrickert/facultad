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

#### Esquemas de ubicación jerarquica

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
