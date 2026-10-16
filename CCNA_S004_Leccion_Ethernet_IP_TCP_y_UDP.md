# CCNA S004 — Lección: Ethernet, IP, TCP y UDP

**Tema:** qué problema resuelve cada protocolo.  
**Preparación:** 16 de octubre de 2026.  
**Tiempo sugerido:** 35 minutos de lección y 40 de práctica.  
**Programa:** CCNA 200-301 v2.0, semana 1.  
**Práctica asociada:** `CCNA_S004_Practica_Comparar_TCP_y_UDP.md`.  
**Escenario:** Campus Aurora, organización ficticia.

## 1. Objetivo del programa y alcance de hoy

Al **16/10/2026**, el examen disponible es **200-301 CCNA v1.1**, cuyo último día anunciado es el **02/02/2027**. Cisco ya publicó el temario de **v2.0** y anunció su examen para el **03/02/2027**. Nuestro objetivo sigue siendo **v2.0**. Las fechas y los objetivos pertinentes se verificaron en las fuentes oficiales; no requieren cambiar el plan [C1, C2].

La sesión desarrolla fundamentos de apoyo a estos IDs del temario v2.0:

| ID oficial | Relación con esta sesión |
| --- | --- |
| 1.6 | Preparar el diagnóstico de conectividad de clientes: distinguir direccionamiento, alcance IP y acceso a una aplicación. |
| 2.4 | Preparar la interpretación de tráfico y de pruebas de conectividad de capas 2 y 3. |

Esta sesión **no acredita dominio completo** de esos objetivos. La comparación elemental Ethernet/IP/TCP/UDP es un fundamento del programa, sin atribuirle un sub-ID oficial inexistente. Usaremos DNS únicamente como ejemplo de tráfico UDP; su estudio detallado corresponde a la semana 15. El aprendizaje de MAC en switches y el cálculo de subredes también tienen sesiones posteriores.

## 2. Objetivos observables y prerrequisitos

Al terminar deberías poder:

1. **O1:** explicar una función de Ethernet, IP, TCP y UDP, sin intercambiarlas.
2. **O2:** distinguir MAC, dirección IP y puerto de transporte en un ejemplo.
3. **O3:** interpretar el establecimiento básico de TCP y un ACK expresado en bytes.
4. **O4:** comparar TCP y UDP sin equiparar fiabilidad con cifrado ni ausencia de retransmisión con pérdida obligatoria.
5. **O5:** identificar TCP o UDP dentro de un paquete observado en Packet Tracer, indicando qué evidencia respalda la identificación.
6. **O6:** plantear y verificar una falla de servicio cambiando una sola variable en laboratorio.

**Prerrequisitos:** las ideas de red, cliente y servidor de S001; bits, bytes y latencia de S002; capas y encapsulación de S003. Haber recibido esas sesiones no significa haberlas estudiado. Si alguna idea falta, vuelve al apartado correspondiente antes del laboratorio.

**Recuperación sin consultar apuntes:**

- R1. ¿En qué capas OSI ubicarías IP y TCP?
- R2. ¿Un `ping` exitoso demuestra que una página HTTP funciona? Explica.
- R3. ¿Cuántos megabits por segundo equivalen a 10 megabytes por segundo, usando unidades decimales?

Las respuestas de recuperación y comprensión están al final de la práctica, para revisarlas después del intento.

## 3. Cuatro protocolos, cuatro responsabilidades

Ana abre un portal interno de Campus Aurora. Para esta sesión, el portal utiliza HTTP sobre TCP, IPv4 y Ethernet. No hay túneles, NAT ni cifrado en el escenario.

| Protocolo | Pregunta que ayuda a resolver | Identificador que miraremos | Unidad habitual |
| --- | --- | --- | --- |
| Ethernet, en su función de enlace | ¿Cómo llevo esta trama al destinatario de esta LAN? | MAC origen y destino | Frame: trama |
| IP | ¿Cuáles son los extremos IP y cómo se encamina el paquete entre redes? | IP origen y destino | Packet: paquete |
| TCP | ¿Cómo entrego a la aplicación un flujo de bytes fiable y ordenado? | Puertos, secuencias y ACK | Segment: segmento |
| UDP | ¿Cómo entrego datagramas a una aplicación con un mecanismo de transporte sencillo? | Puertos origen y destino | Datagram: datagrama |

