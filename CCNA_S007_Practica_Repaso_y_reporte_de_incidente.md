# CCNA S007 — Práctica: repaso y reporte de un incidente

**Fecha:** 19 de octubre de 2026.  
**Lección asociada:** `CCNA_S007_Leccion_Explicar_una_comunicacion_y_un_incidente.md`.  
**Tiempo total de S007:** 30 minutos.  
**Referencias v2.0:** fundamentos de apoyo a 1.6 y 2.4.  
**Proyecto ficticio:** Campus Aurora.

## 1. Preparación y orden del trabajo

Este es un repaso de S001–S006, no una certificación de que las dominas. La actividad principal consiste en analizar evidencia suministrada y redactar una explicación. No necesitas reconstruir S006 para completar los 30 minutos.

| Orden | Trabajo | Tiempo | Objetivos |
| --- | --- | ---: | --- |
| A | Primer intento de Q1–Q8 de la lección, sin consultar respuestas | 6 min | O1–O4 |
| Lectura | Síntesis de la lección S007 | 7 min | O1–O5 |
| B | Reconstruir una comunicación y analizar el incidente | 12 min | O1, O3, O4, O5 |
| C | Inglés breve, corrección y bitácora | 5 min | O4, O5 |

**Prerrequisitos:** distinguir PC/switch, Ethernet/IP, consulta/configuración y modos básicos de IOS. Puedes usar los apuntes durante B, pero indica qué consultaste. Si falta una base, anótala para un refuerzo específico.

**Modalidad principal:** análisis didáctico en Markdown. Todos los datos del caso son ficticios; el profesor no ejecutó este laboratorio al redactarlo. No escribas “ejecuté” cuando solo analizaste las tablas.

**Comprobación opcional:** Packet Tracer de escritorio, dos PC-PT y un 2960 en una LAN de laboratorio equivalente a S006. Anota tu versión desde Help → About y el modelo usado. No se ha validado empíricamente una versión concreta. El simulador no implementa todo IOS ni sustituye Windows/macOS/Linux. No se necesita IOS XE/CML para S007; funciones futuras que lo requieran se practicarán en entornos autorizados. La ampliación opcional del apartado 6 requiere unos 5–10 minutos adicionales si la LAN ya está preparada.

## 2. Actividad A — Recuperar antes de releer

Responde Q1–Q8 de la sección 2 de la lección. No es un examen oficial: son preguntas originales para detectar qué debes repasar.

**English instruction 1: Answer from memory before checking your notes.** Intenta primero responder de memoria; después podrás revisar. Conserva el primer intento y marca “no lo recuerdo” cuando corresponda.

**Evidencia A:** ocho respuestas iniciales. No necesitas grabarte ni resolver nada fuera del alcance de S001–S006. A continuación dedica siete minutos a la síntesis de la lección y pasa al caso.

## 3. Caso de Campus Aurora: topología y evidencia

**Scenario — English reading:**

Two training computers could communicate earlier. PC-A now receives no replies from PC-B, but the switch console is still available. Use the evidence to explain the incident and verify the proposed repair.

**Apoyo en español:** antes funcionaba la comunicación entre las dos PCs; ahora fallan las respuestas hacia PC-B. La consola del switch sigue disponible. Debes delimitar la falla y justificar qué comprobarías y cómo cerrarías el incidente.

### Topología textual de referencia

| Enlace | Extremo 1 | Extremo 2 | Medio y función |
| --- | --- | --- | --- |
| L1 | PC-A FastEthernet0 | SW1 FastEthernet0/1 | Copper Straight-Through, datos |
| L2 | PC-B FastEthernet0 | SW1 FastEthernet0/2 | Copper Straight-Through, datos |
| C1 | PC-A RS-232 | SW1 Console | Cable Console, administración |

### Direccionamiento esperado

| Equipo | Interfaz | IPv4 | Máscara | Gateway/DNS |
| --- | --- | --- | --- | --- |
| PC-A | FastEthernet0 | 192.168.10.10 | 255.255.255.0 | Sin configurar |
| PC-B | FastEthernet0 | 192.168.10.20 | 255.255.255.0 | Sin configurar |
| SW1 | Puertos de capa 2 | Sin IP | No corresponde | No corresponde |

No hay router ni acceso a Internet en este escenario. Las PCs usan la misma subred y el mismo segmento de capa 2. No se pide crear VLAN ni calcular otras máscaras.

### E1. Estado anterior documentado — datos ficticios

