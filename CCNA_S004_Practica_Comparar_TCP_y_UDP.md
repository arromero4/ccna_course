# CCNA S004 — Práctica: comparar TCP y UDP

**Preparación:** 16 de octubre de 2026.  
**Lección asociada:** `CCNA_S004_Leccion_Ethernet_IP_TCP_y_UDP.md`.  
**Tiempo sugerido:** 40 minutos; instalación y dificultades de acceso requieren tiempo adicional.  
**Objetivo de certificación:** 200-301 CCNA v2.0.  
**Relación curricular:** fundamentos de apoyo a 1.6 y 2.4; no se evalúa el dominio completo de esos IDs.  
**Escenario:** Campus Aurora, red ficticia y aislada de laboratorio.

## 1. Preparación, plataforma y evidencia

Lee la lección S004. Necesitas **Cisco Packet Tracer de escritorio**, una PC-PT, un switch 2960 y un Server-PT. Anota la versión exacta en **Help → About**, o su equivalente. Usaremos funciones básicas de DNS, HTTP y Simulation; la ubicación o traducción de los controles puede variar entre versiones. La guía no afirma haber sido ejecutada o validada empíricamente en una versión concreta.

**El profesor no ejecutó este laboratorio al redactarlo.** Usa estas etiquetas en tu informe:

- **Predicción:** lo que esperas antes de hacer una prueba.
- **Observación en simulador:** lo que tú observaste al ejecutarla en Packet Tracer.
- **Análisis de escenario:** razonamiento a partir de los datos ficticios del apartado 7.

El modo Simulation representa protocolos; no es una captura de una red física ni una medición de rendimiento real. No sustituye todos los comportamientos de un sistema operativo o de IOS XE. Esta práctica no requiere CML. Si en otra sesión una función excede Packet Tracer, se indicará un laboratorio autorizado con IOS XE/CML e imágenes oficiales.

Todo cambio se hace en este laboratorio. No necesitas cambiar DNS en tu computadora real ni entregar archivos `.pkt`, capturas de pantalla u otros formatos: basta la evidencia escrita.

| Actividad | Tiempo orientativo | Objetivos de la lección |
| --- | ---: | --- |
| A. Responsabilidades | 5 min | O1, O2 |
| B. Interpretar el transporte | 5 min | O3, O4 |
| C. Observar dos intercambios | 24 min | O2, O5 |
| D. Diagnosticar y restaurar | 6 min | O4, O6 |

Antes de empezar, responde R1–R3 de la lección sin consultar. No suman puntos.

## 2. Actividad A — Qué hace cada protocolo

Para **A1–A4**, escribe el protocolo y una razón. Cada respuesta debe explicar la responsabilidad, no solo repetir una sigla.

| Código | Situación | Protocolo y razón |
| --- | --- | --- |
| A1 | Una trama debe llegar a la interfaz destino de esta LAN usando direcciones MAC. | Por completar |
| A2 | Un paquete necesita identificar direcciones lógicas de origen y destino para circular entre redes. | Por completar |
| A3 | Una aplicación necesita recibir un flujo de bytes fiable y en orden. | Por completar |
| A4 | Una aplicación envía datagramas sin pedir al transporte un handshake ni retransmisiones propias. | Por completar |

En A1 añade por qué una MAC no reemplaza al puerto de destino de TCP. En A2 añade por qué el direccionamiento IP, por sí solo, no garantiza la entrega.

**Evidencia:** cuatro asociaciones razonadas. No afirmes que un protocolo garantiza más de lo que ofrece.

## 3. Actividad B — Secuencias, ACK y elección

Todos los números de este apartado son ficticios. No uses Packet Tracer todavía.

### B1. Completa el handshake

Supón conexión nueva, sin datos de aplicación en estos mensajes:

| Mensaje | Sentido | Flags | Seq | Ack |
| --- | --- | --- | ---: | --- |
| 1 | Cliente a servidor | SYN | 250 | No significativo |
| 2 | Servidor a cliente | SYN, ACK | 800 | Por completar |
| 3 | Cliente a servidor | ACK | Por completar | Por completar |

Completa los tres campos y explica por qué SYN afecta a la numeración.

### B2. Cuenta bytes

Después de B1, el cliente envía 300 bytes comenzando en `Seq=251`, sin SYN ni FIN. El servidor recibe todo ese tramo en orden. Escribe la última posición recibida y el ACK que esperas. Explica por qué no estás contando paquetes.

### B3. Justifica un requisito

