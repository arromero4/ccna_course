# CCNA S006 — Práctica: construir, verificar y reparar una LAN

**Fecha:** 18 de octubre de 2026.  
**Lección asociada:** `CCNA_S006_Leccion_Construir_una_LAN_de_dos_equipos.md`.  
**Tiempo:** 70 minutos, más los 20 de lección; instalación del simulador aparte.  
**Objetivo curricular:** fundamentos de apoyo a **1.6 y 2.4 de CCNA v2.0**.  
**Proyecto ficticio:** Campus Aurora.

## 1. Plataforma, alcance y registro de evidencia

Utiliza **Cisco Packet Tracer de escritorio**, dos dispositivos **PC-PT** y un switch **2960 nuevo**, en el espacio **Logical**. Anota la versión instalada desde **Help → About** o su equivalente. Esta guía utiliza funciones básicas; no se afirma haber ejecutado o validado empíricamente una versión concreta.

Trabaja en un escenario nuevo y aislado. Necesitarás dos cables Copper Straight-Through y un cable Console. El hostname del switch nuevo suele ser `Switch`; **SW1** será su etiqueta en el plano. No es necesario cambiar su configuración de IOS.

Los cambios se limitan al laboratorio: conexiones de los dispositivos nuevos y direccionamiento de las PCs. No se configuran VLAN, enrutamiento ni servicios. Packet Tracer simula una parte de IOS y de los hosts; sus comandos, mensajes y tiempos pueden diferir de equipos físicos. No sustituye un sistema Windows completo. S006 no requiere IOS XE/CML; cuando una función posterior lo necesite, se indicará un entorno autorizado con imágenes oficiales.

**El profesor no ejecutó este laboratorio al redactarlo.** Identifica tus respuestas como:

- **Predicción:** lo que esperas antes de una prueba.
- **Observación en Packet Tracer:** paso o comando que tú ejecutaste y su resultado.
- **Análisis didáctico:** interpretación de los datos ficticios del apartado 8.

Si no tienes acceso al simulador, realiza el análisis alternativo; eso no contará como ejecución confirmada.

| Actividad | Tiempo | Objetivos de la lección |
| --- | ---: | --- |
| A. Recuperación y preparación | 5 min | O1, O3 |
| B. Construcción y direcciones | 20 min | O1, O2 |
| C. Pruebas y observación | 20 min | O3, O4 |
| D. Dos fallas con reversión | 15 min | O5, O6 |
| E. Informe, comprobación e inglés | 10 min | O3, O5, O6 |

## 2. Actividad A — Preparación

Responde R1–R3 de la lección sin consultarla. Anota también la versión de Packet Tracer, los modelos elegidos y si trabajas con observación o con datos didácticos.

**Scenario — English reading:**

Campus Aurora needs two training computers on the same local network. Build the LAN and verify communication in both directions. Then investigate two controlled faults and restore the working configuration.

**Apoyo en español:** conectarás dos puestos de capacitación, comprobarás que se comunican y analizarás dos fallas introducidas por separado. Debes devolver el laboratorio a su estado correcto después de cada una.

Antes de conectar nada, predice: ¿necesitas Internet para lograr el objetivo? ¿Qué evidencia necesitarías además de ver enlaces verdes? Compara tus ideas con los resultados al cerrar la actividad.

## 3. Actividad B — Construcción y direccionamiento

### B1. Construye y documenta la topología

Coloca dos PC-PT y un 2960 nuevo. Identifica las PCs como PC-A y PC-B y el switch como SW1 mediante sus etiquetas gráficas o notas. Cambiar una etiqueta del plano no equivale a ejecutar `hostname` en IOS.

La topología consta de estos enlaces:

| Enlace | Extremo 1 | Extremo 2 | Cable | Función |
| --- | --- | --- | --- | --- |
| L1 | PC-A FastEthernet0 | SW1 FastEthernet0/1 | Copper Straight-Through | Datos |
| L2 | PC-B FastEthernet0 | SW1 FastEthernet0/2 | Copper Straight-Through | Datos |
| C1 | PC-A RS-232 | SW1 Console | Console | Administración |

