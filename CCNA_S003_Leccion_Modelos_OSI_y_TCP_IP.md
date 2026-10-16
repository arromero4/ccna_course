# CCNA S003 — Modelos OSI y TCP/IP: seguir un mensaje por capas

**Fecha:** 16 de octubre de 2026.  
**Nivel:** desde cero.  
**Tiempo:** 35 minutos de lección y repaso + 40 minutos de práctica.  
**Proyecto ficticio:** Campus Aurora.  
**Objetivo de certificación:** 200-301 CCNA v2.0.

**Versión y disponibilidad, verificadas el 16/10/2026:** Cisco mantiene el examen v1.1 disponible hasta el **02/02/2027** y anuncia v2.0 desde el **03/02/2027**. El temario v2.0 ya está publicado y sigue siendo el objetivo de este programa. [1][2]

## 1. Objetivos, prerrequisitos y alcance

Al terminar podrás demostrar estas capacidades:

| Objetivo de la sesión | Evidencia que buscarás |
| --- | --- |
| O1. Nombrar las siete capas OSI y explicar su función básica | Asociar una función con su capa, no solo recitar nombres |
| O2. Relacionar OSI con el modelo TCP/IP de cuatro capas | Completar una correspondencia razonada |
| O3. Describir encapsulación y desencapsulación | Seguir datos, segmento, paquete y trama en un ejemplo HTTP |
| O4. Distinguir direcciones y puertos de distintas capas | Identificar MAC, IPv4 y puerto TCP en el mismo mensaje |
| O5. Reconocer los límites de una prueba | Explicar por qué un ping correcto no valida HTTP |
| O6. Documentar un incidente por capas | Separar observación, hipótesis, cambio y verificación |

**Prerrequisitos recomendados:** S001, funciones de PC, switch y router; S002, unidades y límites de ping. La entrega de esos materiales no significa que ya los hayas estudiado. Si falta una base, anótala para solicitar refuerzo.

**IDs oficiales relacionados:** fundamentos de apoyo a **1.6**, diagnóstico de conectividad de clientes, y **2.4**, diagnóstico básico de conectividad de capas 2/3 e interpretación de herramientas y capturas. Esta sesión no cubre íntegramente esos objetivos. “Memorizar OSI” no se inventa como un ID adicional del temario. [1]

Hoy aprenderemos la organización de las funciones. S004 profundizará en qué problema resuelven Ethernet, IP, TCP y UDP; todavía no necesitamos memorizar sus campos ni configurar un router.

## 2. Recuerdo activo — Antes de leer

Intenta responder sin consultar S001/S002:

1. ¿Qué función permite comunicar redes IP diferentes?
2. Convierte **40 Mb/s** a **MB/s**.
3. Si recibes cuatro respuestas de ping, ¿ya mediste la capacidad máxima del enlace?

Las respuestas están al final de la práctica S003. Una duda es información útil para aprender, no una razón para marcar la sesión anterior como dominada.

## 3. Para qué sirve un modelo por capas

Imagina que Ana abre la página de avisos de Campus Aurora. Para que aparezca, hacen falta varias cosas: formular una solicitud, identificar el servicio, indicar el destino, transportar la información por la red y convertirla en señales.

Un **modelo por capas** separa esas responsabilidades. Permite preguntar “¿qué parte funciona y qué evidencia me falta?” en lugar de tratar toda la comunicación como una caja negra.

No confundas tres conceptos:

| Concepto | Qué representa | Ejemplo |
| --- | --- | --- |
| Modelo | Forma de organizar funciones | OSI o TCP/IP |
| Protocolo | Reglas concretas para intercambiar información | HTTP, TCP, IP |
| Dispositivo o programa | Implementación que participa en la comunicación | Switch, servidor, navegador |

Una capa **no es un equipo**, ni requiere un cable propio. Las capas describen funciones dentro y entre sistemas. Un mismo dispositivo puede implementar varias. [3]

OSI significa **Open Systems Interconnection**. Es un modelo de referencia de siete capas. TCP/IP también da nombre a una **familia de protocolos**, no solo a TCP y a IP. Utilizaremos su representación de cuatro capas. [3][4]