Una aplicación va a usar directamente TCP o UDP. Su diseño exige recibir un flujo ordenado y recuperar pérdidas, sin implementar esos mecanismos por su cuenta. ¿Cuál encaja con el requisito? ¿La elección garantiza cifrado? Da una razón para cada respuesta.

## 4. Actividad C — Dos servicios, una misma LAN

### C1. Escenario, enlaces y direcciones

Ana usa PC-A para consultar el nombre del portal y abrir la página alojada en WEB-A. SW1 conserva su configuración inicial. No hace falta configurar IP en el switch para conmutar este tráfico.

**Topología textual mediante tabla de enlaces:**

| Extremo | Interfaz | Extremo | Interfaz | Cable |
| --- | --- | --- | --- | --- |
| PC-A | FastEthernet0 | SW1 | FastEthernet0/1 | Copper Straight-Through |
| WEB-A | FastEthernet0 | SW1 | FastEthernet0/2 | Copper Straight-Through |

| Equipo | IPv4 | Máscara | Gateway | Servidor DNS configurado en el cliente | Servicios |
| --- | --- | --- | --- | --- | --- |
| PC-A | 192.168.10.20 | 255.255.255.0 | Vacío | 192.168.10.30 | Navegador y consola |
| WEB-A | 192.168.10.30 | 255.255.255.0 | Vacío | No se necesita para responder | HTTP y DNS |
| SW1 | No se configura | No aplica | No aplica | No aplica | Conmutación inicial |

Los dos extremos están en la misma subred. Copia los valores proporcionados; el cálculo de subredes se estudia después. No añadas gateway ni salida a Internet.

1. Abre un escenario temporal nuevo y añade los tres dispositivos. Asígnales los nombres indicados y conecta los enlaces. Si ya tienes el escenario de S003 y prefieres reutilizarlo, anota primero sus valores originales para revertir los cambios; no supongas que S003 fue ejecutada por haberse entregado.
2. En **Realtime**, espera a que los enlaces estén listos. Si el modelo muestra nombres de interfaz distintos, documenta la equivalencia.
3. En **Desktop → IP Configuration** de cada extremo, configura IPv4 y máscara estáticas. En PC-A configura además DNS `192.168.10.30`. Deja gateway vacío.
4. En **WEB-A → Services → HTTP**, selecciona **HTTP: On**. No hace falta editar la página predeterminada.
5. En **WEB-A → Services → DNS**, selecciona **DNS: On** y añade este registro. Usa **Add** o el control equivalente; verifica que aparezca en la lista.

   | Name | Type | Address |
   | --- | --- | --- |
   | portal.aurora.test | A Record | 192.168.10.30 |

   No sobrescribas un registro previo que necesites conservar. Si existe el mismo nombre, comprueba primero su valor y anota el estado original.

6. En **PC-A → Desktop → Command Prompt**, ejecuta:

   ```text
   ping 192.168.10.30
   ```

7. Registra el resultado. Si aparece una pérdida inicial, repite una vez cuando la resolución de vecinos y los enlaces estén listos. Si continúa fallando, revisa enlaces, IP, máscara y duplicados antes de seguir. Un ping correcto es una referencia de alcance IP; todavía no prueba DNS ni HTTP.

**Evidencia C1:** versión de Packet Tracer, topología utilizada, configuración y resultado del ping. No copies un resultado esperado como si lo hubieras visto.

### C2. Observar DNS sobre UDP

1. Cambia a **Simulation**. En **Edit Filters**, selecciona **ARP, DNS, UDP, TCP y HTTP**, según los filtros disponibles. Si UDP no aparece como filtro independiente, selecciona DNS y revisa el encabezado de transporte dentro de la PDU.
2. En la consola de PC-A ejecuta:

   ```text
   nslookup portal.aurora.test
   ```

3. Avanza con **Capture/Forward** hasta observar la consulta y su respuesta. Si aparece ARP primero, no lo cuentes como handshake TCP.
4. Abre el evento en PC-A o WEB-A y revisa **Outbound PDU Details** o **Inbound PDU Details**, según el sentido. Localiza IPv4 y UDP. En algunos eventos solo verás las capas que ese dispositivo procesa.
5. Anota las IP y puertos de la consulta y de la respuesta. El puerto temporal real puede ser diferente del ejemplo de la lección. Comprueba qué dirección devuelve DNS.

**Evidencia C2:** dos filas, una para consulta y otra para respuesta, con protocolo, IP y puertos, más la respuesta DNS. Si tu versión no ofrece el comando o no muestra un campo, escribe esa limitación; no inventes una salida.