**English instruction 1: Connect each PC to the correct switch port.** Conecta cada PC al puerto indicado usando **Connections → Copper Straight-Through**. Después añade el cable Console entre PC-A RS-232 y SW1 Console.

1. Trabaja en **Realtime**. Permite que el switch termine de arrancar y sus puertos estabilicen el enlace. El color inicial puede cambiar; no deshabilites protocolos para acelerar el ejercicio.
2. En PC-A abre **Desktop → Terminal**. Usa 9600 bits/s, 8 bits de datos, paridad None, 1 bit de parada y control de flujo None, como en S005.
3. Pulsa Enter hasta obtener el prompt. Si aparece el diálogo inicial de configuración, responde `no`. El dispositivo debe ser uno nuevo de laboratorio.
4. Desde `Switch>`, introduce una orden a la vez:

   ```text
   enable
   show version
   show interfaces status
   show ip interface brief
   ```

5. Registra el modelo/software simulado y el estado de FastEthernet0/1 y FastEthernet0/2. Si una consulta no está implementada, usa `show interfaces ?` para explorar y conserva la evidencia de `show ip interface brief`; anota la limitación, sin inventar resultados.

Cuando una salida se detenga en `--More--`, avanza con Espacio o Enter y espera al prompt antes de escribir la siguiente orden. No introduzcas comandos de configuración de interfaces.

**Evidencia B1:** los tres enlaces de la tabla, versión del simulador, versión/modelo simulado, prompt de consulta y estados de los dos puertos. Los puertos Ethernet de capa 2 pueden aparecer `unassigned` y estar activos.

### B2. Configura las PCs según el plan

Registra primero el estado inicial de ambas PCs en **Desktop → IP Configuration**. Después selecciona **Static** y utiliza:

| Equipo | Interfaz | IPv4 | Máscara | Default Gateway | DNS Server |
| --- | --- | --- | --- | --- | --- |
| PC-A | FastEthernet0 | `192.168.10.10` | `255.255.255.0` | Sin configurar | Sin configurar |
| PC-B | FastEthernet0 | `192.168.10.20` | `255.255.255.0` | Sin configurar | Sin configurar |
| SW1 | Puertos Ethernet de capa 2 | Sin IP | No corresponde | No corresponde | No corresponde |

Si gateway o DNS muestran `0.0.0.0`, registra ese valor como no configurado en este ejercicio. Revisa la máscara aunque la interfaz la haya completado automáticamente.

En **Desktop → Command Prompt** de cada PC ejecuta:

```text
ipconfig
```

No escribas este comando dentro de Terminal/IOS. Copia la IPv4 y máscara de la interfaz conectada y compáralas con la tabla. Explica por qué las dos direcciones son distintas y pertenecen a la misma subred con esta máscara.

**Evidencia B2:** valores iniciales, configuración aplicada y consulta de ambas PCs. Solo utiliza la máscara dada; aún no se pide calcular otras subredes.

## 4. Actividad C — Verificar y observar la comunicación

### C1. Establece el estado de referencia

Antes del primer ping, consulta en Command Prompt de PC-A:

```text
arp -a
```

Anota si ya existe una asociación para PC-B. Una tabla vacía antes de generar tráfico puede ser normal. No es necesario forzar que esté vacía.

**English instruction 2: Verify connectivity in both directions.** Comprueba la comunicación desde cada PC hacia la otra; no realices estas pruebas desde IOS del switch.

En **PC-A**:

```text
ping 192.168.10.20
```

En **PC-B**:

```text
ping 192.168.10.10
```

Espera que termine cada prueba. Si el primer intento tiene pérdidas, conserva ese resultado y repite una vez después de estabilizar los enlaces. Si la pérdida continúa, usa el diagnóstico del apartado 7 antes de introducir fallas.

Completa una tabla con tus resultados:

| Prueba | Origen | Destino | Enviados | Respuestas del destino | Pérdidas/mensaje | Conclusión |
| --- | --- | --- | ---: | ---: | --- | --- |
| Base A→B | PC-A | 192.168.10.20 | Por observar | Por observar | Por observar | Por redactar |
| Base B→A | PC-B | 192.168.10.10 | Por observar | Por observar | Por observar | Por redactar |

