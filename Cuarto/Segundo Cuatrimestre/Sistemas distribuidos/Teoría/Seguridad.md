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