- L1 y L2 estaban activos.
- PC-A hacia `192.168.10.20`: cuatro respuestas de cuatro solicitudes.
- PC-B hacia `192.168.10.10`: cuatro respuestas de cuatro solicitudes.
- La consola estaba disponible. Ambas PCs tenían los valores de la tabla.

### E2. Estado durante la falla — datos ficticios

Las consultas `ipconfig` de ambas PCs siguen coincidiendo con el direccionamiento esperado. Gateway y DNS permanecen sin configurar.

Desde **Command Prompt de PC-A**, este es el resumen didáctico de `ping 192.168.10.20`:

```text
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

Desde **EXEC privilegiado de SW1**, este es un fragmento didáctico de `show ip interface brief`:

```text
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/1         unassigned      YES unset  up                    up
FastEthernet0/2         unassigned      YES unset  down                  down
```

Además, `arp -a` en PC-A todavía muestra una asociación de `192.168.10.20` con la MAC ficticia `02:00:00:00:00:20`, aprendida cuando la red funcionaba. La consola C1 responde y PC-B está encendida.

Un compañero propone: “Como hay una entrada ARP, el cable debe estar bien. Reiniciemos todo el switch”. Evalúa esa propuesta usando E1 y E2.

## 4. Actividad B — Explicar, diagnosticar y reportar

### B1. Comunicación normal

En cuatro pasos, explica cómo debería ir un ping de PC-A a PC-B y regresar. Incluye decisión de destino local, ARP cuando hace falta, trama Ethernet/switch y respuesta ICMP. Di si interviene TCP o UDP. No necesitas describir campos todavía no estudiados.

**Evidencia B1:** una explicación corta del recorrido, no solo una lista de siglas.

### B2. Hipótesis a partir de E1 y E2

**English instruction 2: Separate the evidence from your hypothesis.** Separa lo observado de lo que aún propones como explicación.

Escribe:

1. Dos observaciones concretas de E2 y qué parte de la comunicación delimitan.
2. Una hipótesis compatible con ellas y una comprobación que permita confirmarla o descartarla.
3. Por qué la entrada ARP no demuestra que el enlace siga funcionando y por qué reiniciar todo no es el siguiente paso justificado.

Detente antes de leer E3 y conserva tu respuesta. `down/down` por sí solo no identifica un componente averiado específico.

### E3. Resultado posterior de inspección — léelo después de B2

En el caso ficticio, se inspecciona el tramo L2 y se confirma que falta su cable. PC-B sigue encendida. No se dispone de evidencia sobre quién retiró el cable ni por qué. Se reconecta **PC-B FastEthernet0 con SW1 FastEthernet0/2** mediante Copper Straight-Through.

### E4. Verificación posterior suministrada

Después de que el enlace se estabiliza, Fa0/2 vuelve a `up/up`. No se cambió ninguna IPv4, máscara ni configuración de IOS.

| Prueba posterior | Resultado didáctico |
| --- | --- |
| PC-A: `ping 192.168.10.20` | 4/4 respuestas del destino |
| PC-B: `ping 192.168.10.10` | 4/4 respuestas del destino |
| Consola C1 | Disponible |

### B3. Reporte del incidente

**English instruction 3: Verify the result before closing the incident.** Antes de cerrar, contrasta la reparación con E4; una acción realizada no sustituye a su verificación.

Redacta un reporte de seis a ocho líneas utilizando E1–E4:

| Campo | Contenido que debes completar |
| --- | --- |
| Síntoma y alcance | Origen/destino que fallaban y acceso que seguía funcionando |
| Evidencia | Resultado de ping, estado de interfaces y configuración del host pertinente |
| Hipótesis y causa comprobada | Lo que propusiste con E2 y lo que confirmó E3 |
| Cambio mínimo y reversión | Cómo se recuperó el enlace original; qué no se necesitó cambiar |
| Verificación | La misma prueba fallida y la prueba de control en sentido inverso |
| Límite de la conclusión | Qué servicios no se probaron y qué dato sobre el origen del incidente sigue desconocido |

Encabeza el reporte con **“Análisis de evidencia didáctica”**. Puedes cerrar el caso suministrado según E4, pero eso no certifica que ejecutaste una reparación ni que tu propia LAN funcione.

**Desafío opcional, sin puntos:** si una prueba posterior distinta envía 20 solicitudes y recibe 18 respuestas, calcula el porcentaje de pérdida y escribe una conclusión prudente. ¿Sería correcto decir “sin pérdidas”?

## 5. Actividad C — Inglés, corrección y bitácora

Escribe **dos oraciones en inglés** que resuman la causa y la verificación del caso. Usa una estructura de apoyo:

- `The supplied evidence shows ...`
- `After ..., the recorded tests ...`

La respuesta modelo está al final. No necesitas traducir el reporte completo. Se corregirá por separado el razonamiento de redes y la comprensión del inglés; una falta gramatical que no altere la idea no cuenta como error técnico.

Consulta ahora las soluciones y compara con tu primer intento. No sustituyas una respuesta equivocada como si siempre hubiese sido correcta. Completa una fila:

| Pregunta o actividad | Mi respuesta inicial | Corrección y motivo | Tipo: redes/inglés/ambos | Próximo intento |
| --- | --- | --- | --- | --- |
| Por completar | Por completar | Por completar | Por completar | Día siguiente / siete días |

Si no detectaste un error, elige una idea cuya explicación quieras comprobar con otro ejemplo. Si todavía no has respondido, deja la fila y la puntuación pendientes.

**Entrega:** Q1–Q8, B1, B2, reporte B3, dos oraciones en inglés y una fila de bitácora. Indica si consultaste notas y si hubo ejecución adicional en Packet Tracer. No se requieren imágenes, audio ni archivos de simulador.

## 6. Comprobación opcional en Packet Tracer

Esta ampliación utiliza únicamente una LAN de laboratorio ya preparada, equivalente a la topología anterior. No reemplaza la actividad de análisis ni es necesaria para completar sus 30 minutos.

1. Anota versión de Packet Tracer, modelo, cableado y direcciones actuales. Comprueba que coinciden con el plan. Si aún no construiste la LAN, vuelve a S006; no marques esta ampliación como realizada.
2. En Command Prompt de cada PC ejecuta `ipconfig` y confirma dirección/máscara. Haz ping al otro host desde ambas PCs para obtener una referencia correcta. Si falla, diagnostica antes de introducir otro cambio.
3. Abre Terminal de PC-A hacia SW1 con los parámetros de S005: 9600, 8 bits, None, 1 bit de parada, None. Desde `Switch>` usa `enable`; desde `Switch#` consulta:

   ```text
   show ip interface brief
   ```