### C3. Observar HTTP sobre TCP

1. Conserva tus notas de C2. Puedes limpiar la lista de eventos para facilitar la lectura, sin borrar la configuración ni reiniciar los dispositivos.
2. En **PC-A → Desktop → Web Browser**, escribe en la barra de direcciones:

   ```text
   http://192.168.10.30
   ```

   Esto se introduce en el navegador, no en la consola de comandos. Usamos la IP para separar esta prueba de DNS.

3. Pulsa **Go** y avanza la simulación. En una conexión nueva, busca los mensajes TCP con flags **SYN**, **SYN/ACK** y **ACK**. Escribe los valores de secuencia y confirmación que exponga tu versión.
4. Si solo ves parte del intercambio, revisa filtros y punto de inicio. La reutilización de una conexión o los límites del simulador pueden impedir observar un handshake nuevo. Registra lo que falta; no inventes números. Puedes iniciar la observación desde un escenario limpio si necesitas repetirla.
5. Busca la solicitud HTTP y su respuesta. Revisa que el transporte sea TCP y que el destino de la solicitud sea el puerto 80. Si solo miras un evento del switch, abre además uno del cliente o servidor para inspeccionar el transporte.
6. Continúa hasta que el navegador reciba la página o muestre un error. Anota el resultado concreto y la evidencia de una respuesta nueva, no solo una página que permaneció visible de antes.

**Evidencia C3:** transporte, IP, puertos, flags visibles y resultado HTTP. Para comparar con UDP, responde: ¿qué establecimiento viste en TCP y qué diferencia observaste en la consulta DNS?

### C4. Tabla de observaciones

Completa con tus datos; si usaste el escenario del apartado 7, cambia la etiqueta de todas las filas a “análisis de escenario”.

| Prueba | Protocolo de transporte | Origen IP:puerto | Destino IP:puerto | Evidencia del resultado | Tipo de evidencia |
| --- | --- | --- | --- | --- | --- |
| Consulta DNS | Por completar | Por completar | Por completar | Por completar | Por completar |
| Respuesta DNS | Por completar | Por completar | Por completar | Por completar | Por completar |
| Solicitud HTTP | Por completar | Por completar | Por completar | Por completar | Por completar |
| Respuesta HTTP | Por completar | Por completar | Por completar | Por completar | Por completar |

**Comparación:** explica dónde aparecen Ethernet e IP en ambas conversaciones y por qué no puedes concluir que UDP sea siempre más rápido a partir de esta simulación.

## 5. Actividad D — Falla de servicio y reversión

**D1. Predicción.** Antes de cambiar nada, escribe qué esperas que ocurra con una consulta DNS nueva y con HTTP por IP si solo detienes DNS en WEB-A. Explica qué mantendrás constante.

**D2. Una sola modificación.** En WEB-A, cambia **Services → DNS → Off**. Conserva HTTP en On y no alteres cables, IP, máscara, registro A ni DNS configurado en PC-A.

**D3. Pruebas comparables.** Repite `nslookup portal.aurora.test` y la visita a `http://192.168.10.30`. En Simulation verifica que generas tráfico nuevo. Una página almacenada o una consulta que no sale por caché no prueban el estado actual del servicio. Si no se genera el intercambio, anota “prueba no concluyente” y el motivo. El texto exacto de error, los temporizadores y la presencia de mensajes ICMP pueden variar; registra lo observado.

**D4. Diagnóstico.** Escribe:

- El síntoma y la evidencia antes y después del cambio.
- Una hipótesis delimitada: qué servicio dejó de responder.
- Qué dato respalda que HTTP por IP sigue disponible, si lo comprobaste.
- Por qué eso no demuestra que todo UDP esté averiado ni que todos los servicios de la red funcionen.

**D5. Restauración y verificación.** Vuelve a poner **DNS: On**. Repite una consulta nueva y comprueba la respuesta `192.168.10.30`. Repite la prueba HTTP por IP y comprueba una respuesta nueva. El estado final del ejercicio debe tener ambos servicios en On y mantener la tabla de direccionamiento.

Para devolver un escenario reutilizado a su estado anterior a S004, restaura también el DNS del cliente, el estado inicial del servicio DNS y el registro que añadiste o modificaste, según las notas de C1. No borres otros registros. Si creaste un escenario temporal, puedes cerrarlo al terminar sin sobrescribir material previo. Conserva el informe escrito de lo aprendido.