Ethernet también define aspectos físicos; aquí estudiamos principalmente sus tramas de capa 2. TCP y UDP pertenecen a transporte, capa 4; IP corresponde a red, capa 3. Para este tráfico, el paquete IP contiene **TCP o UDP**, según la aplicación, y la trama Ethernet contiene el paquete IP.

“Paquete” suele usarse informalmente para todo el tráfico. Al explicar una captura, usa el nombre preciso de la unidad cuando sea relevante.

## 4. Ethernet: entrega dentro del enlace local

Una **MAC address** identifica una interfaz en el contexto del enlace. Ejemplo de formato: `02:00:00:00:00:20`. Es un valor ficticio, no la dirección que debas asignar a tu PC.

En la LAN del ejercicio, PC-A envía una trama cuyo destino es la MAC de WEB-A. SW1 la conmuta hacia el puerto adecuado. La trama transporta un paquete IPv4; el campo **EtherType** permite identificar qué protocolo lleva. Para IPv4 sobre Ethernet, ese valor es `0x0800` [T1]. No necesitas memorizar hoy todos los campos ni sus tamaños.

La verificación de errores de la trama no proporciona por sí sola entrega fiable de extremo a extremo. Tampoco cifra los datos. Un cable conectado y una trama válida no demuestran que un servicio web esté disponible.

Cuando el destino IP está en otra red, el destinatario Ethernet del primer tramo suele ser la interfaz del router que actúa como siguiente salto. El router retira la envoltura de enlace recibida y construye la apropiada para el enlace de salida. Por eso no debes suponer que la MAC del servidor remoto acompaña al paquete durante todo el recorrido.

En el laboratorio de hoy los extremos están en la misma LAN; no añadiremos un router. Si aparece **ARP**, se está resolviendo la correspondencia entre IPv4 y MAC del vecino local. ARP no es TCP ni UDP.

## 5. IP: direccionamiento y encaminamiento

Una dirección IP permite identificar interfaces y ubicar destinos dentro de un esquema de redes. Usaremos estos valores dados:

| Equipo | Dirección IPv4 | Papel |
| --- | --- | --- |
| PC-A | 192.168.10.20 | Cliente |
| WEB-A | 192.168.10.30 | Servidor del laboratorio |

IP ofrece un servicio de **best effort**, o mejor esfuerzo: intenta entregar paquetes, pero por sí solo no confirma su recepción, no retransmite lo perdido ni garantiza el orden [T2]. “Mejor esfuerzo” no significa que los paquetes se pierdan siempre.

Si añadimos routers en un ejemplo conceptual sin NAT, las direcciones IP de los extremos normalmente permanecen iguales, mientras cambian las envolturas de enlace. Eso **no** significa que todo el encabezado IP sea inmutable: por ejemplo, el TTL de IPv4 disminuye al atravesar un router. Volveremos a esos detalles al estudiar enrutamiento.

IP no elige por sí solo entre el navegador y otra aplicación que esté funcionando en la misma PC. Para diferenciar conversaciones de aplicaciones, TCP y UDP añaden puertos.

## 6. Puertos: distinguir aplicaciones y conversaciones

Un **port number** es un número de transporte; no es un conector físico del switch. En una conversación cliente-servidor, el servicio suele escuchar en un puerto conocido y el cliente utiliza un puerto temporal, o **ephemeral port**.

Estos valores de cliente son **didácticos** y pueden ser distintos en tu simulador:

| Mensaje | Transporte | IP origen | Puerto origen | IP destino | Puerto destino |
| --- | --- | --- | ---: | --- | ---: |
| Solicitud web | TCP | 192.168.10.20 | 51000 | 192.168.10.30 | 80 |
| Respuesta web | TCP | 192.168.10.30 | 80 | 192.168.10.20 | 51000 |
| Consulta DNS | UDP | 192.168.10.20 | 53000 | 192.168.10.30 | 53 |
| Respuesta DNS | UDP | 192.168.10.30 | 53 | 192.168.10.20 | 53000 |

Para describir cada flujo, combina **protocolo de transporte, IP origen, puerto origen, IP destino y puerto destino**. El puerto 53/TCP y el 53/UDP son identificadores de transportes diferentes, aunque compartan número.

En una captura debes comprobar el protocolo y los campos: el número 80 por sí solo no demuestra qué aplicación transporta un flujo. En nuestro laboratorio conocemos además la configuración del servidor HTTP.