## 4. Las siete capas OSI

Las numeramos desde la capa física, **1**, hasta aplicación, **7**. Lee primero la función; el nombre será más fácil de recordar después.

| N.º | Español / inglés | Responsabilidad básica | Ejemplo para razonar |
| --- | --- | --- | --- |
| 7 | Aplicación / Application | Servicios de comunicación usados por aplicaciones | Una solicitud web HTTP |
| 6 | Presentación / Presentation | Representación de los datos; transformación de formatos | Acordar cómo interpretar caracteres; funciones de compresión o cifrado |
| 5 | Sesión / Session | Organización y control del diálogo entre aplicaciones | Mantener el contexto de un intercambio |
| 4 | Transporte / Transport | Comunicación entre procesos de los extremos | TCP o UDP; puertos para distinguir conversaciones |
| 3 | Red / Network | Direccionamiento lógico y entrega entre redes | IPv4 o IPv6; decisiones de enrutamiento |
| 2 | Enlace de datos / Data Link | Transferencia de tramas en el enlace o dominio de capa 2 | Ethernet y direcciones MAC |
| 1 | Física / Physical | Representación y transmisión de bits mediante señales | Señales eléctricas, ópticas o de radio |

Los nombres y el orden son los del modelo OSI. [5] Los ejemplos son una guía inicial, no una clasificación exclusiva de todo producto o protocolo.

### Tres precisiones importantes

**Aplicación no significa “todo el navegador”.** El navegador usa protocolos de aplicación y servicios de las capas inferiores. La capa 7 describe una función de comunicación, no cada botón de la interfaz.

**Sesión no significa únicamente iniciar sesión con usuario y contraseña.** En OSI hablamos de organizar un diálogo. Las aplicaciones reales pueden repartir esa función entre distintas bibliotecas y protocolos.

**Presentación no significa que todo cifrado sea siempre capa 6.** El modelo ayuda a describir funciones; las tecnologías de seguridad pueden trabajar en distintos niveles. No busques obligatoriamente una cabecera independiente de capa 5 y otra de capa 6 en cada paquete de Internet.

## 5. Cómo se relaciona con TCP/IP

Usaremos esta correspondencia didáctica habitual:

| Modelo TCP/IP de cuatro capas | Funciones OSI relacionadas | Ejemplos de esta sesión |
| --- | --- | --- |
| Application — Aplicación | 7, 6 y 5 | HTTP y funciones de la aplicación |
| Transport — Transporte | 4 | TCP; UDP se estudiará después |
| Internet — Internet | 3 | IP e ICMP |
| Network Access / Link — Acceso a red / enlace | 2 y 1 | Ethernet y transmisión de señales |

La correspondencia aproxima responsabilidades; no convierte ambos modelos en definiciones idénticas. RFC 1122 describe las capas de aplicación, transporte, Internet y enlace de la arquitectura de Internet. [4] El esquema comparativo también aparece en material de Cisco Networking Academy. [6]

Algunos libros emplean **cinco capas**: separan enlace y física dentro de acceso a red. No aparece un protocolo nuevo por dibujar cinco casillas. Cuando leas una pregunta, identifica el modelo usado y responde con sus nombres.

La capa **Internet** no exige estar conectado a la Internet pública. Una LAN privada también puede usar IP. Y usar la familia TCP/IP **no significa que todos los mensajes usen TCP**.

## 6. Encapsulación: información dentro de información

Un protocolo necesita datos de control para tratar lo que recibe. Llamamos:

- **Header — cabecera:** información situada antes de los datos transportados.
- **Payload — carga útil:** lo que un protocolo está transportando; puede contener una unidad completa del protocolo superior.
- **Trailer — campo final:** información que algunos protocolos añaden al final.
- **PDU — Protocol Data Unit:** unidad de datos de un protocolo.

**Encapsular** es incorporar información de un protocolo alrededor de datos que se le entregan. **Desencapsular** es interpretar esa información y entregar el contenido al protocolo que corresponda. [3]