### Si la práctica no se comporta como esperas

| Síntoma | Hipótesis inicial | Prueba concreta antes de cambiar algo |
| --- | --- | --- |
| No responde ping | Enlace, IP o máscara incorrectos | Comparar extremos e interfaces con C1; revisar ambos enlaces. |
| Ping funciona, DNS no | DNS del cliente, servicio o registro incorrectos | Revisar destino IP de la consulta, DNS On y registro A. |
| DNS responde, HTTP no | Servicio HTTP o acceso web incorrectos | Revisar HTTP On, URL por IP y eventos TCP/HTTP. |
| No se ve SYN nuevo | Filtros, captura tardía o conexión reutilizada | Revisar filtros y cuándo empezó la observación. |
| Al detener DNS parece no cambiar nada | Resultado previo o ausencia de consulta nueva | Comprobar que sale una consulta y que la respuesta pertenece a ese intento. |
| La simulación parece detenida | Eventos pendientes | Avanzar con Capture/Forward o volver temporalmente a Realtime. |

**Método:** observa, plantea una hipótesis, cambia una variable si corresponde, repite la misma prueba y verifica la reversión. Si debes corregir una falla de preparación, anótala por separado de la falla DNS introducida deliberadamente.

## 6. Desafío y formato de entrega

**Desafío opcional, sin puntos:** otra persona afirma “el switch rompió UDP porque falló DNS”. Redacta una respuesta de cuatro frases que incluya evidencia, una hipótesis más precisa, una prueba y una limitación de lo que puedes concluir. No introduzcas averías adicionales.

Envía tus respuestas como texto o Markdown con este esquema:

1. **Modo y versión:** observación en Packet Tracer o análisis de escenario; versión si corresponde.
2. **R1–R3, A1–A4 y B1–B3:** respuestas razonadas.
3. **C:** configuración, ping, tabla de observaciones y comparación.
4. **D1–D5:** predicción, cambio, pruebas, diagnóstico y restauración.
5. **Q1–Q8 de la lección:** respuestas antes de mirar soluciones.
6. **Duda principal:** concepto o paso que necesita refuerzo.

No atribuyas observaciones al simulador si no lo ejecutaste. Las respuestas permitirán registrar estudio y evaluar razonamiento; la mera entrega del material no confirma ninguno de esos estados.

## 7. Alternativa de análisis si aún no puedes ejecutar Packet Tracer

Estos datos son **completamente didácticos**. No proceden de una ejecución del profesor. Puedes completar A, B y el razonamiento de C/D, marcando toda la evidencia como análisis. La práctica ejecutada seguirá sin confirmar.

| Evento ficticio | Origen | Destino | Información |
| --- | --- | --- | --- |
| Consulta UDP | 192.168.10.20:53000 | 192.168.10.30:53 | DNS pregunta por portal.aurora.test. |
| Respuesta UDP | 192.168.10.30:53 | 192.168.10.20:53000 | DNS devuelve 192.168.10.30. |
| TCP 1 | 192.168.10.20:51000 | 192.168.10.30:80 | SYN, Seq=100. |
| TCP 2 | 192.168.10.30:80 | 192.168.10.20:51000 | SYN/ACK, Seq=400, Ack=101. |
| TCP 3 | 192.168.10.20:51000 | 192.168.10.30:80 | ACK, Seq=101, Ack=401. |
| HTTP | Cliente y servidor anteriores | Extremo opuesto | Solicitud y respuesta dentro de TCP; aparece la página. |

Para D, analiza este conjunto de resultados ficticios:

| Estado | Consulta nueva a DNS | Nueva solicitud HTTP por IP |
| --- | --- | --- |
| Inicial: ambos servicios On | Respuesta correcta | Respuesta HTTP y página |
| Solo DNS Off | No se recibe respuesta DNS válida | Respuesta HTTP y página |
| DNS restaurado a On | Respuesta correcta | Respuesta HTTP y página |

Escribe qué apoyan estos resultados y qué no prueban. No inventes tiempos, pérdidas, MAC ni otros campos que no se proporcionaron.

## 8. Rúbrica y comprobación