El criterio de aceptación de este escenario estable es recibir respuesta a todas las solicitudes de una prueba completa en cada sentido: normalmente cuatro de cuatro en la PC simulada. Si tu versión usa otra cantidad, registra el número real. Un mensaje de destino inaccesible no cuenta como una respuesta Echo Reply exitosa del destinatario.

**Evidencia C1:** comando, origen, destino, resumen y una conclusión limitada. Solo cuando la red base funcione pasarás a las fallas controladas.

### C2. Relaciona ARP, ICMP y Ethernet

Después del ping, ejecuta en PC-A:

```text
arp -a
```

1. Busca la asociación de `192.168.10.20` y anota su MAC si aparece. Compárala con el estado anterior. No copies una MAC de los ejemplos como si fuera la de tu dispositivo.
2. Cambia a **Simulation**. En **Edit Filters** selecciona ARP e ICMP para facilitar la lectura; los filtros de visualización no son reglas que bloqueen la red.
3. Desde Command Prompt de PC-A inicia otro `ping 192.168.10.20`. Usa **Capture/Forward** para avanzar eventos. Abre un evento ICMP y distingue una solicitud de una respuesta mediante la información de la PDU.
4. Registra un origen y destino IPv4 de una solicitud y de su respuesta. Describe qué dispositivos atraviesa el mensaje. Si hay eventos ARP, identifica para qué sirven. Si no aparecen, considera que las PCs ya pueden tener la asociación en caché; no inventes un evento ARP.
5. Vuelve a **Realtime** y deja terminar el ping antes de continuar. El modo paso a paso puede dejar eventos pendientes; no confundirlo con una avería.

**Evidencia C2:** asociación ARP observada o ausencia documentada, una solicitud y una respuesta ICMP, y explicación del papel del switch. No se pide todavía reconstruir toda la tabla MAC ni interpretar cada campo de la PDU.

## 5. Actividad D — Dos fallas separadas

Conserva el estado correcto de B2 y C1 como **baseline**, referencia para reparar. Antes de cada incidente predice el efecto. No introduzcas el segundo hasta restaurar y verificar el primero.

### D1. Falta el enlace Ethernet de PC-B

1. Identifica **L2** en la tabla y elimina únicamente ese cable del escenario nuevo usando la herramienta de borrado. No borres PC-B, el switch ni el cable de consola.
2. Desde PC-A ejecuta `ping 192.168.10.20`. Registra la salida y consulta por consola `show interfaces status` y/o `show ip interface brief`.
3. Compara el puerto hacia PC-B con el puerto hacia PC-A. Comprueba también que la consola de PC-A sigue respondiendo. ¿Qué observación permite separar la administración serial del tráfico de las PCs?
4. Formula una hipótesis a partir del enlace y las consultas; no te limites a escribir “ping falla”.
5. **Reversión:** vuelve a conectar PC-B FastEthernet0 con SW1 FastEthernet0/2 usando Copper Straight-Through. Espera que el enlace se estabilice.
6. Repite el mismo ping de PC-A y la prueba de control de PC-B hacia `192.168.10.10`. Verifica el puerto restaurado.

**Evidencia D1:** antes/falla/después, estado del puerto, cambio realizado, prueba repetida y prueba de control. Una tabla ARP antigua puede seguir presente durante la desconexión y no invalida la evidencia de enlace caído.

### D2. Dirección de PC-B fuera del plan

Con los enlaces restaurados y las dos pruebas base correctas:

1. Anota de nuevo la dirección actual de PC-B. En **Desktop → IP Configuration**, cambia **solo** su IPv4 de `192.168.10.20` a `192.168.20.20`. Conserva `255.255.255.0` y deja gateway/DNS sin configurar.
2. Desde **PC-B**, ejecuta `ipconfig` y después `ping 192.168.10.10`. Registra el resultado: esta será la prueba que repetirás tras reparar.
3. Desde PC-A consulta su `ipconfig` como control y desde IOS verifica que los dos enlaces siguen activos. No añadas un router ni cambies la IP de PC-A para adaptar la red al error.
4. Explica por qué compartir switch no basta para que estas dos direcciones en subredes distintas se comuniquen mediante el escenario propuesto.
5. **English instruction 3: Restore the original address and repeat the failed test.** Restaura en PC-B la IPv4 **`192.168.10.20`**, comprueba la máscara y ejecuta `ipconfig`.
6. Repite en **PC-B** el mismo `ping 192.168.10.10`. Como prueba de control, ejecuta en **PC-A** `ping 192.168.10.20`. Conserva las salidas y compara con el estado base.

