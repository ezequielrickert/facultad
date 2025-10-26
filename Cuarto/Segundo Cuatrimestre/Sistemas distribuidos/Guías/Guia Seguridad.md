
**1 - ¿Qué protocolos o herramientas de seguridad se pueden utilizar para superar los siguientes desafíos?**

**a. Garantizar la integridad del mensaje.**

Hash o firma digital

El _hash_ (función _checksum_ o de resumen) asegura que el mensaje no ha sido alterado. La **Firma Digital** no solo asegura la integridad sino también la **autenticidad** (quién lo envió). Más específicamente, se usa un **Código de Autenticación de Mensaje (MAC)** o un _hash_ cifrado.

**b. Autenticación mutua.**

Challenge

El desafío (_challenge-response_) es el _mecanismo_ principal. El protocolo completo es **Challenge-Response Protocol** (como el uso de un _nonce_ o una _timestamp_). Esto evita ataques de repetición y prueba que ambas partes poseen la clave secreta.

**c. Suplantación de identidad.**

Hacer MFA, no solo quedarse con algo que conoce, ver si podemos agregar algo que tiene, es o hace.

**Autenticación Multifactor (MFA)** o **Autenticación Fuerte**. La clave es combinar dos o más factores: _Algo que sabe_ (contraseña), _Algo que tiene_ (token, celular) y/o _Algo que es_ (biometría).

**d. Ataques de replicación.**

Nonce o número de sesión

Se utiliza una **Marca de Tiempo (_Timestamp_)** o un **Número de Uso Único (Nonce)**. Estos valores garantizan que cada mensaje de sesión sea único, haciendo que la repetición de un mensaje antiguo (incluso si es válido) sea irreconocible como nuevo.

**e. Gestionar eficientemente las claves secretas compartidas en sistemas grandes.**

Key Distribution Center

El **Centro de Distribución de Claves (KDC)**, implementado en protocolos como **Kerberos**, permite que $N$ entidades se comuniquen de forma segura sin requerir $N(N-1)/2$ claves secretas.

**f. Autenticidad de las claves públicas.**

Tener una autoridad certificadora

Una **Autoridad de Certificación (CA)** emite **Certificados Digitales** (X.509) que enlazan una clave pública a una identidad verificada. Esto es la base de la infraestructura de clave pública (PKI).

**g. No revelar información sensible antes de la autenticación de las partes.**

Intercambio de claves públicas o canal de comunicación cifrada.

**Cifrado (_Encryption_)** o establecer un **Canal Seguro (Ej. TLS/SSL)**. Esto asegura que incluso si un atacante escucha la comunicación inicial (antes de la autenticación completa), los datos transportados (incluidas las credenciales o claves de sesión) no serán legibles.

**2 - ¿Por qué se usan claves de sesión? ¿Qué beneficios tiene?**

Luego de la autenticación se usan Sessions Key. 
- Se utiliza solo durante el tiempo de vida del canal de comunicación. Al término se destruye. 
- Permite preservar las claves de mayor vida como las que se usan para autenticar. Un atacante con suficiente información encriptada podría deducirlas.

**3 - Identifique la política de acceso que aplica a cada escenario:**   
a. DAC
b. RBAC
c. MAC
d. ABAC

**4 - ¿Cuál es la principal diferencia entre un firewall de filtrado de paquetes y un gateway a nivel de aplicación? De un ejemplo de ambos.**

- **Packet-filtering**: funciona como router y decide según el contenido de los headers del paquete. ej. firewall puesto en entrada de una red privada de empresa que bloquea, por ejemplo, mensajes UDP para bloquear DoS.

- **Application-level**: decide según el contenido del mensaje. ej. un filtrado de spam que se puede poner dentro del sistema de mail.

**5 - Busque ejemplos reales de mecanismos de Detection Intrusion Para signature-based Intrusion Detection Systems y Anomaly-based Intrusion Detection Systems**

Para recordar:

- **SIDS - Signature-based Intrusion Detection Systems** 
	- Funcionan comparando patrones de intrusiones ya conocidas a nivel de red. 
	  
  - **AIDS - Anomaly-based Intrusion Detection Systems** 
	- Se basa en la premisa que se puede modelar un comportamiento típico del sistema para luego detectar cualquier comportamiento anómalo. 
	- Se basa especialmente en herramientas de IA como Machine Learning. 

SIDS:
- Snort
- Suricata
- Cisco Secure IPS
- Fail2Ban

AIDS:
- Sagan
- Zeek
- SolarWinds Security Event Manager