## 7. TCP: establecer una conexión y seguir los bytes

**Transmission Control Protocol** ofrece a las aplicaciones un flujo de bytes fiable y ordenado. Utiliza números de secuencia, confirmaciones y retransmisiones. No conserva obligatoriamente la separación entre los mensajes que escribió la aplicación: esta debe reconocer sus propios límites [T3].

### 7.1 Establecimiento básico: three-way handshake

Supón una conexión nueva, sin datos de aplicación en los tres mensajes iniciales. Los números son pequeños para facilitar el cálculo; no son valores capturados.

| Paso | Sentido | Flags | Seq | Ack | Interpretación |
| --- | --- | --- | ---: | ---: | --- |
| 1 | PC-A a WEB-A | SYN | 100 | No significativo | El cliente propone iniciar la conexión. |
| 2 | WEB-A a PC-A | SYN, ACK | 400 | 101 | El servidor inicia su lado y confirma el SYN del cliente. |
| 3 | PC-A a WEB-A | ACK | 101 | 401 | El cliente confirma el SYN del servidor. |

**SYN** consume una posición de secuencia; por eso la confirmación de 100 es 101. El ACK de la tercera fila no lleva datos y, por sí solo, no consume otra posición. Cada dirección tiene su propia numeración.

No es necesario escribir estos mensajes manualmente: la pila TCP los genera. Tampoco debes exigir tres eventos nuevos al recargar una página si el navegador reutiliza una conexión existente.

### 7.2 Secuencia y confirmación de datos

Después del establecimiento, PC-A envía **200 bytes**, comenzando en `Seq=101`. Esos bytes ocupan las posiciones **101 a 300**. Si WEB-A recibe todo ese tramo en orden, responde con **Ack=301**: espera el byte 301 como siguiente byte.

El ACK no significa “recibí 301 paquetes” ni demuestra que una persona leyó la página. Confirma recepción en TCP; no acredita que la aplicación completó una operación de negocio.

Si se pierde información, TCP puede retransmitirla. Una interrupción persistente puede hacer que la conexión falle: “fiable” describe los mecanismos y el servicio, no una promesa de éxito ante cualquier avería [T3, T4].

### 7.3 Dos controles distintos

| Concepto | Qué intenta evitar |
| --- | --- |
| Flow control: control de flujo | Que el emisor sobrepase la capacidad de recepción del otro extremo. |
| Congestion control: control de congestión | Que el envío contribuya a saturar el camino de red. |

Hoy basta distinguir sus propósitos. TCP no cifra automáticamente el contenido: fiabilidad y confidencialidad son propiedades diferentes.

## 8. UDP: enviar datagramas sin el mecanismo de TCP

**User Datagram Protocol** mantiene mensajes como datagramas y añade puertos. UDP no establece el handshake de TCP ni incorpora sus ACK, ordenamiento o retransmisiones [T5].

Esto permite diseñar aplicaciones con intercambios sencillos o con mecanismos propios. Si una aplicación necesita reintentos, orden o control de congestión sobre UDP, debe obtenerlos de su propio diseño o de otro protocolo; UDP no los aporta automáticamente [T6].

Evita estas dos conclusiones:

- “UDP siempre pierde datos”: una consulta y su respuesta pueden llegar correctamente.
- “UDP siempre es más rápido”: el tiempo observado depende del camino, la aplicación, la carga y sus mecanismos. La ausencia del handshake de TCP no garantiza mejor rendimiento en cualquier caso.

UDP incluye un campo de checksum; ausencia de retransmisiones no equivale a ausencia de todo mecanismo de detección de errores. Ni TCP ni UDP, por sí solos, ofrecen cifrado de aplicación.

## 9. Comparación aplicada a Campus Aurora

| Propiedad | TCP | UDP |
| --- | --- | --- |
| Servicio a la aplicación | Flujo de bytes ordenado | Datagramas con límites de mensaje |
| Establecimiento básico | Conexión mediante handshake | Sin handshake propio equivalente |
| Recuperación de pérdida | Mecanismos de retransmisión | Sin retransmisión propia |
| Orden de entrega | Ordenado en el flujo entregado | Sin garantía de orden |
| Identificación de extremos | IP y puertos TCP | IP y puertos UDP |
| Ejemplo que observarás | HTTP del servidor simulado, TCP/80 | Consulta DNS ordinaria, UDP/53 |