**Evidencia D2:** dirección errónea observada, enlace activo, hipótesis, restauración comprobada y ambas pruebas finales. No atribuyas una causa única al texto de error de ping sin revisar estos datos.

## 6. Actividad E — Informe, desafío e inglés

Redacta el incidente D1 o D2 con esta plantilla y resume el otro en una tabla antes/después:

| Campo | Tu respuesta |
| --- | --- |
| Síntoma | Qué prueba falló y desde qué PC |
| Alcance | Qué comunicación fallaba y qué acceso seguía disponible |
| Evidencia | Dirección, interfaz y comando con su resultado observado |
| Hipótesis | Causa propuesta y dato que la apoya |
| Cambio mínimo y reversión | Qué restauraste y cuál era el valor o enlace correcto |
| Verificación | Repetición de la prueba fallida y prueba de control |
| Límite | Qué servicio o condición no comprobaste |

Escribe además **dos o tres oraciones en inglés** sobre la prueba y la reparación. Puedes utilizar:

- Si ejecutaste la práctica: `I tested ... from ...` / `The evidence showed ...` / `After restoring ..., I verified ...`.
- Si analizaste los ejemplos: `The supplied evidence shows ...` / `I would restore ... and then test ...`.

Las soluciones incluyen modelos. La exactitud técnica se evalúa por separado de la gramática; una frase imperfecta pero técnicamente clara no equivale a desconocer redes.

**Desafío opcional, sin puntos y sin ejecutar cambios:** PC-A pasa a `192.168.10.30/24` y PC-B a `192.168.10.40/24`; todo lo demás permanece igual. Predice si pueden comunicarse localmente, escribe los dos comandos ping y explica por qué no necesitan gateway. Después indica cómo recuperarías el plan original. No cambies la máscara ni añadas dispositivos.

Entrega en texto o Markdown:

1. Modalidad y versión de Packet Tracer si lo usaste; R1–R3 y predicciones de A.
2. Evidencia B1–B2 y tabla de pruebas C1.
3. Observación o análisis C2.
4. Resultados D1–D2, con las reversiones y verificaciones.
5. Informe E, explicación breve en inglés y Q1–Q8 de la lección.
6. Una duda técnica y, si corresponde, una expresión inglesa que dificultó la comprensión.

No necesitas capturas, audio ni archivos del simulador. Los entregables de esta sesión son sus dos Markdown.

## 7. Diagnóstico, verificación y cierre

| Síntoma | Primera comprobación | Próximo paso razonado |
| --- | --- | --- |
| Puerto sin enlace | Cable, puertos de ambos extremos y encendido | Corregir el enlace concreto; no cambiar IP para reparar un cable ausente |
| Enlace inicialmente ámbar | Estado de arranque y estabilización del switch | Esperar la transición y observar; si persiste, registrar el estado antes de cambiar configuración |
| Enlaces activos, ping falla persistentemente | `ipconfig` en ambas PCs, destino escrito y máscara | Comparar con el plan; reparar una discrepancia cada vez |
| IOS rechaza `ipconfig` | Ventana y prompt | Ejecutarlo en Command Prompt de la PC |
| `show interfaces status` no se reconoce | Modo EXEC privilegiado y ayuda del modelo | Probar la consulta disponible de interfaces y documentar la limitación |
| Solo falla el primer intento | Estado inicial, ARP y una segunda prueba completa | Conservar ambas salidas; investigar si la pérdida se repite |
| Simulation parece detener el ping | Eventos pendientes | Avanzar eventos o volver a Realtime |
| ARP conserva una entrada y el ping falla | Momento en que se aprendió y estado actual del enlace | No usar una asociación en caché como prueba suficiente de disponibilidad |