| Criterio | Puntos | Cómo se asignan |
| --- | ---: | --- |
| A1–A4: responsabilidades | 8 | Dos por caso: protocolo correcto y razón válida; incluir las distinciones pedidas en A1 y A2. |
| B1: handshake | 2 | Uno por campos completos correctos; uno por explicar SYN. |
| B2: bytes y ACK | 2 | Uno por última posición y ACK; uno por explicar el conteo. |
| B3: requisito y cifrado | 2 | Uno por transporte justificado; uno por distinguir cifrado. |
| C: observación y comparación | 8 | Dos por estado inicial; dos por DNS/UDP; dos por TCP/HTTP; dos por relacionar capas y límites de la comparación. |
| D: diagnóstico y reversión | 8 | Dos por predicción y una sola variable; dos por pruebas comparables; dos por conclusión delimitada; dos por restauración y verificación. |
| **Total** | **30** | **24/30 = 80%, criterio interno de entrenamiento.** |

R1–R3, Q1–Q8 y el desafío sirven para retroalimentación adicional y no duplican puntos. Si usas el apartado 7, puedes valorar el razonamiento con la misma rúbrica, pero registra la modalidad de análisis: incluso 30/30 no confirma ejecución en laboratorio. Campos no visibles se anotan como tales y se revisan por el razonamiento que puedas demostrar.

El 80% **no es una nota oficial de aprobación de Cisco**. Para continuar con solidez, explica tus respuestas sin depender de las soluciones. Entre 60% y 79%, refuerza los errores; por debajo de 60%, vuelve al fundamento que los causó, conforme al plan.

**Verificación antes de entregar:** identifica el transporte por sus campos; no confunde ACK con paquetes; separa resultado DNS de HTTP; registra evidencia nueva y limitaciones; y deja documentada la reversión o el estado pendiente.

## 9. Fuentes oficiales y primarias

Consultadas el **16/10/2026**. La actividad y sus preguntas son originales; no reproducen un banco de examen.