El uso de DNS sobre UDP en este ejercicio no convierte “DNS = siempre UDP” en una regla: DNS también admite TCP [T7]. Del mismo modo, limita la afirmación “HTTP usa TCP” al servicio observado aquí; no extrapoles todos los detalles del simulador a cualquier implementación web.

La aplicación y los protocolos que implementa determinan el transporte. No puedes convertir un servicio TCP en UDP cambiando una casilla del switch y esperar que siga funcionando igual.

## 10. Seguir dos solicitudes paso a paso

### Caso A: consultar un nombre

1. PC-A pregunta al servidor DNS por `portal.aurora.test`.
2. En este ejercicio la consulta viaja como datos DNS dentro de UDP, dentro de IPv4 y dentro de Ethernet.
3. WEB-A devuelve la dirección `192.168.10.30`; en la respuesta se invierten las IP y los puertos de la conversación.
4. Una respuesta DNS demuestra la resolución consultada, no que el servidor web funcione.

### Caso B: abrir la página por IP

1. Ana introduce `http://192.168.10.30` en el navegador de Packet Tracer.
2. Al usar esta IP literal, el ejercicio no necesita resolver un nombre.
3. Para una conexión TCP nueva, se establece la conexión hacia el puerto 80.
4. HTTP intercambia la solicitud y la respuesta usando el flujo TCP.
5. El contenido aparece en el navegador si el servicio y la comunicación funcionan.

En ambos casos, IP y Ethernet siguen siendo necesarios. Si detienes solo DNS y la página por IP continúa funcionando, has separado dos servicios que comparten equipo y red.

## 11. Comandos y evidencia: lo que sí permiten concluir

En **PC-A → Desktop → Command Prompt** de Packet Tracer:

```text
ping 192.168.10.30
nslookup portal.aurora.test
```

Ejecuta cada comando por separado. Sus funciones son distintas:

| Acción | Evidencia que buscamos | Conclusión que no basta para sostener |
| --- | --- | --- |
| `ping` | Respuestas ICMP desde el destino | Que TCP/80 o DNS estén operativos |
| `nslookup` | Respuesta DNS y dirección correspondiente al nombre | Que HTTP entregue la página |
| Navegador por IP | Respuesta de HTTP en esta aplicación | Que la resolución DNS funcione |
| Detalles de PDU | Campos IP, TCP o UDP presentes | Rendimiento idéntico al de equipos físicos |

Estos son comandos e instrucciones para que los ejecutes tú en laboratorio. **No se ejecutó el laboratorio al preparar el material.** Los ejemplos numéricos de esta lección son didácticos.

## 12. Errores comunes y corrección

| Error | Corrección |
| --- | --- |
| Confundir puerto TCP con puerto de switch | Uno es un identificador de transporte; el otro, una interfaz física o lógica del equipo. |
| Atribuir retransmisiones a IP | Distingue el servicio de IP de los mecanismos de TCP o de la aplicación. |
| Suponer que TCP cifra | TCP aporta transporte fiable; el cifrado requiere mecanismos adicionales. |
| Interpretar ACK como cantidad de paquetes | En el ejemplo, indica el siguiente byte esperado del flujo. |
| Concluir “UDP averiado” porque DNS no responde | Revisa también servicio, configuración y evidencia del intercambio. |
| Ver verde el enlace y declarar todo operativo | Verifica por separado alcance IP y servicio. |
| Copiar puertos temporales del ejemplo como obligatorios | Registra los valores que muestre tu simulación. |

## 13. Vocabulario de examen

| Inglés | Español y sentido en esta sesión |
| --- | --- |
| Frame / packet / segment / datagram | Trama / paquete / segmento / datagrama: unidades según el protocolo. |
| Source / destination | Origen / destino. |
| Best effort | Servicio sin garantía propia de entrega. |
| Connection-oriented / connectionless | Orientado a conexión / sin conexión propia de transporte. |
| Sequence number | Número de secuencia; posición en el flujo TCP. |
| Acknowledgment, ACK | Confirmación; en nuestros ejemplos, siguiente byte esperado. |
| Retransmission | Reenvío de información para recuperar una pérdida. |
| Ephemeral port | Puerto temporal del cliente. |
| Payload | Contenido transportado dentro de una unidad. |
| Troubleshooting | Diagnóstico mediante hipótesis y pruebas. |