4. Retira únicamente el cable **L2** del escenario simulado. Conserva PC-B y el switch. Repite desde PC-A:

   ```text
   ping 192.168.10.20
   ```

5. Consulta otra vez las interfaces desde IOS. Registra tus salidas reales, que pueden diferir en formato de E2. Una entrada ARP podría seguir presente o haber caducado; ninguna de las dos posibilidades sustituye la comprobación del enlace.
6. **Reversión obligatoria:** reconecta L2 exactamente entre PC-B FastEthernet0 y SW1 FastEthernet0/2. Espera que se estabilice; verifica Fa0/2 y repite A→B. Como control, desde PC-B ejecuta:

   ```text
   ping 192.168.10.10
   ```

7. Finaliza solo cuando el cable, las direcciones y las dos pruebas hayan regresado al estado de referencia, o registra explícitamente qué falta. No guardes una falla pendiente sobre un laboratorio anterior. No necesitas modificar configuración de IOS ni reiniciar equipos.

Identifica estas notas como **“Observación propia en simulador”**, separadas de E1–E4. No copies los resultados ficticios si tus pruebas muestran otra cosa.

### Diagnóstico y cierre

| Problema | Comprobación concreta | Acción o límite |
| --- | --- | --- |
| Ping falla | Destino escrito, `ipconfig` y enlace del host | No asignar una causa solo por un timeout |
| Fa0/2 sin enlace | Cable/puertos correctos, PC encendida y estado de la interfaz | Contrastar una causa física antes de cambiar direccionamiento |
| Consulta rechazada | Prompt y herramienta utilizada | `ipconfig` en Command Prompt; consultas IOS en Terminal del switch |
| Una salida o comando difiere | Modelo, versión y ayuda contextual | Documentar el límite del simulador; no fabricar la salida esperada |
| Solo falla el primer intento | Estabilización y una segunda prueba completa | Conservar ambas pruebas; investigar pérdidas persistentes |
| El reporte dice “Internet recuperado” | Qué destinos y servicios se probaron | Limitar la conclusión al alcance demostrado |

En la modalidad de análisis no se modifica ningún equipo: la reversión física solo se propone y se interpreta mediante E3–E4. En la ampliación, la desconexión introducida debe quedar revertida y verificada en el laboratorio.

