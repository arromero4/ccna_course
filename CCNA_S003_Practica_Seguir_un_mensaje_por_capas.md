# CCNA S003 — Práctica: seguir un mensaje por capas

**Fecha:** 16 de octubre de 2026.  
**Lección asociada:** Modelos OSI y TCP/IP.  
**Tiempo sugerido:** 40 minutos; instalar Packet Tracer, si falta, requiere tiempo adicional.  
**Objetivo de certificación:** CCNA 200-301 v2.0.  
**IDs relacionados:** apoyo a 1.6 y 2.4; no se evalúa su dominio completo.  
**Escenario:** Campus Aurora, red ficticia y aislada de laboratorio.

## 1. Preparación y reglas de evidencia

Lee primero la lección S003. Usarás **Cisco Packet Tracer de escritorio**, una PC, un switch 2960 y un Server-PT. Anota la versión instalada en **Help → About** o su equivalente en tu idioma. No se exige una versión puntual: utilizaremos funciones básicas de dispositivos finales y modo Simulation; los nombres y la ubicación de controles pueden variar.

El profesor **no ejecutó** este laboratorio al redactarlo. Diferencia en tus notas:

- **Observación en simulador:** tú realizaste la prueba dentro de Packet Tracer.
- **Análisis de escenario:** interpretaste los datos ficticios incluidos como alternativa.
- **Predicción:** indicaste qué esperabas antes de probarlo.

No llames “captura real” a los datos ficticios ni al modo Simulation. No cambies ninguna computadora o red de trabajo. No necesitas comprar equipos, abrir puertos hacia Internet ni entregar archivos `.pkt`.

| Actividad | Tiempo |
| --- | ---: |
| A. Capas y funciones | 5 min |
| B. Encapsulación razonada | 5 min |
| C. Observación y falla controlada | 24 min |
| D. Informe de diagnóstico | 6 min |

**Recuerdo previo, sin puntos:** contesta las tres preguntas de recuperación de la lección. Las soluciones aparecen al final de este archivo.

## 2. Actividad A — Identifica la responsabilidad

Completa las columnas vacías. Usa OSI de siete capas y TCP/IP de cuatro. Para OSI escribe número y nombre; para TCP/IP, nombre. Los casos 2 y 3 describen funciones conceptuales, no cabeceras independientes obligatorias.

| Caso | Función o protocolo | Capa OSI | Capa TCP/IP |
| --- | --- | --- | --- |
| A1 | Formar una solicitud HTTP | ... | ... |
| A2 | Representar caracteres en un formato acordado | ... | ... |
| A3 | Organizar el diálogo entre aplicaciones | ... | ... |
| A4 | Usar puertos TCP para distinguir una conversación | ... | ... |
| A5 | Identificar origen y destino mediante IPv4 | ... | ... |
| A6 | Usar MAC de destino al conmutar Ethernet | ... | ... |
| A7 | Transmitir información como señales por el medio | ... | ... |
| A8 | Transportar un mensaje de control ICMP | ... | ... |

**Evidencia:** ocho asociaciones, acompañadas de una explicación breve para A4 y A8. No basta con que coincidan los números si confundes sus funciones.

## 3. Actividad B — Sigue las envolturas

Supón una solicitud HTTP pequeña sobre TCP, IPv4 y Ethernet, sin túneles ni cifrado y sin fragmentación. Es un modelo simplificado, no la afirmación de que todas las transferencias se comporten igual.

1. Ordena estas unidades desde la perspectiva más cercana a la aplicación hasta la transmisión física: **trama Ethernet, datos HTTP, señales que representan bits, paquete IPv4, segmento TCP**. Explica cuáles contienen a cuáles.
2. Para IPv4, ¿el segmento TCP es cabecera propia o contenido transportado? Para TCP, ¿qué papel tienen los datos HTTP?
3. Corrige esta afirmación: “El modelo OSI tiene siete capas; por tanto un ping debe contener siete cabeceras, incluida una TCP”. Da dos razones independientes.

**Evidencia:** orden, relaciones de contenido y corrección razonada. No necesitas escribir los campos completos de los protocolos.

## 4. Actividad C — Observar una aplicación en Packet Tracer

### C1. Escenario y estado inicial

