
### **Explicar por qué el modelo OSI es anticuado o poco representativo y por qué se opta por hablar de middleware**

OSI se planteo como un modelo, no como un protocolo. Esto quiere decir que era un planteamiento teórico, luego se establecieron varios protocolos basados en modelo OSI pero se volvía complejo.

Cuando hablamos de middleware, nos movemos un poco más al concepto de transparencia. Tenemos una capa que se encarga de realizar transformaciones y operaciones. Este no es un esquema tan estricto, es una capa donde tenemos muchas herramientas brindando servicios.

### **En clase se presentó RPC.

¿Cómo se relacionan con los siguientes conceptos?**

- **HTTP**
    - **HTTP puede actuar como el protocolo de transporte subyacente para RPC**, permitiendo que las llamadas a procedimientos se realicen a través de la red a través de una capa de conexión HTTP. Es importante entender que HTTP pertenece a la capa de aplicación, la implementación completa de RPC, junto al uso de HTTP, pertenecen a la capa de middleware.
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


