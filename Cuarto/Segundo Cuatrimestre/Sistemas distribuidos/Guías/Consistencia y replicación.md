
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