Ana, en PC-A, debe abrir la página local de avisos de WEB-A. Solo hay una LAN. SW1 conserva su configuración inicial y no necesita dirección IP para conmutar este tráfico.

**Topología textual, definida por sus enlaces:**

| Extremo | Interfaz | Extremo | Interfaz | Medio |
| --- | --- | --- | --- | --- |
| PC-A | FastEthernet0 | SW1 | FastEthernet0/1 | Cobre directo |
| WEB-A | FastEthernet0 | SW1 | FastEthernet0/2 | Cobre directo |

Si tu modelo usa otros nombres, documenta la equivalencia; no agregues otro recorrido.

| Equipo | IPv4 | Máscara | Gateway | DNS | Servicio |
| --- | --- | --- | --- | --- | --- |
| PC-A | 192.168.10.20 | 255.255.255.0 | Vacío | Vacío | Cliente web |
| WEB-A | 192.168.10.30 | 255.255.255.0 | Vacío | Vacío | HTTP activado |
| SW1 | No se configura | No aplica | No aplica | No aplica | Conmutación inicial |

Ambos extremos pertenecen a la misma subred del ejercicio. Hoy copia los valores dados; aprenderemos a calcular redes y máscaras después. Usaremos una dirección IP literal, por lo que no configuraremos DNS.

1. Abre un escenario nuevo, sin sobrescribir otro laboratorio que quieras conservar. Añade una PC-PT, un switch 2960 y un Server-PT; asígnales los nombres de la tabla.
2. Conecta los puertos indicados con **Copper Straight-Through**. En modo **Realtime**, espera a que los enlaces estén listos.
3. En PC-A y WEB-A, utiliza **Desktop → IP Configuration**, selecciona configuración estática e introduce IPv4 y máscara. Deja gateway y DNS vacíos.
4. En WEB-A, entra en **Services → HTTP** y confirma **HTTP: On**. No actives servicios adicionales ni modifiques el contenido de la página. Anota este estado inicial para poder restaurarlo.
5. En **PC-A → Desktop → Command Prompt**, ejecuta:

   ```text
   ping 192.168.10.30
   ```

6. Registra la salida. Si la primera prueba difiere, consérvala y repite después de que los enlaces estén listos. No borres evidencia para forzar el resultado esperado.
7. En **PC-A → Desktop → Web Browser**, abre `http://192.168.10.30`. Comprueba que aparece una página del servidor. No uses `https://` en este ejercicio.

**Resultado esperado:** el servidor responde al eco y entrega una página por HTTP. Si no ocurre, consulta el diagnóstico de la sección 6 antes de provocar la falla controlada.

### C2. Observa el recorrido, no solo el resultado

1. Cambia a **Simulation**. Si hay eventos anteriores, registra lo necesario y limpia la lista con el control correspondiente. Limpiar la lista no debe asumirse como borrado de todas las tablas o conexiones aprendidas.
2. En **Edit Filters**, muestra inicialmente HTTP, TCP y ARP. Si faltan filtros o eventos, selecciona todos y localiza los que interesan. Un filtro de visualización no es una regla que bloquee tráfico.
3. Desde el navegador de PC-A genera una nueva solicitud a `http://192.168.10.30`. Pulsa **Go** y comprueba que hay eventos nuevos; una página antigua visible no basta como evidencia.
4. Usa **Capture/Forward** para avanzar. El número de pasos no es fijo: puede haber preparación TCP, ARP u otros eventos. No memorices un número de clics ni esperes que el primer evento sea HTTP.
5. Selecciona un evento asociado a la solicitud HTTP. Abre **OSI Model** y los detalles de PDU disponibles, **Outbound PDU Details** o **Inbound PDU Details**, según el punto del recorrido. La actividad oficial de Cisco documenta estos controles. [3]
6. Examina la solicitud al salir de PC-A, su paso por SW1 y su llegada a WEB-A. Observa después una respuesta dirigida a PC-A. Si se muestran varias acciones por equipo, identifica cuál corresponde a entrada o salida.
7. Anota las MAC, IP y puertos que realmente muestre el simulador. **No sustituyas tus MAC ni el puerto del cliente por los valores ficticios de la lección.** Si un campo no aparece, escribe “no visible en esta vista” y consulta los detalles de la PDU; no lo inventes.

