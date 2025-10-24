
### **Explicar por qué el modelo OSI es anticuado o poco representativo y por qué se opta por hablar de middleware**

OSI se planteo como un modelo, no como un protocolo. Esto quiere decir que era un planteamiento teórico, luego se establecieron varios protocolos basados en modelo OSI pero se volvía complejo.

Cuando hablamos de middleware, nos movemos un poco más al concepto de transparencia. Tenemos una capa que se encarga de realizar transformaciones y operaciones. Este no es un esquema tan estricto, es una capa donde tenemos muchas herramientas brindando servicios.

### **En clase se presentó RPC.

¿Cómo se relacionan con los siguientes conceptos?**

- **HTTP**
    - ==**HTTP puede actuar como el protocolo de transporte subyacente para RPC**, permitiendo que las llamadas a procedimientos se realicen a través de la red a través de una capa de conexión HTTP. Es importante entender que HTTP pertenece a la capa de aplicación, la implementación completa de RPC, junto al uso de HTTP, pertenecen a la capa de middleware.== Ver esto porque en clase dijeron que RPC esta por encima de HTTP
- **REST**
    - Al igual que RPC, viene a solucionar el problema de la comunicación en aplicaciones distribuidas. Sin embargo, la forma de lograrlo es distinta. REST se encuentra en capa de aplicación, desfavorenciendo a la transparencia ya que se tiene que saber los endpoints, sistema de mensajes y metodo que se utiliza (POST, PUT, etc). Por otro lado, RPC se encuentra en capa de middleware, logra encapsular la información favoreciendo a la transparencia. La comunicación se logra accediendo a los métodos como si pertenecieran a un mismo ordenador.
- **gRPC**
    - gRPC es una implementación moderna del mecanismo RPC desarrollada por Google que mejora la eficiencia y el rendimiento en la comunicación entre sistemas distribuidos. Utiliza HTTP/2 como protocolo de transporte, lo que permite multiplexación y streaming bidireccional, y Protocol Buffers para la serialización de datos, reduciendo el tamaño de los mensajes y el consumo de recursos.
- **GraphQL**
    - GraphQL, al igual que REST y RPC, viene a solucionar el problema de comunicación entre aplicaciones distribuidas. La diferencia está en que GraphQL lo logra permitiendo al cliente acceder a datos mediante un único endpoint y especificar exactamente qué información necesita según el esquema definido por el servidor. Por otro lado, el mecanismo RPC, se encuentra en la capa de middleware. Este busca encapsular la información aparentando un llamado a función dentro del mismo ordenador. Podemos ver que el beneficio central se encuentra en la transparencia que brinda RPC vs GraphQL. Mientras RPC oculta los detalles de comunicación y almacenamiento, GraphQL requiere que el cliente conozca el esquema y las relaciones de los datos, lo que implica cierto conocimiento previo del servicio.

### **¿Qué es gRPC? ¿Por qué se desarrolló? ¿Por qué no se usa en aplicaciones web? ¿Por qué si se usa en mobile?¿Qué nos ofrece por sobre la definición de RPC vista en clase?**