## 7. Rúbrica y uso de resultados

| Criterio | Puntos | Cómo se asignan |
| --- | ---: | --- |
| Q1–Q8 | 16 | Dos por pregunta: respuesta correcta y explicación; desgloses en soluciones |
| B1: recorrido | 4 | Destino local, ARP/Ethernet, switch y respuesta ICMP: uno por elemento correcto |
| B2: diagnóstico | 3 | Observaciones y límite de ARP (1); hipótesis comprobable (1); comprobación pertinente antes de reiniciar (1) |
| B3: reporte | 5 | Síntoma/alcance, evidencia, causa sustentada, restauración y verificación con límites: uno por cada conjunto |
| C: explicación técnica en inglés | 2 | Causa y verificación correctas, identificadas como datos suministrados; gramática comentada aparte |
| **Total** | **30** | **24/30 = 80%, umbral interno del curso** |

La bitácora sirve para decidir el siguiente repaso, no para fabricar una nota. El desafío y la ampliación opcional no agregan puntos.

- 24–30: conservar repaso espaciado y comprobar que puedes justificar las respuestas.
- 18–23: reforzar los conceptos fallados y repetir un caso equivalente.
- 0–17: volver al prerrequisito concreto antes de añadir dificultad.

Estos rangos son criterios docentes, no notas oficiales de aprobación de Cisco. Una autoevaluación es provisional; envía tus respuestas para corrección. Puntuar un análisis no acredita ejecución del laboratorio. Sin respuestas, estudio, práctica realizada y dominio permanecen sin verificar.

## 8. Fuentes oficiales

Consultadas el **19/10/2026**. El objetivo sigue siendo CCNA v2.0, anunciado desde el 03/02/2027, mientras v1.1 está disponible hasta el 02/02/2027; la lección enlaza las fuentes oficiales del examen.