Usa esta tabla para al menos tres puntos de la solicitud y uno de la respuesta:

| Punto del recorrido | Protocolo/evento | Campo que observaste y valor | Capa OSI relacionada | Qué permite concluir |
| --- | --- | --- | --- | --- |
| PC-A, salida | ... | ... | ... | ... |
| SW1, tránsito | ... | ... | ... | ... |
| WEB-A, entrada | ... | ... | ... | ... |
| WEB-A, respuesta | ... | ... | ... | ... |

Luego responde:

- ¿Cambian IP origen y destino al atravesar únicamente SW1 en esta topología?
- ¿La conmutación de SW1 requiere interpretar el texto de la página?
- ¿Qué cambia en origen y destino cuando comparas solicitud y respuesta de la misma conexión?

**Límites:** Simulation explica eventos y campos de los protocolos implementados. No representa todas las particularidades de IOS XE ni mide el rendimiento de hardware físico. No exige una cabecera por cada capa OSI; algunas vistas pueden tener capas sin actividad. Para este laboratorio no se necesita CML. Si tu versión no permite inspeccionar una función, documenta la limitación y utiliza el escenario alternativo; no lo presentes como una captura ejecutada. Las funciones avanzadas futuras se practicarán en CML/IOS XE autorizado cuando corresponda.

### C3. Falla controlada — HTTP apagado, enlace conservado

Realiza esta parte solo después de verificar el estado inicial. El cambio afecta **únicamente al servidor ficticio dentro de Packet Tracer**.

1. Vuelve a **Realtime**. Confirma que WEB-A tenía HTTP en **On** y que los enlaces siguen conectados.
2. En **WEB-A → Services → HTTP**, cambia **solo HTTP a Off**. No cambies IPv4, máscara, cables ni otros servicios.
3. Repite `ping 192.168.10.30` desde PC-A. Registra las respuestas reales de esta muestra.
4. Intenta una solicitud web nueva. Cierra y vuelve a abrir el navegador simulado si es necesario y pulsa **Go**. Si aún ves una página anterior, usa Simulation y verifica que se generó un intento nuevo; contenido antiguo no prueba disponibilidad actual. Registra el error o comportamiento, sin inventar un mensaje exacto.
5. Compara: ¿responde el eco aunque el nuevo acceso HTTP falle? ¿Qué cambió y qué mantuviste constante?
6. **Reversión obligatoria:** restaura **HTTP: On**. Repite una solicitud nueva y el ping de control. El estado final debe coincidir con el inicial: enlaces intactos, mismas direcciones, HTTP activado.

| Estado | HTTP configurado | Evidencia de ping | Evidencia de solicitud web nueva | Conclusión limitada |
| --- | --- | --- | --- | --- |
| Inicial | On | ... | ... | ... |
| Falla provocada | Off | ... | ... | ... |
| Después de revertir | On | ... | ... | ... |

**Predicción del modelo:** apagar ese servicio no cambia por sí mismo el direccionamiento ni el enlace; se espera que el eco siga respondiendo y que el nuevo acceso HTTP no se complete correctamente. Tras restaurar el servicio, se espera recuperar HTTP. Si no observas eso, registra la diferencia y revisa el estado; una predicción no sustituye la evidencia.

### C4. Alternativa si no puedes ejecutar Packet Tracer

Usa este conjunto **ficticio y simplificado**. En tu respuesta escribe “análisis de escenario; laboratorio no ejecutado”. Los eventos omiten preparación y confirmaciones; no describen todo el tráfico.

| Evento didáctico | MAC origen / destino | IP origen / destino | TCP origen / destino |
| --- | --- | --- | --- |
| Solicitud al salir de PC-A | 0200.0000.0020 / 0200.0000.0030 | 192.168.10.20 / 192.168.10.30 | 50000 / 80 |
| Misma solicitud al salir de SW1 hacia WEB-A | 0200.0000.0020 / 0200.0000.0030 | 192.168.10.20 / 192.168.10.30 | 50000 / 80 |
| Solicitud recibida por WEB-A | 0200.0000.0020 / 0200.0000.0030 | 192.168.10.20 / 192.168.10.30 | 50000 / 80 |
| Respuesta de WEB-A | 0200.0000.0030 / 0200.0000.0020 | 192.168.10.30 / 192.168.10.20 | 80 / 50000 |