Para una solicitud **HTTP sobre TCP sobre IPv4 sobre Ethernet**, el ejemplo es:

| Perspectiva | Nombre que usaremos | Contenido simplificado |
| --- | --- | --- |
| Aplicación | Datos o mensaje HTTP | Solicitud HTTP |
| Transporte TCP | Segmento | Cabecera TCP + datos HTTP |
| Red IPv4 | Paquete | Cabecera IPv4 + segmento TCP |
| Enlace Ethernet | Trama | Cabecera Ethernet + paquete IPv4 + campo final de comprobación |
| Física | Bits representados por señales | Transmisión del contenido de la trama por el medio |

No son cinco mensajes independientes que viajan uno detrás de otro. El paquete **contiene** el segmento y la trama **contiene** el paquete. La capa física transmite señales; no añade una “cabecera OSI número 1” universal.

Tampoco afirmamos que cualquier mensaje de aplicación quepa en un solo segmento o trama. Puede dividirse en varias unidades. Aquí elegimos un mensaje pequeño y omitimos intercambios de preparación para concentrarnos en el anidamiento.

**Carga útil depende del punto de vista:** para IPv4, el segmento TCP completo es contenido transportado; para TCP, la solicitud HTTP es contenido transportado. “Payload” no significa siempre “solo el texto que lee una persona”.

## 7. Ejemplo completo: la página local de Campus Aurora

### Condiciones del escenario

Ana usa PC-A para acceder a WEB-A. Ambos están en la misma LAN, unidos por un switch de capa 2, sin router ni traducción de direcciones. Usamos HTTP sin cifrar **solo dentro de este laboratorio aislado**; no es una recomendación para publicar servicios reales.

| Elemento | PC-A | WEB-A |
| --- | --- | --- |
| IPv4 | 192.168.10.20 | 192.168.10.30 |
| Máscara | 255.255.255.0 | 255.255.255.0 |
| MAC ficticia para explicar | 0200.0000.0020 | 0200.0000.0030 |
| Puerto TCP de este ejemplo | 50000, elegido por el cliente | 80, servicio HTTP del laboratorio |

Las MAC y el puerto del cliente son **valores didácticos**, no resultados de tu simulador. En la práctica conservarás los que observes. Las direcciones IP y MAC tienen funciones diferentes; su formato y cálculo se desarrollarán más adelante.

### Paso a paso, desde la solicitud hasta la respuesta

1. **Aplicación:** Ana escribe `http://192.168.10.30` en el navegador. La dirección literal evita necesitar una consulta DNS en este ejercicio. Se genera una solicitud HTTP.
2. **Transporte:** TCP identifica los extremos de la conversación mediante puertos. Para nuestro ejemplo, origen `50000` y destino `80`. Antes de transportar los datos, TCP necesita establecer la conexión; analizaremos ese proceso después.
3. **Red:** IPv4 identifica origen `192.168.10.20` y destino `192.168.10.30`.
4. **Enlace:** Ethernet usa la MAC de PC-A como origen y la de WEB-A como destino en esta LAN. Puede requerirse ARP para conocer la MAC asociada a la IPv4 local; si aparece en el simulador, es preparación adicional, no el mensaje HTTP.
5. **Física:** la información se representa mediante señales y pasa al switch.
6. **Switch:** su función de conmutación de capa 2 entrega la trama hacia el destino usando información Ethernet. No necesita interpretar la página HTTP para cumplir esa función. En este recorrido sin enrutamiento, no sustituye las MAC de los extremos por su propia MAC.
7. **Servidor:** recibe la trama, interpreta la información de enlace, red y transporte, y entrega los datos al servicio HTTP. Este proceso permite que la solicitud llegue a la aplicación.
8. **Respuesta:** el servidor genera una respuesta HTTP y repite el proceso de encapsulación en sentido contrario. PC-A la recibe y su navegador presenta el contenido.

Para **esa misma conexión** del ejemplo:

| Campo | Solicitud de PC-A | Respuesta de WEB-A |
| --- | --- | --- |
| MAC origen | 0200.0000.0020 | 0200.0000.0030 |
| MAC destino | 0200.0000.0030 | 0200.0000.0020 |
| IP origen | 192.168.10.20 | 192.168.10.30 |
| IP destino | 192.168.10.30 | 192.168.10.20 |
| Puerto TCP origen | 50000 | 80 |
| Puerto TCP destino | 80 | 50000 |

El **puerto TCP 80 no es un conector físico** del switch. El primero es un identificador de transporte; el segundo es una interfaz del dispositivo.

Si el destino estuviera en otra red, entraría en juego un router y habría nuevos encabezados de enlace en los saltos enrutados. No generalices la tabla anterior a cualquier recorrido: hoy estamos observando una sola LAN.

## 8. Ping no sigue exactamente el mismo conjunto de protocolos

En la terminal de una PC de Packet Tracer, con el servidor del laboratorio configurado, puedes ejecutar:

```text
ping 192.168.10.30
```

Es un comando de consulta para el laboratorio. No se ejecutó al redactar este material.

La utilidad genera mensajes de eco **ICMP**, transportados por IP. ICMP se sitúa en la capa Internet del modelo TCP/IP, no es TCP ni UDP y no usa sus puertos. [4]

| Prueba en nuestra LAN | Encapsulación relevante, de dentro hacia fuera | Qué se observa |
| --- | --- | --- |
| Ping IPv4 | ICMP dentro de IPv4 dentro de Ethernet | Respuestas de eco y tiempos de esa muestra |
| Solicitud web del ejercicio | HTTP dentro de TCP dentro de IPv4 dentro de Ethernet | Intercambio con el servicio web |

Por eso **no debes inventar un segmento TCP dentro de cada ping**. Tampoco un programa ejecutándose en un equipo demuestra automáticamente todas las funciones de la capa de aplicación del servicio que quieres probar.

## 9. Diagnóstico por capas sin conclusiones apresuradas

Puedes comenzar por abajo y avanzar, o empezar con una prueba que divida las posibilidades. Lo importante es justificar cada paso.

| Evidencia | Qué ayuda a establecer | Qué no garantiza |
| --- | --- | --- |
| Enlace físico activo | Existe enlace en ese puerto bajo esas condiciones | Direccionamiento correcto o aplicación disponible |
| Trama observada en el switch | Hay tráfico de enlace en ese punto | Entrega final ni respuesta del servicio |
| Respuesta ICMP del destino | La muestra tuvo comunicación de eco de ida y vuelta | Puerto TCP accesible, HTTP correcto ni capacidad máxima |
| Respuesta HTTP a una solicitud nueva | Ese intercambio con el servicio tuvo respuesta | Ausencia de problemas en todos los servicios o momentos |

**Incidente didáctico:** WEB-A responde a ping pero el navegador no obtiene una página nueva. Es razonable investigar el servicio web o el transporte que usa; no es razonable declarar “toda la red está bien” ni “el cable está roto” sin más evidencia.

Si tú desactivas HTTP en el servidor simulado y luego lo reactivas, puedes contrastar una hipótesis mediante un cambio conocido. El origen está en la configuración del servicio, aunque el síntoma pueda manifestarse como un fallo de conexión TCP. Las capas organizan preguntas; no son una regla para asignar toda falla a una única casilla.

## 10. Errores comunes

| Error | Corrección |
| --- | --- |
| “Siete capas significan siete equipos” | Son funciones, no un inventario |
| “Cada paquete tiene siete cabeceras” | Inspecciona los protocolos reales; el modelo no impone siete encabezados |
| “El switch lee HTTP para reenviar una trama” | La conmutación de capa 2 no necesita interpretar HTTP |
| “Puerto 80 es el puerto del cable” | Distingue identificador TCP e interfaz física |
| “TCP/IP siempre utiliza TCP” | La familia incluye otros protocolos; ping usa ICMP sobre IP |
| “No veo ARP; por tanto no existe” | Puede haber información ya aprendida o filtros de visualización |
| “El ping funciona; la web funciona” | Son pruebas de intercambios distintos |
| “Simulation es una captura de mi red física” | Es una representación de la topología simulada |