**Verificación final obligatoria:** PC-A `192.168.10.10/24`, PC-B `192.168.10.20/24`, gateway y DNS sin configurar; L1 y L2 restaurados; consultas por consola disponibles; ping completo sin pérdidas en ambos sentidos después de estabilizar. Si algo sigue pendiente, indícalo como pendiente.

Los incidentes D1 y D2 deben quedar revertidos. Para deshacer también la construcción completa, cierra únicamente el escenario nuevo sin guardarlo; conserva tus notas en Markdown y no sobrescribas laboratorios anteriores. No se requiere una orden de guardado, borrado de configuración ni reinicio de IOS para esta sesión.

## 8. Alternativa didáctica sin Packet Tracer

Este conjunto es **ficticio** y no demuestra que el profesor o el alumno ejecutaron un laboratorio. Úsalo para completar el razonamiento y escribe “no suministrado” en campos que carezcan de datos, como la versión instalada o los parámetros realmente observados.

| Estado | PC-A: IP/máscara | PC-B: IP/máscara | Puertos Fa0/1 y Fa0/2 | Prueba principal |
| --- | --- | --- | --- | --- |
| Base | 192.168.10.10/24 | 192.168.10.20/24 | Ambos activos | A→B: 4/4; B→A: 4/4 respuestas |
| Caso X | Sin cambios | Sin cambios | Fa0/1 activo; Fa0/2 sin enlace | A→B: 0/4 respuestas del destino |
| Restauración X | Sin cambios | Sin cambios | Ambos activos | A→B: 4/4; B→A: 4/4 |
| Caso Y | Sin cambios | 192.168.20.20/24 | Ambos activos | B→A: 0/4 respuestas del destino |
| Restauración Y | Sin cambios | 192.168.10.20/24 | Ambos activos | B→A: 4/4; A→B: 4/4 |

En todos los estados, la consola de PC-A a SW1 permanece disponible. No hay gateway, DNS ni router. El caso X corresponde a retirar L2; el caso Y, a cambiar únicamente la dirección de PC-B.

Datos adicionales ficticios para C2: antes del tráfico no había entrada ARP para PC-B; después, PC-A relaciona `192.168.10.20` con `02:00:00:00:00:20`. Una solicitud ICMP tiene origen `192.168.10.10` y destino `192.168.10.20`; su respuesta invierte esas direcciones y atraviesa el mismo switch. La MAC es inventada y no debe trasladarse como configuración de tus PCs.

Reconstruye las comprobaciones y reparaciones de X e Y, interpreta ARP/ICMP y completa E y Q1–Q8. La comprensión de estos datos puede evaluarse, pero la construcción y la ejecución seguirán sin confirmar.

## 9. Rúbrica

| Criterio | Puntos | Evidencia |
| --- | ---: | --- |
| R1–R3 | 3 | Un punto por cada recuperación correcta |
| B1: topología y consultas | 8 | Enlaces y funciones correctos (4); contexto de IOS y estados interpretados (4) |
| B2: direccionamiento | 6 | Ambas configuraciones contrastadas (4); explicación de subred local (2) |
| C1: alcance IP | 6 | Pruebas en ambos sentidos (4); interpretación y límites (2) |
| C2: ARP e ICMP | 5 | Asociación IP/MAC (2); solicitud/respuesta (2); límite de caché o simulación (1) |
| D1 y D2 | 10 | Cinco por caso: síntoma, evidencia, causa, reversión y verificación |
| E: informe e inglés técnico | 4 | Informe coherente (2); explicación técnica breve en inglés (2), idioma corregido aparte |
| Q1–Q8 | 8 | Un punto por cada respuesta razonada |
| **Total** | **50** | **40/50 = 80%, criterio interno de entrenamiento** |

Este porcentaje no es una nota oficial de aprobación de Cisco. Sin reversión comprobada, la ejecución del laboratorio no se considera completada aunque la puntuación conceptual sea alta. El análisis alternativo puede recibir puntuación conceptual, identificada como tal; nunca certifica ejecución.

## 10. Fuentes oficiales

Consultadas el **18/10/2026**. La lección contiene también las fuentes del temario y de las fechas de examen: v1.1 disponible hasta el 02/02/2027; objetivo v2.0 anunciado desde el 03/02/2027.