Resultados ficticios del experimento:

| Estado | Enlaces | Ping | Solicitud HTTP nueva |
| --- | --- | --- | --- |
| HTTP On, inicial | Activos | 4 respuestas de 4 | Página recibida |
| HTTP Off | Activos | 4 respuestas de 4 | No se obtiene la página |
| HTTP On, restaurado | Activos | 4 respuestas de 4 | Página recibida |

Completa las tablas de evidencia con estos datos, responde las tres preguntas de C2 y explica cómo verificarías el cambio en un simulador. No declares que tú activaste, desactivaste o restauraste un servicio si solo analizaste esta tabla.

## 5. Actividad D — Redacta un incidente comprobable

Usando tus observaciones o el escenario alternativo, escribe seis apartados:

1. **Síntoma:** qué acceso falla.
2. **Alcance:** cliente, servidor y servicio involucrados.
3. **Evidencia:** compara una solicitud web nueva con la muestra de ping; identifica la modalidad.
4. **Hipótesis y prueba:** qué investigarías si no te hubieran dicho cuál fue el cambio.
5. **Cambio mínimo y reversión:** qué se modificó y cómo se recuperó el estado inicial, o qué harías en la alternativa no ejecutada.
6. **Verificación y límite:** qué repetirías y qué no puedes garantizar con esas pruebas.

Evita frases como “las capas 1 a 3 están perfectas para siempre” o “siempre que ping funciona, el error es de capa 7”. La evidencia se limita al escenario y al tráfico comprobado.

### Desafío opcional

Sin configurar nada adicional, imagina que la misma PC se conecta por Wi-Fi a la misma LAN. ¿Qué funciones inferiores cambiarían y cuáles podrían mantenerse en la conversación HTTP/TCP/IPv4? Menciona al menos una nueva comprobación que sería necesaria. No afirmes que cambiar el medio garantiza que la aplicación funcione.

## 6. Fallas de laboratorio y método de diagnóstico

Primero conserva el síntoma. Después comprueba una variable por vez y repite la misma prueba.

| Síntoma | Comprobación segura en el simulador |
| --- | --- |
| Enlace no listo | Puertos, cable, alimentación y tiempo de preparación del switch |
| Ping sin respuesta desde el inicio | IPv4 y máscara de ambos extremos; enlaces; que el destino sea WEB-A |
| Ping responde pero HTTP falla antes de provocar el error | HTTP On, URL con `http://`, IP correcta y solicitud realmente nueva |
| No aparecen eventos | Modo Simulation, filtros, solicitud nueva y avance con Capture/Forward |
| Hay TCP o ARP antes de HTTP | Reconocer preparación; continuar sin confundir esos eventos con la solicitud |
| Faltan pestañas o campos | Revisar si el evento es de origen, tránsito o recepción; usar la vista pertinente y anotar límites |
| HTTP Off pero se ve una página | Comprobar si es contenido previo; generar y observar un intento nuevo |
| HTTP no se recupera al revertir | Verificar On, direcciones y enlaces originales; volver a probar, sin añadir cambios arbitrarios |

**Verificación final:** repite el acceso que falló y el ping de control. Si no recuperas el estado, registra “reversión no verificada” y pide revisión con tus datos; no lo marques como éxito.

## 7. Rúbrica y entrega

| Criterio | Puntos |
| --- | ---: |
| A: ocho correspondencias OSI/TCP/IP correctas y entendidas | 8 |
| B: orden/anidamiento, payload y corrección del error; 2 puntos cada uno | 6 |
| C: parámetros iniciales identificados | 2 |
| C: recorrido documentado en tres puntos de solicitud y una respuesta | 4 |
| C: interpretación de campos, prueba controlada y reversión o propuesta verificable | 4 |
| D: seis apartados con evidencia y conclusiones proporcionadas | 6 |
| **Total** | **30** |

**Meta interna:** 24/30, equivalente al 80%; no es una nota oficial de aprobación de Cisco. Analizar C4 puede demostrar comprensión conceptual, pero **no acredita ejecución**. Tampoco leer las soluciones acredita dominio.

Formato sugerido para enviar tus respuestas:

```text
S003
Modalidad: observación en Packet Tracer / análisis de escenario
Versión de Packet Tracer, si se usó:
Recuerdo previo 1–3:
A1–A8:
B1–B3:
C: tablas de recorrido y comparación de estados
C2: respuestas a las tres preguntas
D: síntoma, alcance, evidencia, hipótesis/prueba, cambio/reversión, verificación/límite
Preguntas 1–8 de la lección:
Duda principal:
```

**Repaso espaciado:** mañana explica MAC/IP/puerto con tus palabras; a los siete días reconstruye el recorrido sin mirar tablas; aproximadamente al mes vuelve al incidente “ping responde, aplicación falla” y propón una prueba adicional. Si necesitas refuerzo, indica qué paso no pudiste explicar antes de avanzar.

---

## 8. Soluciones razonadas — Consultar después de intentarlo

### Recuerdo previo de la lección

1. El enrutamiento permite comunicar redes IP diferentes; lo realiza, por ejemplo, un router.
2. `40 Mb/s ÷ 8 = 5 MB/s`. Es conversión de unidades, no garantía de tasa útil.
3. No. La muestra demuestra respuestas de eco; no mide una transferencia sostenida ni la capacidad máxima.

### A. Correspondencias

| Caso | OSI | TCP/IP |
| --- | --- | --- |
| A1 | 7 — Aplicación | Aplicación |
| A2 | 6 — Presentación | Aplicación, en la correspondencia didáctica |
| A3 | 5 — Sesión | Aplicación, en la correspondencia didáctica |
| A4 | 4 — Transporte | Transporte |
| A5 | 3 — Red | Internet |
| A6 | 2 — Enlace de datos | Acceso a red / enlace |
| A7 | 1 — Física | Acceso a red / enlace |
| A8 | 3 — Red, al relacionar ICMP con OSI | Internet |

A4: los puertos TCP identifican extremos de conversaciones de transporte, no conectores de cable. A8: ICMP lleva control y diagnóstico sobre IP; no es un protocolo TCP ni UDP.

### B. Encapsulación

1. Datos HTTP, segmento TCP, paquete IPv4, trama Ethernet y señales que representan bits. El segmento contiene los datos HTTP; el paquete contiene el segmento; la trama contiene el paquete. Esta enumeración describe perspectivas anidadas, no cinco envíos independientes.
2. El segmento TCP es contenido transportado por IPv4 e incluye su propia cabecera TCP. Para TCP, los datos HTTP son su carga útil. La misma información se interpreta de forma diferente según el protocolo observado.
3. OSI no exige una cabecera independiente por cada capa. Además, ping IPv4 utiliza ICMP sobre IP, no TCP. En nuestra LAN va dentro de Ethernet, pero no por eso aparece un segmento TCP ni un puerto 80.

### C. Qué esperar y cómo interpretarlo

**Configuración:** PC-A `.20` y WEB-A `.30`, ambos con máscara `255.255.255.0`; no necesitan gateway para comunicarse localmente. HTTP está On al inicio y debe restaurarse a On al terminar. El switch no necesita una IP de administración para esta conmutación básica.

**Recorrido:** PC-A origina la solicitud; SW1 la reenvía como tráfico de capa 2; WEB-A recibe e interpreta la solicitud. En la respuesta se intercambian los papeles de origen y destino. El servidor usa su puerto de servicio como origen y el puerto del cliente como destino para esa conexión.

**Respuestas a C2:**

1. El switch de capa 2 de esta topología no cambia las IP origen/destino al reenviar la solicitud. No hay aquí enrutamiento ni NAT.
2. No necesita interpretar el texto HTTP para conmutar la trama. Puede verse una etiqueta HTTP en el simulador sin que esa sea la información usada por su función de conmutación.
3. Solicitud y respuesta de la misma conexión invierten origen/destino de IP y puertos TCP; en esta LAN también se invierten las MAC de los extremos. El puerto de cliente observado puede diferir de `50000`.

**Falla y reversión:** si apagar solo HTTP deja funcionando el eco y rompe el acceso web nuevo, la comparación apoya que ese cambio afectó al servicio probado sin eliminar la respuesta ICMP. Restaurar On y recuperar la página fortalece la explicación. No prueba ausencia de problemas ajenos al experimento.