**gRPC es una implementación de llamadas a procedimiento remoto** [**(RPC)**](https://datatracker.ietf.org/doc/html/rfc1831) diseñado originalmente por Google.

![image.png](attachment:4b70009a-eef5-46d5-a18f-83922b7d3f1a:image.png)

Y ¿qué diferencia gRPC de RCP? Pues introduce **dos mejoras** sustanciales que son:

**El uso de HTTP/2 para el envío de datos a través de la capa de [transporte.La](http://transporte.La) utilización de Protocol buffers para la gestión de la estructura y distribución de los datos.**

- El uso de **HTTP/2** para el envío de datos a través de la capa de transporte.
    
    - Multiplexación
    - Compresión de cabeceras
    - Server push
    - Priorización de flujos
    - Formato binario en lugar de texto
- La utilización de **Protocol buffers** para la gestión de la estructura y distribución de los datos.
    
    - Serialización de las estructuras de datos
    - Describir la interfaz de comunicación

Google creó gRPC porque su infraestructura [Stubby](https://www.google.com/search?sca_esv=0d71c7b82b192171&rlz=1C5CHFA_enAR1173AR1173&cs=0&q=Stubby&sa=X&ved=2ahUKEwi49prnpLWPAxWZO7kGHbzhHtUQxccNegQIAxAB&mstk=AUtExfCxrcfTMD4ED6uaNYZ8VWkDbfaxwBMWH5yjga40gqJ9XjLdaYXV_DeSKz1g801kYepaKsRcQAm22axPYPo3EtGflsXMwDDLT1mOLDz9S-szCKbGK4JOnN6bcKPkqb4AXz9yWvLG18CROuTehV3HEHJ9gjW4DhtfsxcUg-g8hrUF93gYKvGl-B2tVZpfNpUnSy6A&csui=3), que usaba internamente para conectar sus microservicios, era una solución de alto rendimiento, pero no podía usarse fácilmente con navegadores y necesitaba de un formato de datos más eficiente que JSON. Las limitaciones con los navegadores, la falta de un estándar para las actualizaciones parciales y de concurrencia, y la necesidad de un formato de serialización binario más eficiente que XML o JSON impulsaron la creación de gRPC como una solución abierta y de alto rendimiento, según indica [**gRPC**](https://grpc.io/about/).

**Mobile**

- En mobile (iOS, Android) las aplicaciones pueden implementar gRPC **completo**, con streaming bidireccional y todas las ventajas de HTTP/2 y Protocol Buffers.
- Esto hace que gRPC sea muy eficiente en **consumo de datos y velocidad**, ideal para apps móviles con conexiones variables o limitadas.

**Web**

- gRPC “clásico” utiliza **HTTP/2 con frames binarios y multiplexación avanzada**, lo que no siempre es compatible con las restricciones de los navegadores web.
- Los navegadores **no permiten abrir conexiones TCP arbitrarias** ni enviar mensajes binarios en HTTP/2 de la misma manera que un cliente nativo.
- Por ello, gRPC tradicional funciona **bien en aplicaciones móviles o backend a backend**, donde se tiene control total del cliente.

### Explicar qué es Protocol Buffers y por qué se usan junto con gRPC. Comparar con JSON y XML en términos de tamaño, eficiencia y flexibilidad.

Protocol Buffers es un **formato de serialización de datos binario** desarrollado por Google. Permite definir estructuras de datos (mensajes) mediante un **schema fuertemente tipado**, que luego se compilan en clases o structs para distintos lenguajes de programación.

**Uso con gRPC:**

- gRPC utiliza Protobuf para **serializar los mensajes** que se envían entre cliente y servidor.
- Esto asegura que los datos sean **compactos, rápidos de procesar y fuertemente tipados**, lo que permite la verificación de tipos en tiempo de compilación y reduce errores.
- Facilita además la **generación automática de stubs** para cliente y servidor, haciendo transparente la invocación remota de métodos.

| Característica            | Protocol Buffers (Protobuf)                   | JSON                        | XML                            |
| ------------------------- | --------------------------------------------- | --------------------------- | ------------------------------ |
| **Formato**               | Binario                                       | Texto                       | Texto                          |
| **Tamaño del mensaje**    | Muy pequeño                                   | Mayor que Protobuf          | Muy grande                     |
| **Eficiencia**            | Alta (rápida serialización y deserialización) | Moderada                    | Baja (mucho parsing y verbose) |
| **Flexibilidad**          | Estricta (schema obligatorio)                 | Alta (no requiere schema)   | Alta (no requiere schema)      |
| **Verificación de tipos** | En tiempo de compilación                      | Solo en tiempo de ejecución | Solo en tiempo de ejecución    |


### ¿Cuál es la definición de mensaje en el contexto de la comunicación basada en mensajes?


>Una **unidad autónoma de información** que se intercambia entre procesos, aplicaciones o sistemas distribuidos para coordinar acciones, compartir datos o solicitar servicios.


 **Características principales de un mensaje**
 
1. **Autocontenido**: incluye tanto los datos que se transmiten (payload) como metadatos necesarios para su interpretación (encabezados, identificadores, etc.).
    
2. **Independiente**: puede viajar de manera aislada, sin requerir el estado previo de la comunicación (a diferencia de una llamada directa en RPC).
    
3. **Asincronía posible**: permite que el emisor y el receptor no estén activos al mismo tiempo (ejemplo: colas de mensajes como RabbitMQ o Kafka).
    
4. **Formato definido**: puede representarse en JSON, XML, Protobuf, Avro, entre otros.


### ¿En qué escenarios se usa MPI? Listar ejemplos reales y citar fuentes.

Antes... que era MPI?

> Es un estándar de software para la comunicación entre procesos en sistemas de computación paralela, como clusters de ordenadores, que se basa en el modelo de "paso de mensajes". Define un conjunto de funciones que los programadores usan para crear aplicaciones paralelas portátiles y escalables, permitiendo a procesos con sus propias memorias locales intercambiar datos explícitamente a través del envío de mensajes.

Para recordar, se veía de esta forma, tenía un millón de funciones:

![[Pasted image 20250904091717.png]]

Por lo que recuerdo de la clase, MPI era el que se usaba en supercomputadoras, son redes cerradas y muy bien monitoreadas. Como dijimos que era para supercomputadoras, vemos que los casos de uso son aquellos que requieren capacidad de cómputo enorme:

- **Simulación numérica y modelado** (por ejemplo, simulaciones de fluidos, estructuras, dinámica molecular, meteorología, astrofísica) 
    
- **Investigación científica** en física, química, biotecnología, química computacional 
    
- **Modelos económicos**, simulación de crecimiento y finanzas computacionales para valoración de derivados o análisis de riesgo.


### Explicar por qué MPI no se usa de forma generalizada en aplicaciones web.

Para empezar, como fue mencionado anteriormente, MPI fue diseñado para **aplicaciones de computación paralela en clusters o supercomputadoras**, donde los procesos cooperan estrechamente y tienen **bajo nivel de comunicación directa y controlada**.

Es por esto que en web tiene problemas en:

1. Transporte
   En la web, el transporte está estandarizado sobre **HTTP/HTTPS**, que no encaja con la semántica de MPI.
   Rompe la compatibilidad con la infraestructura de Internet, que está optimizada para **request/response** y no para comunicación paralela masiva. 
2. Persistencia de conexiones
   **MPI** requiere mantener conexiones estables entre procesos durante toda la ejecución de un programa paralelo.
   La **elasticidad** de las aplicaciones web (donde los nodos aparecen o desaparecen dinámicamente) es contraria al modelo rígido de MPI, que espera un número fijo de procesos al inicio.   
3. Seguridad
   **MPI** fue diseñado para ambientes de confianza (clusters privados). Su modelo de comunicación no incluye de forma nativa cifrado de extremo a extremo ni autenticación robusta.
4. Compatibilidad

### ¿Cómo funcionan los sockets que usa ZeroMQ?¿En qué capa del modelo OSI se encuentran? ¿Qué protocolo usa ZeroMQ?

Como visto en clase, los sockets de ZeroMQ proveen mayor transparencia, no es necesario saber la configuración de ip, puerto, etc. ZeroMQ está orientado a mensajes, y su comportamiento se define por el tipo de socket y el patrón de mensajería elegido:
1. Req / Res
2. Pub / Sub
3. Push / Pull

==preguntar en que capa se encuentran==, por lo que vi no se encuentran en una única capa.

Utiliza ZMTP: este es un protocolo hecho custom sobre protocolos como TCP y PGM para la conectividad por red.

Como funciona?

- **Capa base:** 
    
    ZMTP opera sobre una capa de transporte ya existente, como TCP para conexiones de red o PGM para multidifusión. 
    
- **Reglas de comunicación:** 
    
    ZMTP define cómo se formatean los mensajes y comandos, cómo se establecen y cierran las conexiones, y cómo se gestionan los metadatos y el control. 
    
- **Seguridad:** 
    
    ZMTP tiene mecanismos de seguridad extensibles para la autenticación y el cifrado, acordados por los pares durante el establecimiento de la conexión.

### Explicar las ventajas de usar ZeroMQ, haciendo énfasis en la transparencia

Rendimiento y Latencia

- **Alto Rendimiento y Baja Latencia**: 
    
    ZeroMQ está diseñado para mensajería de alto rendimiento, lo que lo hace adecuado para aplicaciones en tiempo real que necesitan entrega rápida de mensajes. 
    

Flexibilidad y Facilidad de Uso

- **Patrones de Mensajería Inteligentes**: 
    
    Ofrece varios patrones de mensajería, como publicar/suscribir (pub-sub), solicitar/responder (request-reply) y empujar/tirar (push-pull), lo que permite implementar diversas arquitecturas. 
    
- **Agnosticismo de Lenguaje y Plataforma**: 
    
    Proporciona enlaces para múltiples lenguajes de programación (C++, Java, Python, etc.) y funciona en diversas plataformas, facilitando la integración en sistemas heterogéneos. 
    
- **Transporte Agnóstico**: 
    
    Soporta múltiples protocolos de transporte, incluyendo TCP, IPC (comunicación entre procesos), PGM (multidifusión fiable) y memoria compartida (inproc).

En este último punto de "flexibilidad y facilidad de uso" podemos ver claramente la transparencia. Es independiente de las capas inferiores, del lenguaje, de la plataforma, esto hace que el cliente no se tenga que enterar de los cambios en otras capas.

### Lista 3 message brokers y sus características principales. ¿Es Apache Kafka un message broker?

**Que es un broker?**
Un método de comunicación utilizado por el middleware de mensajería es un modelo basado en servidor que utiliza un intermediario de mensajes. Con un intermediario de mensajes, la aplicación de origen (productor) envía un mensaje a un proceso servidor que puede proporcionar la ordenación de datos, el enrutamiento, la traducción de mensajes, la persistencia y la entrega a todos los destinos apropiados (consumidores). La característica que define a un intermediario de mensajes es que es un servicio discreto. Los productores y consumidores se comunican con el intermediario mediante protocolos estándar o propietarios. El intermediario de mensajes generalmente proporciona toda la gestión del estado y el seguimiento de los clientes, de modo que las aplicaciones individuales no necesitan asumir esta responsabilidad, y la complejidad de la entrega de mensajes está integrada en el propio intermediario de mensajes.

Hay dos formas básicas de comunicación con un intermediario de mensajes:

- Publicar y suscribirse (Temas)
- Punto a punto (colas)



![[Pasted image 20250904143631.png]]

**Broker**: tengo dos nodos que se comunican, hay un intermediario que es el broker. Tener un broker es una decisión de diseño de mi sistema distribuido. Por ejemplo, si mis aplicaciones hablan todos en distintos lenguajes, voy a necesitar un broker para que mi servidor reciba la infromación de una forma en particular. Los plugins son para ir agregando, por ejemplo, nuevos lenguajes sin tener que rehacer el broker de nuevo. Las multiples queues, son porque el broker tambien maneja prioridades. Las reglas del broker es por como tienen que interactuar los clientes para poder comunicarse con el broker.

Ejemplos:
- Redis
- IBM MQ
- RabbitMQ

**Con respecto a si kafka es o no, encontre esto en Stack Overflow:**

Kafka puede reemplazar las Colas de Mensajes (MQ), como RabbitMQ o ActiveMQ, en algunas implementaciones, pero definitivamente no es MQ. Por lo tanto, esta opción está descartada.

> Una **cola de mensajes** consta de dos componentes: un **intermediario de mensajes** y un cliente. El intermediario de mensajes se encarga de recibir y almacenar mensajes en una cola, y el cliente se encarga de enviar y recibir mensajes del intermediario.

de un artículo de Medium [aquí](https://medium.com/@fullstacktips/mq-vs-kafka-which-messaging-system-is-right-for-your-distributed-system-692debae1033)

Por lo tanto, llamar a Kafka un intermediario de mensajes también sería confuso y probablemente inexacto.

Lo que podría estar causando esta confusión es que el tipo de servidor principal en un clúster de Kafka se denomina « **Broker»** , pero, como se mencionó, esto no define a Kafka y no debe usarse como sinónimo de su nombre. La mayoría de las fuentes que he consultado se refieren a Kafka como una plataforma de transmisión de eventos distribuidos.

En una nota personal, para mí Kafka es una categoría propia, por lo que debería llamarse únicamente Kafka. Espero haber respondido a tu pregunta.

Afinando un poco con ChatGPT, entendí lo siguiente de Kafka:

> No tiene una cola implementada, se maneja 100% a base de logs inmutables. Estos logs, luego son almacenados en clústers que poseen responsabilidades. Se busca tener réplicas en los clusters de los logs y hay un líder por cluster que ordena.


### Listar tres herramientas que sirven para desarrollar o montar Enterprise Application Integration basadas en mensajes

#### Que es?

Sistema que conecta aplicaciones empresariales dispares dentro de una organización, como las de CRM o ERP, para facilitar el intercambio automatizado de datos y optimizar los procesos de negocio.

#### Ejemplos

- IBM WebSphere MQSeries (o IBM MQ)


### Dar un ejemplos concretos de herramientas que nos habiliten cada combinación de tipo de comunicación

#### Sincrónica y transitoria

#### Sincrónica y persistente

#### Asincrónica y transitoria

#### Asincrónica y persistente