## 11. Vocabulario para el examen

| Inglés | Español / uso |
| --- | --- |
| Reference model | Modelo de referencia |
| Protocol suite | Familia de protocolos |
| Layer / stack | Capa / pila de protocolos |
| Encapsulation / decapsulation | Encapsulación / desencapsulación |
| Header / payload / trailer | Cabecera / carga útil / campo final |
| Segment / packet / frame | Segmento / paquete / trama, según el protocolo |
| Source / destination | Origen / destino |
| Inbound / outbound | Entrante / saliente respecto del dispositivo observado |
| Port | Puerto; identifica si es lógico de transporte o interfaz física |
| Reachability | Alcance o posibilidad de comunicación comprobada por una prueba |

Frase para practicar: **A successful ping does not prove that the web service is working.**

## 12. Preguntas de comprensión y repaso

Responde sin mirar la solución de la práctica:

1. Enumera OSI desde la capa 1 hasta la 7, con nombre en español e inglés.
2. ¿Con qué capa TCP/IP se relacionan las funciones OSI 5, 6 y 7 en nuestra correspondencia?
3. En HTTP sobre TCP/IPv4/Ethernet, ¿qué contiene el paquete IPv4 y qué contiene la trama?
4. ¿Por qué el puerto TCP 80 no es lo mismo que FastEthernet0/1?
5. ¿Qué identificador consulta principalmente la función de conmutación Ethernet para entregar una trama hacia un destino?
6. ¿Un ping IPv4 necesita TCP? Justifica con los protocolos del ejemplo.
7. Si ping responde pero HTTP no, escribe una hipótesis y una prueba que la investigue sin cambiar equipos reales.
8. **Which OSI layer is associated with IP addressing?** Responde en inglés y añade una frase explicativa.

### Repaso de un minuto

- OSI tiene siete capas; usaremos TCP/IP con cuatro.
- Encapsular agrega información de protocolos; recibir requiere interpretarla.
- MAC, IP y puertos de transporte no son intercambiables.
- Una trama puede contener un paquete, que puede contener un segmento con datos de aplicación.
- No todos los mensajes llevan TCP ni pasan por los mismos protocolos.
- Un resultado de prueba demuestra algo concreto, no el funcionamiento universal de la red.

**Siguiente sesión prevista:** S004 — Ethernet, IP, TCP y UDP: qué problema resuelve cada uno. Antes, completa la práctica S003 y conserva tus dudas. Estudio y dominio permanecen sin verificar hasta recibir evidencia.

## 13. Fuentes oficiales y alcance de la consulta

Consultadas el **16 de octubre de 2026**. Los ejemplos, direcciones, preguntas y ejercicios son elaboración docente original.

1. [Cisco: temario oficial 200-301 CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf). Se confirmaron los objetivos pertinentes; no se requiere cambiar el plan.
2. [Cisco: fechas de transición del examen](https://blogs.cisco.com/learning/stay-on-track-get-certified-before-the-ccna-refresh).
3. [Cisco: Understanding TCP/IP — modelos y encapsulación](https://www.cisco.com/E-Learning/bulk/public/tac/cim/cib/using_cisco_ios_software/linked/tcpip.htm). Referencia histórica para arquitectura; no se utiliza como guía de configuración o seguridad actual.
4. [RFC Editor / IETF: RFC 1122, sección 1.1.3](https://www.rfc-editor.org/rfc/rfc1122.html#section-1.1.3). Arquitectura de Internet, transporte, IP e ICMP; no sustituye las revisiones posteriores de cada protocolo.
5. [Cisco: What Is Network Programming? — las siete capas OSI](https://www.cisco.com/site/us/en/learn/topics/networking/what-is-network-programming.html).
6. [Cisco Networking Academy: material de modelos de comunicación](https://www.cisco.com/web/TH/assets/docs/seminar/20080401_handout1.pdf). Correspondencia conceptual entre modelos; no es un temario del examen actual.

---

Fin de la lección S003.