- [Packet Tracer: cables e indicadores de enlace](https://tutorials.ptnetacad.net/help/default/connectionsLinks.htm).
- [Packet Tracer: IP Configuration, Terminal y Command Prompt](https://tutorials.ptnetacad.net/help/default/config_PCs.htm).
- [Cisco: consultas de interfaces del Catalyst 2960](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/15-0_2_ez/command/reference/cr2960/cli2.html).
- [Cisco: funcionamiento de ping](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13730-ext-ping-trace.html).
- [Cisco Networking Academy: ARP y observación en Packet Tracer](https://contenthub.netacad.com/courses/itn-dl/_common/9.2.9-packet-tracer---examine-the-arp-table_es-XL.pdf).

---

## 11. Soluciones razonadas — consultar después del intento

### Recuperación y actividad A

- **R1:** `enable` permite pasar de EXEC de usuario a EXEC privilegiado; el sufijo cambia de `>` a `#`.
- **R2:** IPv4 permite identificar la interfaz en la red IP; Ethernet utiliza MAC para identificar origen y destino de la trama en el enlace. No son nombres intercambiables.
- **R3:** sí. La consola serial y el enlace Ethernet son conexiones distintas. Una falla de datos no implica necesariamente perder el acceso de consola.
- **Predicciones A:** no se requiere Internet. Además del enlace, hacen falta direcciones y máscaras coherentes y pruebas de alcance con resultados documentados. Verde no equivale a servicio validado.

### Actividad B

**B1:** PC-A y PC-B utilizan sus interfaces FastEthernet0 hacia Fa0/1 y Fa0/2 del switch. El cable Console conecta RS-232 con Console; no transporta el ping entre PCs. `enable` permite las consultas desde `Switch#`. En el estado base se esperan enlaces activos; `unassigned` en los puertos de capa 2 no impide su función. Modelo, versión y mensajes deben salir de la observación del alumno, no de esta solución.

**B2:** las direcciones correctas son `192.168.10.10` y `192.168.10.20`, ambas con `255.255.255.0`. Con esa máscara pertenecen a `192.168.10.0/24` y tienen identificadores de host diferentes. No necesitan una salida hacia otra red para comunicarse entre sí. Gateway y DNS quedan sin configurar. La validación utiliza `ipconfig` en cada PC, no solo la tabla que se planeó introducir.

### Actividad C

**C1:** los comandos correctos son `ping 192.168.10.20` desde PC-A y `ping 192.168.10.10` desde PC-B. Una prueba estable completa con respuestas permite afirmar alcance ICMP para esos destinos y ese momento. No valida navegación web ni acceso a Internet. El primer intento puede diferir de los siguientes; debe registrarse sin convertir una posible explicación en una causa demostrada.

**C2:** después del intercambio, PC-A normalmente dispone de una asociación ARP para PC-B. La MAC concreta depende de la PC simulada. La solicitud ICMP viaja de `192.168.10.10` a `192.168.10.20`; la respuesta hace el recorrido inverso. El switch reenvía las tramas en la LAN. La ausencia de un nuevo ARP durante una prueba posterior puede explicarse por una asociación ya conocida. La tabla de caché no garantiza disponibilidad actual.

En el análisis alternativo, la asociación es la suministrada `192.168.10.20` → `02:00:00:00:00:20`. Es evidencia ficticia explícita, no la MAC de una ejecución propia.

### Actividad D

**D1:** Fa0/2 sin enlace, junto al cable retirado, apoya una falla física en el tramo de PC-B. La consola y Fa0/1 siguen disponibles. La reparación consiste en devolver L2 a los mismos puertos. Después se repite A→B y se comprueba B→A y el estado de Fa0/2. Cambiar IP no reconectaría el enlace.

**D2:** con máscara `/24`, `192.168.20.20` no pertenece a la subred de `192.168.10.10`. Los enlaces pueden estar activos mientras la comunicación IP falla. El escenario carece de un router y una salida configurada para enlazar las subredes. La reparación del error introducido es restaurar PC-B a `192.168.10.20`, verificar su configuración y repetir **desde PC-B** `ping 192.168.10.10`. El control es **desde PC-A** `ping 192.168.10.20`.

En ambos incidentes, “ya lo cambié” no basta: falta demostrar que volvió el estado esperado y que la prueba se recuperó. Si la salida no coincide, debe registrarse y analizarse, no sustituirse por esta solución.

### Actividad E y desafío

**Ejemplo de informe didáctico para D2:** PC-B dejó de obtener respuestas de PC-A tras modificar su IPv4. Los dos enlaces seguían activos y la consola funcionaba. La consulta de PC-B mostraba `192.168.20.20/24`, mientras PC-A conservaba `192.168.10.10/24`. La hipótesis era direccionamiento fuera del plan. Se propone restaurar `192.168.10.20/24`, verificar con `ipconfig` y repetir el ping de B hacia A, seguido del control A hacia B. Los datos ficticios muestran recuperación de ambas pruebas. Esto no comprueba HTTP, DNS ni Internet.

**Modelo de inglés si lo ejecutaste y observaste:** “I tested connectivity from PC-B to PC-A. After restoring PC-B's original IP address, I received replies in both directions.” Úsalo únicamente si coincide con tus resultados.

**Modelo de análisis:** “The supplied evidence shows an incorrect IP address on PC-B. I would restore the original address and then test connectivity in both directions.”

Confundir una máscara con un gateway es un error de redes. Una preposición incorrecta que no cambie esa distinción se corrige como idioma. Si no comprendes el enunciado, indícalo para separar ambas dificultades.

**Desafío:** `.30` y `.40` son hosts distintos de `192.168.10.0/24`; con el resto del escenario correcto pueden comunicarse localmente. Desde PC-A se usaría `ping 192.168.10.40` y desde PC-B `ping 192.168.10.30`. El gateway no interviene en esa comunicación local. Para volver al plan se restauran `.10` en A y `.20` en B, se verifica la máscara y se repiten las pruebas originales. Esta respuesta es una predicción, no una prueba realizada.

### Preguntas Q1–Q8 de la lección

1. **Q1:** Ethernet conecta FastEthernet0 con el puerto de datos del switch y transporta las tramas del ping. La conexión RS-232 a Console sirve para administrar SW1 localmente.
2. **Q2 — Traducción:** “¿Por qué PC-A y PC-B no necesitan una puerta de enlace predeterminada para comunicarse en este laboratorio?”. **Respuesta:** ambas pertenecen a la misma subred con la máscara dada y comparten la LAN; no requieren enviar el tráfico a otra red.
3. **Q3:** sí. El switch reenvía tramas entre puertos de la misma LAN sin necesitar una IP propia para ese reenvío. Una IP de administración permitiría dirigirse al switch como destino de gestión por red; aquí se usa consola.
4. **Q4:** la comparación de tres números funciona porque `/24` delimita exactamente esos primeros 24 bits. Otra máscara puede situar el límite en otra posición; no puede deducirse la subred ignorándola.
5. **Q5 — Traducción:** “Ambos enlaces están activos, pero ping falla. ¿Qué dos ajustes de los hosts comprobarías primero y por qué?”. **Respuesta:** IPv4 y máscara en ambas PCs, contrastadas con la tabla. Determinan las identidades y la decisión local/remota; un enlace activo no garantiza que sean correctas.
6. **Q6:** muestra asociaciones IPv4–MAC conocidas por el host. Una asociación puede conservarse después de desconectar el destino, por lo que hace falta una prueba actual para evaluar alcance.
7. **Q7:** es válido decir que se recibieron respuestas ICMP a esas cuatro solicitudes. No permite afirmar que HTTP funciona ni que existe acceso a Internet; tampoco mide capacidad real del enlace.
8. **Q8 — Traducción:** “Después de restaurar la dirección original de PC-B, ¿qué evidencia recogerías antes de cerrar el incidente?”. **Respuesta:** `ipconfig` de PC-B con dirección y máscara correctas, enlaces activos, la repetición de B→A y el control A→B con resultados completos. Si falta alguno, se registra pendiente.

---

**Estado:** material preparado; estudio, ejecución, dominio técnico y comprensión del inglés pendientes de evidencia del estudiante.

Fin de la práctica S006.