**No hay una salida real obligatoria para copiar.** La primera prueba puede incluir preparación, los campos visibles varían y la cantidad de eventos no es fija. Una divergencia debe documentarse y diagnosticarse, no sustituirse por la tabla ficticia.

### D. Informe de ejemplo — Datos ficticios, no ejecutados

1. **Síntoma:** PC-A no obtiene una página nueva de WEB-A cuando HTTP está Off.
2. **Alcance:** una PC, un servidor y su servicio HTTP en una LAN aislada; no hay evidencia sobre otros servicios.
3. **Evidencia:** en el escenario, el eco devuelve 4/4 respuestas tanto antes como después del cambio; la página solo se recibe con HTTP On.
4. **Hipótesis/prueba:** si desconociera el cambio, comprobaría la disponibilidad del servicio y el intento TCP/HTTP en el simulador, conservando direcciones y enlaces.
5. **Cambio/reversión:** la intervención propuesta es restaurar HTTP a On, su estado inicial. No se requiere modificar cables ni direcciones.
6. **Verificación/límite:** repetiría una solicitud nueva y el ping. El conjunto ficticio indica recuperación; eso no equivale a que yo haya ejecutado el laboratorio ni certifica toda la red.

Son válidas otras redacciones que separen evidencia, hipótesis y prueba. “El ping funciona” por sí solo no identifica la causa del problema HTTP.

### Desafío opcional

El acceso al medio pasaría a utilizar funciones de enlace inalámbrico y radio, relacionadas con OSI 2/1. Podrían mantenerse las funciones de HTTP, TCP e IPv4 de los extremos. Haría falta comprobar, por ejemplo, asociación y seguridad Wi-Fi además de la conectividad IP. La realización concreta depende del diseño; no todas las tramas seguirían siendo Ethernet en todos los tramos.

### Preguntas de comprensión de la lección

1. 1 Física/Physical; 2 Enlace de datos/Data Link; 3 Red/Network; 4 Transporte/Transport; 5 Sesión/Session; 6 Presentación/Presentation; 7 Aplicación/Application.
2. Con Aplicación en nuestro modelo TCP/IP de cuatro capas. Es una relación funcional, no una correspondencia exacta de cabeceras.
3. El paquete IPv4 contiene el segmento TCP; la trama Ethernet contiene el paquete IPv4, además de su propia información de enlace.
4. El puerto TCP 80 es lógico y corresponde al servicio del ejemplo; FastEthernet0/1 identifica una interfaz del switch.
5. La dirección MAC de destino, junto con la información de conmutación pertinente del dispositivo. El aprendizaje y las decisiones de reenvío se estudiarán después.
6. No. Los mensajes de eco utilizan ICMP sobre IP. Que ping sea un programa no lo convierte en una solicitud HTTP o una conexión TCP.
7. Hipótesis: el servicio HTTP está desactivado. Prueba: revisar su estado en WEB-A simulado, documentarlo, restaurar el estado previsto si corresponde y repetir HTTP y ping. También son válidas hipótesis alternativas con pruebas diferenciadoras; ping no elige la causa por ti.
8. **IP addressing is associated with the OSI Network layer, Layer 3. IP uses logical addresses to identify the source and destination.**

## 9. Referencias y uso del material

Consultadas el **16 de octubre de 2026**. Las actividades y los datos son originales del curso, no un banco oficial de preguntas.

1. [Cisco: temario CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf), objetivos 1.6 y 2.4 como relación curricular.
2. [RFC Editor / IETF: RFC 1122](https://www.rfc-editor.org/rfc/rfc1122.html#section-1.1.3), arquitectura por capas e ICMP.
3. [Cisco Networking Academy: Investigate the TCP/IP and OSI Models in Action](https://contenthub.netacad.com/courses/itn-dl/_common/3.5.5-packet-tracer---investigate-the-tcp-ip-and-osi-models-in-action.pdf), referencia de controles y vistas de Simulation. Se contrastaron sus instrucciones indexadas; la descarga directa del PDF devolvió un error temporal. Esta práctica es autocontenida y no requiere descargarlo.
4. Para instalar el simulador, usa el recurso oficial indicado en el plan: [introducción a Cisco Packet Tracer](https://www.netacad.com/courses/getting-started-cisco-packet-tracer/1000).

---

Fin de la práctica S003.