- [Cisco: funcionamiento de ping](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13730-ext-ping-trace.html).
- [Packet Tracer: herramientas de las PCs y acceso por Terminal](https://tutorials.ptnetacad.net/help/default/config_PCs.htm).
- [Packet Tracer: conexiones e indicadores de enlace](https://tutorials.ptnetacad.net/help/default/connectionsLinks.htm).
- [Cisco: modos y ayuda de IOS del Catalyst 2960](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swcli.html).

---

## 9. Soluciones razonadas — después del intento

### Q1–Q8

1. **Q1:** sí. Una LAN es una red local y puede existir sin conexión a otras redes. Para el ping IPv4 del laboratorio se necesitan enlaces y direccionamiento adecuados, pero no Internet. Un punto por la respuesta y otro por distinguir LAN de Internet.
2. **Q2 — Traducción:** “Un archivo de 40 MB atraviesa un enlace de 80 Mb/s. Ignorando sobrecarga, ¿cuál es el tiempo mínimo de transferencia? ¿Eso determina la latencia?”. **Respuesta:** 40 × 8 = 320 Mb; 320 ÷ 80 = **4 segundos**. No determina la latencia de un paquete: esta depende de demoras del recorrido y del procesamiento, entre otros factores. Un punto por conversión/cálculo y otro por distinguir tiempo de transferencia y latencia.
3. **Q3:** Ethernet corresponde a enlace/capa 2, IP a red/capa 3 y TCP/UDP a transporte/capa 4. Ping utiliza ICMP, transportado sobre IP; no necesita TCP ni UDP. Un punto por las asociaciones y otro por ICMP.
4. **Q4:** ARP permite averiguar la MAC correspondiente a una IPv4 del enlace local cuando hace falta. La PC guarda una asociación IPv4–MAC, no una lista de puertos del switch. Un punto por la función y otro por la asociación correcta.
5. **Q5 — Traducción:** “Estás en `Switch(config-if)#`. ¿Cómo cambian el prompt `exit` y `end`? ¿Guardan la configuración?”. **Respuesta:** `exit` lleva a `Switch(config)#`; `end`, a `Switch#`. Ninguno guarda la configuración activa en la de arranque ni deshace cambios. Un punto por ambos destinos y otro por no confundir navegación con guardado.
6. **Q6:** `ipconfig` se ejecuta en Desktop → Command Prompt de la PC y consulta ese host. `show ip interface brief` se ejecuta en IOS de SW1, accedido mediante Desktop → Terminal de PC-A, desde EXEC privilegiado. Un punto por cada contexto correcto.
7. **Q7:** las cuatro solicitudes obtuvieron respuestas ICMP del destino en esa prueba. No se puede afirmar que HTTP funcione, porque no se pidió una página ni se comprobó ese servicio. Un punto por una conclusión delimitada y otro por el límite de aplicación.
8. **Q8 — Traducción:** “PC-B no tiene enlace Ethernet, pero la consola sigue funcionando. ¿Demuestra eso que falló todo el switch? Nombra una comprobación antes de cambiar ajustes”. **Respuesta:** no. La consola y el enlace Ethernet son accesos distintos; hay que comprobar el tramo de PC-B, por ejemplo cable, puertos de ambos extremos y encendido. Un punto por evitar la conclusión global y otro por una prueba pertinente.

### B1. Explicación del recorrido

Una respuesta válida es: PC-A identifica `192.168.10.20` como destino local usando `/24`. Si le falta la asociación, ARP resuelve la MAC de PC-B y permite formar la trama Ethernet que lleva el paquete IP con ICMP. El switch reenvía la trama en la LAN. PC-B devuelve ICMP Echo Reply y PC-A registra la respuesta. TCP y UDP no intervienen en este ping.

### B2. Hipótesis y evidencia

E2 muestra, por ejemplo, Fa0/2 `down/down` y las direcciones correctas, junto con consola disponible. Esto concentra la investigación en el acceso de PC-B; no demuestra una caída general ni identifica todavía un cable dañado.

Una hipótesis razonable es que L2 esté desconectado. Inspeccionar sus dos extremos permite comprobarla. También pueden considerarse otras causas compatibles con la evidencia; la hipótesis debe cambiar si la inspección la contradice. PC-B apagada no encaja con el dato explícito de E2.

La entrada ARP procede del estado anterior y puede permanecer en caché; no mide el enlace en tiempo real. Reiniciar el switch no es el cambio mínimo justificado por los datos. E3 confirma la ausencia del cable; no confirma quién lo retiró ni un defecto interno del cable o switch.

### B3. Modelo de reporte

**Análisis de evidencia didáctica.** PC-A dejó de recibir respuestas ICMP de PC-B; la consola de SW1 permaneció disponible. E2 muestra cuatro solicitudes sin respuesta, Fa0/2 sin enlace y direcciones de ambas PCs conformes al plan. La hipótesis de L2 desconectado fue comprobada por la inspección de E3, que encontró el cable ausente. Se recuperó el enlace original PC-B FastEthernet0–SW1 FastEthernet0/2, sin cambiar direccionamiento ni configuración de IOS. E4 documenta Fa0/2 activo, cuatro respuestas de cuatro al repetir A→B y el mismo resultado en B→A. Se considera recuperada la comunicación ICMP del caso. No se probaron HTTP, DNS ni Internet y se desconoce quién retiró el cable.

La reversión del cambio de prueba consiste en restaurar el enlace original, no en usar `end`. Si E4 no existiera o fallara, el cierre quedaría pendiente.

### C. Inglés y bitácora

**Respuesta modelo:** “The supplied evidence shows a disconnected cable to PC-B. After the cable was reconnected, the recorded tests received replies in both directions.”

La frase describe el expediente suministrado. Solo una ejecución propia permite cambiarla por “I tested…” y añadir resultados observados por ti.

**Ejemplo de bitácora, no atribuido al estudiante:** “Creía que una entrada ARP demostraba enlace activo; corregí esa idea porque puede conservarse tras una desconexión. Mañana explicaré qué otra evidencia necesito”. Debes registrar tu propio error o duda, no copiar este como experiencia personal.

### Desafío y comprobación opcional

La pérdida es (20 − 18) ÷ 20 × 100 = **10%**. Hubo respuestas, pero no una prueba sin pérdidas. Hay que conservar ese resultado e investigar si la pérdida persiste; no declarar recuperación completa por ignorar los dos intentos sin respuesta.

En Packet Tracer se espera que retirar L2 afecte el enlace de PC-B mientras la consola siga accesible. Tras reconectarlo y estabilizarse, las pruebas deben volver al estado base. Los valores y mensajes concretos deben salir de tu simulación. Si no la ejecutaste, este resultado continúa siendo una expectativa, no evidencia propia.

---

**Estado:** material preparado; respuestas, estudio confirmado, ejecución opcional y dominio técnico o lingüístico pendientes de evidencia.

Fin de la práctica S007.
