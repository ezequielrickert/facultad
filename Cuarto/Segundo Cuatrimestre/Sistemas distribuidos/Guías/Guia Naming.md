
**1- Escribir un ejemplo en donde la dirección de una entidad E necesita resolverse dentro de otra dirección para acceder en realidad a E.**

Vimos en el caso de DNS, que se pueden guardar lo que se llama CNAME. El CNAME no es la entidad en sí misma, sino una referencia a otra entidad. De esta forma, estamos teniendo que resolver otra dirección para poder tener la dirección a la entidad real. Esto implica resolver 2 direcciones distintas.

**2- En general, ¿cuál es la diferencia en la manera en que se implementan los nombres en sistemas distribuidos y no distribuidos?**

La diferencia radica en que en los nombres en sistemas distribuidos se encuentran dispersos tanto física como geográficamente. Esto se hace para poder tener mayor alcance y menor latencia. Esto como sabemos, trae mucha complejidad ya que hay que mantener consistencia y actualizar las replicas. De todos modos, permite poder escalar y sumar una cantidad grande de nombres dando la capacidad, por ser distribuido, de separar la carga entre distintos nodos.

Por otro lado, el no distribuido se encuentra en un unico nodo, esto trae muchas limitaciones. Se depende completamente de un nodo, de su capacidad de computo y de su alcance geográfico. La ventaja que tiene es la simplicidad de manejar todo en un lugar.
   
**3- Si no se cuenta con un sistema de nombres, ¿qué ocurre si una entidad ofrece más de un punto de acceso?**

Si tenemos más de un punto de acceso a una entidad, y no tenemos un punto de acceso, no queda claro cual es la dirección que se debe usar. Imaginemos que interactuamos con un servidor que tiene 3 proxys, ubicados en 10.0.0.1, 10.0.0.2, 10.0.0.3. Si ingreso primero por 10.0.0.1, altero estado y luego cambio a 10.0.0.2 este proxy no va a tener la metadata, estado e información que alteré en 10.0.0.1 causando incosistencia.

Ordenado por el chat:

###### Ambigüedad en la Identificación (¿Cuál Usar?)

Sin un nombre lógico (como un FQDN o URL) que actúe como una **dirección de alto nivel** para la entidad, el cliente debe interactuar directamente con una de las direcciones físicas (ej. IP).

- **Problema de Selección:** El cliente no sabe qué dirección elegir. Debe tener todas las IP preconfiguradas, y la selección se vuelve un asunto de **política del cliente** o un simple **azar**.
    
- **Pérdida de Abstracción:** El propósito de tener múltiples puntos de acceso (como proxys o servidores en un _cluster_) es mejorar el rendimiento y la disponibilidad. Un sistema de nombres permite que estos múltiples puntos sean abstractos bajo una única etiqueta lógica (ej. `mi-servicio.com`). Sin este nombre, la **abstracción se rompe**, y el cliente debe preocuparse por la infraestructura subyacente (las IP individuales).
    
- **Sin Balanceo de Carga:** No hay un mecanismo centralizado para dirigir las peticiones de manera inteligente (ej. al _proxy_ menos ocupado o al más cercano). Cada cliente debe elegir de forma independiente.
    

###### 2. Complicaciones en la Gestión de Referencias y Estado 

Tu ejemplo sobre la inconsistencia de estado es **muy acertado**. Este es el problema más grave en sistemas distribuidos.

- **Inconsistencia de Estado (El Problema del _Sticky Session_):** Si la entidad representa un servicio que mantiene **estado de sesión** (como un carrito de compras, un inicio de sesión, o tu ejemplo del "metadata" alterado), el cliente debe **volver siempre al mismo punto de acceso**. Si el cliente cambia de forma arbitraria de `10.0.0.1` a `10.0.0.2`, perderá la información de la sesión, causando el error de inconsistencia que mencionas.
    
- **Imposibilidad de Referencias Globales:** Si la IP de un _proxy_ se usa en una referencia o _link_ (ej. `http://10.0.0.1/recurso`), y ese _proxy_ cae, la referencia **se rompe para siempre**. Un nombre de dominio permite cambiar la IP subyacente sin romper el enlace.


**4- En qué se diferencian el sistema de direccionamiento plano (Flat Naming System) y el sistema de direccionamiento estructurado (Structured Naming System).**

La diferencia radica en a quien apunta el sistema de nombramiento. El objetivo de direccionamiento plano es mantenerlo simple y rápido para el entendimiento de las máquinas y su comunicación. Vimos que en este caso, son nombres más básicos (suelen ser numéricos) que sirven como identificadores.