- Cisco: [temario CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf), IDs 1.6 y 2.4 como referencia de apoyo.
- Cisco: [fechas de transición de CCNA](https://blogs.cisco.com/learning/stay-on-track-get-certified-before-the-ccna-refresh). Al preparar esta sesión está disponible v1.1 hasta el 02/02/2027; el objetivo del programa es v2.0, anunciada para el 03/02/2027.
- Cisco Networking Academy: [Packet Tracer Simulation — Exploration of TCP and UDP](https://contenthub.netacad.com/legacy/CCNA/ITN/5.02/hu/course/files/7.3.1.2%20Packet%20Tracer%20Simulation%20-%20Exploration%20of%20TCP%20and%20UDP%20Instructions.pdf), referencia histórica de Simulation, Web Browser y `nslookup`. No fija una versión actual ni garantiza idéntica interfaz.
- RFC Editor: [RFC 9293, TCP](https://www.rfc-editor.org/rfc/rfc9293.html), servicio y numeración; [RFC 768, UDP](https://www.rfc-editor.org/rfc/rfc768.html), campos y datagramas; [RFC 7766, DNS sobre TCP](https://www.rfc-editor.org/rfc/rfc7766.html), límites de la generalización sobre DNS y UDP.

---

## 10. Soluciones razonadas — consultar después del intento

### Recuperación R1–R3

- **R1:** IP corresponde a OSI 3, red; TCP a OSI 4, transporte. Las funciones ayudan a ubicar los protocolos.
- **R2:** no. El ping prueba un intercambio ICMP, no el establecimiento de TCP ni la respuesta de HTTP.
- **R3:** 10 MB/s × 8 = **80 Mb/s**. La distinción es bytes frente a bits, usando unidades decimales.

### Actividad A

- **A1:** Ethernet, porque la trama usa MAC para entrega de enlace. La MAC identifica la interfaz en ese contexto; un puerto TCP distingue el extremo de transporte de una aplicación. Una no sustituye al otro.
- **A2:** IP, porque aporta direccionamiento lógico y permite encaminar paquetes entre redes. Su servicio de mejor esfuerzo no incluye confirmación ni retransmisión de extremo a extremo.
- **A3:** TCP, porque entrega un flujo de bytes ordenado y utiliza mecanismos para recuperar pérdidas.
- **A4:** UDP, porque transporta datagramas sin handshake, ACK ni retransmisión propios equivalentes a los de TCP. La aplicación puede añadir mecanismos si los necesita.

### Actividad B

- **B1:** mensaje 2, `Ack=251`; mensaje 3, `Seq=251` y `Ack=801`. Cada SYN consume una posición en el espacio de secuencia de su emisor. El ACK sin datos del tercer mensaje no consume otra posición.
- **B2:** última posición: `251 + 300 − 1 = 550`. El ACK es **551**, el siguiente byte esperado. Un segmento puede transportar muchos bytes; el ACK no es un contador de segmentos.
- **B3:** TCP satisface directamente el requisito de flujo fiable y ordenado. Elegir TCP no proporciona cifrado; el transporte y la confidencialidad resuelven necesidades diferentes.

### Actividad C

El resultado previsto es una consulta DNS a **UDP/53** y una conexión web hacia **TCP/80**. En las respuestas se invierten los extremos de la conversación. Los puertos temporales no tienen por qué ser 53000 y 51000: esos valores solo pertenecen al ejemplo.

Ethernet lleva los paquetes en la LAN e IP identifica los extremos en ambas conversaciones. Dentro de IP encuentras el transporte correspondiente. Los eventos de ARP no son el establecimiento TCP; son resolución de vecinos IPv4 en el enlace.

Si observaste un establecimiento nuevo de TCP, sus flags deben permitir identificar SYN, SYN/ACK y ACK. Los números exactos pueden diferir. DNS sobre UDP no necesita ese establecimiento de TCP. La ausencia de un evento visible por filtros o por captura tardía no demuestra que un protocolo haya omitido un paso.

La comparación no mide rendimiento real: son aplicaciones diferentes y tráfico simulado. Además, el comportamiento completo depende del diseño de cada aplicación. No permite afirmar que UDP siempre sea más rápido ni que DNS siempre use UDP.

### Actividad D

- **D1:** se espera que una consulta nueva deje de obtener respuesta DNS válida y que HTTP por IP pueda seguir funcionando, pues no requiere resolver el nombre. Mantener direcciones, enlaces y HTTP constantes ayuda a aislar el cambio.
- **D2:** la única modificación deliberada es **DNS On → Off**. Cambiar a la vez IP o cable impediría atribuir el resultado a una causa aislada.
- **D3:** una nueva respuesta HTTP por IP y la ausencia de una respuesta DNS válida respaldan la separación de servicios. Una página antigua en pantalla no es evidencia suficiente. Si no se genera tráfico nuevo, la prueba es no concluyente.
- **D4:** en este experimento controlado, detener DNS explica el cambio observado. No hay fundamento para declarar que el switch rompió UDP ni para asegurar que todos los servicios funcionan. Fuera del experimento, habría que comprobar más causas antes de atribuir la falla al servicio.
- **D5:** se restaura DNS a On y se repiten ambas pruebas. La recuperación de DNS y la continuidad de HTTP verifican el estado previsto. Si una prueba falla, documenta la falla y continúa el diagnóstico; no marques “restaurado y verificado” sin evidencia.

### Desafío: una respuesta posible

“La consulta DNS nueva dejó de recibir respuesta mientras HTTP por IP continuó respondiendo. La hipótesis más precisa es que el servicio DNS está detenido, dado el único cambio realizado. Restauraría DNS y repetiría la misma consulta, conservando las demás condiciones. Estos datos no demuestran una avería general de UDP ni validan todos los servicios de la red.”

### Preguntas Q1–Q8 de la lección

1. **Q1:** Ethernet contiene las MAC; IP, las direcciones IP; TCP, los puertos del caso web. Son identificadores de niveles diferentes.
2. **Q2:** origen `192.168.10.30:80`, destino `192.168.10.20:51000`, transporte TCP. Se invierten los extremos respecto de la solicitud.
3. **Q3:** segundo mensaje, **Ack=701**; tercero, **Ack=901**. Cada SYN consume una posición del espacio de secuencia de su emisor.
4. **Q4:** último byte: `501 + 120 − 1 = 620`; siguiente ACK: **621**. No hay SYN ni FIN que añadir al cálculo.
5. **Q5:** no hay contradicción. No garantizar entrega significa que UDP no aporta mecanismos propios para asegurarla; el intercambio puede completarse sin pérdida.
6. **Q6:** ping utiliza ICMP, de modo que no establece una conexión TCP. Tampoco solicita una página HTTP, por lo que no verifica el portal.
7. **Q7:** comprobaría la configuración y el estado del servicio DNS, además de que la consulta sea nueva. Evitaría afirmar que toda la red o todo UDP están averiados.
8. **Q8:** no. Fiabilidad se refiere a la entrega del flujo; cifrado, a proteger el contenido frente a lectura no autorizada. TCP por sí solo no ofrece ese cifrado.

---

**Estado:** material preparado. Estudio, práctica ejecutada y dominio permanecen sin verificar hasta recibir evidencia del estudiante.

Fin de la práctica S004.