## 14. Preguntas de comprensión

Responde antes de consultar las soluciones de la práctica. Son preguntas originales de entrenamiento.

1. **Q1:** ¿Qué protocolos miras para identificar la MAC de destino, la IP de destino y el puerto de destino de una solicitud web TCP?
2. **Q2:** En una conexión TCP cliente `192.168.10.20:51000` a servidor `192.168.10.30:80`, escribe origen y destino de la respuesta.
3. **Q3:** Un cliente envía SYN con `Seq=700`; el servidor responde con SYN propio `Seq=900`. Sin datos adicionales, ¿cuáles son los ACK del segundo y del tercer mensaje?
4. **Q4:** Se reciben en orden 120 bytes desde `Seq=501`, sin SYN ni FIN. ¿Cuál es el siguiente ACK y por qué?
5. **Q5:** Una consulta UDP llega y recibe respuesta. ¿Contradice que UDP no garantiza entrega? Razona.
6. **Q6:** ¿Qué dos errores contiene “el ping funcionó, así que TCP está establecido y el portal funciona”?
7. **Q7:** DNS deja de responder, pero HTTP por IP sigue funcionando. ¿Qué hipótesis probarías primero y qué afirmación amplia evitarías?
8. **Q8:** ¿Una conexión TCP fiable implica que sus datos estén cifrados? Explica la diferencia.

## 15. Repaso y continuidad

Sin mirar las tablas, explica en un minuto qué aportan **Ethernet, IP y el transporte elegido** a una consulta DNS y a una visita HTTP del laboratorio.

- **Al día siguiente de estudiar S004:** responde Q2, Q4 y Q6 de memoria.
- **Siete días después de estudiarla:** reconstruye la tabla TCP/UDP y distingue una falla de servicio de una falla de conectividad.
- **Aproximadamente un mes después:** repite el razonamiento con direcciones y puertos de cliente diferentes.

Estas fechas de repaso dependen de cuándo estudies, no de cuándo se entregó el archivo. La siguiente sesión del calendario es S005, primer contacto con IOS. Si falta una base, registraremos el refuerzo necesario antes de introducir nuevo contenido.

## 16. Fuentes oficiales y técnicas primarias

Consultadas el **16/10/2026**. No hace falta leer los RFC completos hoy; permiten comprobar las definiciones. Los ejemplos y las preguntas son propios de este programa.

- **C1. Cisco:** [temario oficial 200-301 CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf), relación curricular 1.6 y 2.4.
- **C2. Cisco:** [Stay on Track: Get Certified Before the CCNA Refresh](https://blogs.cisco.com/learning/stay-on-track-get-certified-before-the-ccna-refresh), publicado el 01/07/2026; fechas de transición.
- **T1. RFC Editor:** [RFC 894, IP sobre Ethernet](https://www.rfc-editor.org/rfc/rfc894.html), formato de trama y encapsulación de IPv4. Es una especificación histórica, no una descripción de todas las velocidades Ethernet actuales.
- **T2. RFC Editor:** [RFC 791, Internet Protocol](https://www.rfc-editor.org/rfc/rfc791.html), secciones 1.4 y 2.2: servicio IP y funciones básicas.
- **T3. RFC Editor:** [RFC 9293, TCP](https://www.rfc-editor.org/rfc/rfc9293.html), secciones 2.2, 3.1 y 3.4: servicio, campos y números de secuencia.
- **T4. Cisco:** [Troubleshooting TCP/IP](https://www.cisco.com/en/US/docs/internetworking/troubleshooting/guide/tr1907.html), explicación de puertos, secuencias y establecimiento de TCP; documento de referencia histórico.
- **T5. RFC Editor:** [RFC 768, UDP](https://www.rfc-editor.org/rfc/rfc768.html), servicio de datagramas y campos de cabecera.
- **T6. RFC Editor:** [RFC 8085, UDP Usage Guidelines](https://www.rfc-editor.org/rfc/rfc8085.html), secciones 3.1 y 3.3: congestión y responsabilidad de la aplicación sobre fiabilidad.
- **T7. RFC Editor:** [RFC 7766, DNS sobre TCP](https://www.rfc-editor.org/rfc/rfc7766.html), sección 5: DNS admite UDP y TCP.

---

**Estado:** material preparado; estudio, ejecución y dominio pendientes de evidencia del estudiante.

Fin de la lección S004.