Por otro lado, el direccionamiento estructurado está pensando para los humanos, haciendo que se referencie a las entidades en base a nombres fáciles de recordar, alfanuméricos que no mutan.

|**Característica**|**Direccionamiento Estructurado**|**Direccionamiento Plano**|
|---|---|---|
|**Organización**|Jerárquica (Árbol, niveles)|No Jerárquica (Lista o _Pool_)|
|**Audiencia**|**Humanos** (Fácil de recordar)|**Máquinas** (Fácil de procesar)|
|**Escalabilidad**|Alta, mediante **Delegación de Autoridad**|Baja/Limitada, requiere **Autoridad Central**|
|**Ejemplo**|DNS (`google.com`)|Direcciones MAC, _Hashes_ de objetos|

**5- En la solución de difusión por broadcast para Flat Naming System:**

**a- ¿En qué capa del Modelo de referencia OSI operaría?**

Enlace y Red

**b- ¿Qué limitaciones tiene?**

Se requieren muchos mensajes, 2n siendo n la cantidad de nodos.

chat:

La principal limitación del _broadcast_ en la resolución de nombres planos es de otra naturaleza:

- **Saturación del Tráfico (_Broadcast Storm_):** En una red grande, cada nodo debe procesar cada mensaje de _broadcast_. Si muchos nodos buscan nombres al mismo tiempo, el tráfico de _broadcast_ consume un ancho de banda considerable y tiempo de CPU, degradando el rendimiento de toda la red.
    
- **Limitación Geográfica (Escalabilidad):** Las redes modernas están segmentadas por _routers_ o _switches_ de Capa 3. Por diseño, los _routers_ **no reenvían** el tráfico de _broadcast_ a otras subredes o a Internet. Esto restringe la resolución de nombres planos a un único segmento de red local, haciéndola **inútil** para sistemas distribuidos amplios.
    
- **Inseguridad:** El _broadcast_ expone las solicitudes de nombres y, a menudo, las direcciones físicas (MAC) a todos los nodos de la red, lo que plantea problemas de seguridad.

**c- ¿Cuál es el protocolo más comúnmente utilizado para la difusión por Broadcast en Flat Naming System?**

ARP

chat:

**ARP** es el ejemplo paradigmático de la difusión por _broadcast_ en una red local.

- **Función:** ARP se utiliza para **resolver una dirección IP (Capa 3) conocida en una dirección MAC (Capa 2) desconocida** en la misma subred.
    
- **Mecanismo:** El nodo que necesita una dirección MAC envía un mensaje de _broadcast_ (**ARP Request**) a toda la red preguntando: "¿Quién tiene la IP $X$?" El nodo que posee esa IP responde con su dirección MAC.

**6- De acuerdo al protocolo, ¿Como se sabe que dirección MAC está asociada con una dirección IP?**

 El nodo que necesita una dirección MAC envía un mensaje de _broadcast_ (**ARP Request**) a toda la red preguntando: "¿Quién tiene la IP $X$?" El nodo que posee esa IP responde con su dirección MAC.

**7- De acuerdo al sistema de nombres estructurado, ¿Cómo se denomina el mecanismo para saber cómo y dónde comenzar la resolución de nombres?**

A esto se le llama mecanismo de clausura, se encarga de seleccionar el nodo inical en un espacio de nombres sobre el que se va a iniciar. 

chat:

El **Mecanismo de Clausura** es el componente o protocolo que permite a un cliente (o a un _resolver_ recursivo) **saber dónde comenzar** la búsqueda de una dirección dentro de un espacio de nombres jerárquico y distribuido.

En el contexto del Sistema de Nombres de Dominio (**DNS**), este mecanismo se implementa mediante la configuración de los **servidores raíz (Root Servers)**.

###### Implementación en DNS

Para resolver un nombre estructurado como `www.ejemplo.com`, el proceso de clausura asegura que la resolución siempre comience en un punto conocido:

1. **Conocimiento Inicial:** Todo _resolver_ de DNS (el software que realiza la búsqueda, típicamente en tu sistema operativo o ISP) está preconfigurado con una lista de **direcciones IP** de los **13 servidores raíz** de DNS (Root Servers).
    
2. **Punto de Inicio:** Cuando un cliente necesita resolver cualquier nombre por primera vez, utiliza esta lista preconfigurada para contactar a uno de los servidores raíz. Este acto de usar un conjunto de direcciones conocidas para comenzar una búsqueda en un espacio de nombres jerárquico es la **clausura**.
    
3. **Delegación Continua:** A partir de ahí, el servidor raíz no resuelve el nombre, sino que **delega** la consulta a los servidores de Dominio de Nivel Superior (TLD) (`.com`), y estos a su vez **delegan** al servidor autoritativo de la zona (`ejemplo.com`).