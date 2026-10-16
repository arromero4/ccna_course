# CCNA S002 — Bits, bytes, velocidad, latencia y pérdida

**Fecha:** 15 de octubre de 2026.  
**Nivel:** desde cero.  
**Objetivo de certificación:** 200-301 CCNA v2.0.  
**Duración:** 35 minutos de explicación y repaso + 40 minutos de práctica.  
**Prerrequisito recomendado:** S001 — Qué es una red. No se presupone que ya la hayas completado.  
**Relación con el temario:** fundamentos de apoyo al diagnóstico de clientes, objetivo **1.6**, y al diagnóstico de conectividad, objetivo **2.4**. Las conversiones y métricas de esta clase son bases didácticas; no se presentan como objetivos oficiales adicionales.

**Versión y disponibilidad, verificadas el 15/10/2026:** el examen disponible es v1.1 hasta el 02/02/2027. Nuestro objetivo sigue siendo v2.0, con temario publicado y examen anunciado desde el 03/02/2027. [Cisco: fechas de transición](https://blogs.cisco.com/learning/stay-on-track-get-certified-before-the-ccna-refresh).

## 1. Lo que podrás demostrar

Al terminar podrás:

1. Convertir entre bits y bytes sin confundir `b` con `B`.
2. Distinguir tamaño de archivo, capacidad de enlace, tasa observada y tiempo de respuesta.
3. Estimar cuánto tarda una transferencia bajo supuestos explícitos.
4. Calcular un porcentaje de solicitudes de eco sin respuesta.
5. Interpretar RTT mínimo, promedio y máximo sin deducir causas que no has comprobado.
6. Escribir un diagnóstico breve que incluya medición, hipótesis y siguiente prueba.

Usaremos **Campus Aurora**, la oficina ficticia del curso. Los valores de los ejercicios son didácticos: no provienen de tu red ni de una medición ejecutada por el profesor.

## 2. Recuperación de S001 — Cinco minutos

Antes de leer, intenta responder:

- ¿Qué función permite enviar paquetes entre redes IP diferentes?
- ¿Una respuesta a `ping 127.0.0.1` demuestra que Internet funciona?
- ¿Qué diferencia hay entre “no recibí respuesta” y “el router está averiado”?

Si aún no puedes responder, repasa las secciones de funciones, loopback y evidencia de S001. Puedes continuar leyendo S002, pero registra esa base como pendiente; recibir una nueva sesión no certifica que dominaste la anterior.

## 3. Bit y byte: la cantidad de información

Un **bit** representa un valor binario, `0` o `1`. Un **byte** agrupa ocho bits.

```text
1 byte = 8 bits
bits = bytes × 8
bytes = bits ÷ 8
```

Usaremos `b` para bit y `B` para byte en las abreviaturas habituales. Por tanto, `Mb` y `MB` no expresan la misma cantidad. [1]

### Ejemplo guiado

Un bloque contiene **500 bytes**. ¿Cuántos bits representa?

1. Identifica la unidad de partida: bytes.
2. Para convertir a bits, multiplica por ocho.
3. `500 × 8 = 4 000 bits`.
4. Escribe la unidad junto al resultado.

Ahora invierte la operación: `4 000 bits ÷ 8 = 500 bytes`.

Un error frecuente es multiplicar en ambas direcciones. Comprueba el sentido: para representar la misma información, la cantidad numérica de bits es ocho veces la cantidad de bytes.

## 4. Prefijos y tasas: qué significa “por segundo”

En los ejercicios utilizaremos prefijos decimales salvo indicación explícita:

| Expresión | Equivalencia |
| --- | ---: |
| 1 kbit | 1 000 bits |
| 1 Mbit | 1 000 000 bits |
| 1 Gbit | 1 000 000 000 bits |
| 1 MB | 1 000 000 bytes |
| 1 GB | 1 000 000 000 bytes |

`Mb/s`, `Mbit/s` y `Mbps` se usan para expresar **megabits por segundo**. `MB/s` expresa **megabytes por segundo**.

Una tasa relaciona cantidad y tiempo. Un archivo de **100 MB** tiene un tamaño; una transferencia a **10 MB/s** tiene una tasa. Son magnitudes diferentes.

### Dos conversiones importantes

```text
100 Mb/s ÷ 8 = 12,5 MB/s
5 MB/s × 8 = 40 Mb/s
```

Si el enlace tiene una capacidad nominal de 100 Mb/s, 12,5 MB/s es su equivalencia aritmética en bytes por segundo, **no una promesa de rendimiento útil**.

### Una excepción que debes reconocer, sin memorizarla todavía

Los prefijos binarios tienen otros símbolos:

| Unidad | Bytes |
| --- | ---: |
| 1 MiB | 1 048 576 |
| 1 GiB | 1 073 741 824 |

**MB no es MiB.** Si una herramienta presenta unidades ambiguas, consulta qué definición utiliza antes de comparar resultados. NIST documenta esta distinción. [1]

## 5. Capacidad, throughput y goodput

Estas palabras responden preguntas diferentes:

| Concepto | Pregunta | Ejemplo ficticio |
| --- | --- | --- |
| Capacidad del enlace, a menudo llamada bandwidth | ¿Cuántos bits por segundo puede transportar ese enlace bajo sus condiciones? | Puerto de 100 Mb/s |
| Throughput o tasa observada | ¿Qué cantidad de tráfico se transfirió durante la medición? | Medición de 75 Mb/s, indicando qué tráfico se contó |
| Goodput o tasa útil de aplicación | ¿Cuántos datos útiles nuevos recibió la aplicación por segundo? | Archivo recibido a 8 MB/s |

Al medir, especifica **qué estás contando**: bytes del archivo, bytes de paquetes, datos retransmitidos, una dirección o ambas. Una cifra aislada puede esconder esas diferencias.

Los protocolos necesitan información de control, y el resultado puede estar limitado por otros enlaces, tráfico compartido, dispositivos o aplicaciones. En Wi-Fi, la tasa de enlace indicada tampoco equivale por sí sola al rendimiento de una descarga. [2]

### Caso de Campus Aurora

La PC de Ana tiene una interfaz de 1 Gb/s. El archivo que quiere descargar debe atravesar estos enlaces:

| Tramo, en el sentido de la descarga | Capacidad del escenario |
| --- | ---: |
| Servidor hacia la red de tránsito | 100 Mb/s |
| Enlace hacia la oficina | 20 Mb/s |
| Switch hacia PC de Ana | 1 000 Mb/s |

El límite más bajo del recorrido es **20 Mb/s**. La interfaz de 1 Gb/s no convierte el tramo de 20 Mb/s en uno más rápido.

Incluso esos 20 Mb/s son un **techo del modelo**: no garantizan 20 Mb/s de datos útiles para Ana. Si el enlace se comparte o el servidor entrega más despacio, puede observar menos.

No sumes las capacidades de enlaces consecutivos: `100 + 20 + 1 000` no describe la tasa disponible para una sola transferencia que debe atravesarlos todos.

## 6. Estimar el tiempo de una transferencia

La relación básica es:

```text
tiempo en segundos = cantidad de datos ÷ tasa
```

Las unidades de cantidad deben coincidir: bits con bits por segundo, o bytes con bytes por segundo.

### Ejemplo A — Modelo ideal

Quieres transferir **200 MB** por un enlace de **40 Mb/s**. Suponemos tasa constante, capacidad íntegra disponible y ausencia de sobrecarga, pérdidas, tiempos de establecimiento y límites del servidor.

1. Convierte el archivo: `200 MB × 8 = 1 600 Mbit`.
2. Divide entre la tasa: `1 600 Mbit ÷ 40 Mbit/s = 40 s`.
3. Concluye: **40 segundos en este modelo ideal**.

No lo anuncies como duración garantizada de una descarga real. Has calculado cuánto ocuparían los datos del archivo bajo unas condiciones simplificadas.

### Ejemplo B — Medición de datos útiles

El mismo archivo de 200 MB tarda **50 segundos** en completarse, medidos desde el inicio hasta el final del intervalo elegido.

```text
tasa útil media = 200 MB ÷ 50 s = 4 MB/s
4 MB/s × 8 = 32 Mb/s
```

La observación permite afirmar: “En este intervalo, la aplicación recibió el archivo con una tasa útil media de **32 Mb/s**”. No identifica por sí sola el motivo de la diferencia con 40 Mb/s.

### Control de unidades

Si divides `200 ÷ 40 = 5` y escribes “5 segundos”, mezclaste MB y Mb/s. El cálculo numérico sin unidades ocultó un factor de ocho.

## 7. Latencia: cuánto tarda una comunicación

La **latencia** es un retardo. Puede referirse a un trayecto de ida o a una ida y vuelta; hay que indicar cuál. **RTT**, por *round-trip time*, mide la ida y vuelta de una prueba. Suele expresarse en milisegundos. [3]

```text
1 segundo = 1 000 milisegundos
0,025 segundos = 25 ms
```

Un enlace puede tener gran capacidad y un RTT elevado. Transportar muchos bits por segundo no elimina la distancia ni todas las esperas.

### De dónde puede venir el tiempo

| Componente | Explicación inicial |
| --- | --- |
| Transmisión o serialización | Tiempo necesario para colocar todos los bits de un bloque en un enlace |
| Propagación | Tiempo que tarda la señal en recorrer el medio |
| Procesamiento | Tiempo empleado por los dispositivos o extremos en atender el tráfico |
| Espera en cola | Tiempo antes de que el equipo pueda transmitir el paquete |

Esta separación es un modelo de razonamiento. No puedes descomponer todos esos tiempos usando únicamente una línea de `ping`.

### Pequeño cálculo de serialización

En un modelo ideal, colocar un bloque de **1 500 bytes** sobre un enlace de **100 Mb/s** tarda:

```text
1 500 × 8 = 12 000 bits
12 000 ÷ 100 000 000 = 0,00012 s
0,00012 × 1 000 = 0,12 ms
```

**0,12 ms no es el RTT completo.** Solo has modelado la serialización de ese bloque en un enlace, sin añadir otros bits, distancias, colas ni el retorno.

Tampoco conviertas automáticamente un RTT de 40 ms en “20 ms de ida”. Los caminos y las demoras de ida y vuelta pueden ser diferentes.

## 8. Promedios y variación del retardo

Observa dos series ficticias de cuatro RTT:

| Serie | RTT de las cuatro respuestas | Promedio | Máximo |
| --- | --- | ---: | ---: |
| A | 4, 5, 6 y 5 ms | 5 ms | 6 ms |
| B | 4, 5, 6 y 85 ms | 25 ms | 85 ms |

En B, tres respuestas fueron rápidas y una tardó mucho más. Describe esa variación; el promedio solo no cuenta toda la historia.

La variación del retardo suele relacionarse con **jitter**, especialmente en aplicaciones de voz. Su medición formal depende del método. En esta sesión no calcularemos jitter normalizado: **máximo menos mínimo no se presentará como una definición universal de jitter**. [4]

El punto práctico es aprender a registrar varios valores y compararlos con una referencia del mismo escenario. No existe un único RTT “bueno” para todos los destinos y aplicaciones.

## 9. Pérdida y solicitudes sin respuesta

Los paquetes pueden no llegar por problemas como congestión o fallas. Eso puede perjudicar aplicaciones y provocar retransmisiones en los protocolos que las utilizan. [3]

En una prueba de eco, puedes calcular:

```text
solicitudes sin respuesta = solicitudes enviadas − respuestas recibidas
porcentaje sin respuesta = (sin respuesta ÷ enviadas) × 100
```

Ejemplo: envías 20 solicitudes y recibes 18 respuestas.

```text
20 − 18 = 2 sin respuesta
(2 ÷ 20) × 100 = 10%
```

La conclusión exacta es: **el 10% de las solicitudes de esa muestra no obtuvo una respuesta válida dentro del tiempo esperado**.

Eso no localiza la falla. La solicitud o la respuesta pudo perderse; el destino pudo filtrar o limitar ICMP, o una respuesta pudo llegar demasiado tarde. “10% en este `ping`” no equivale automáticamente a “10% de pérdida de todos los servicios de la red”.

Asimismo, 20 respuestas de 20 solo muestran que no observaste solicitudes sin respuesta en esa pequeña muestra. No prueban que jamás exista pérdida.

## 10. Qué demuestra `ping` y qué no

En Windows 10/11, este ejemplo envía 20 solicitudes a un **destino de laboratorio**:

```text
ping /n 20 /w 1000 192.168.10.30
```

Los argumentos significan:

- `/n 20`: cantidad de solicitudes.
- `/w 1000`: tiempo de espera de respuesta, en milisegundos.
- `192.168.10.30`: destino del escenario; úsalo solo si existe en tu laboratorio.

La documentación de Microsoft describe las respuestas ICMP y el RTT mostrado. [5] Packet Tracer y otras plataformas pueden aceptar opciones diferentes: no copies automáticamente opciones de Windows en sus terminales.

**`ping` no mide por sí solo la capacidad máxima ni la tasa de una descarga.** Enviar unos cuantos mensajes pequeños no equivale a medir cuántos datos útiles puede transportar una aplicación por segundo.

Una aplicación lenta con ping estable necesita más investigación: podría haber un límite de transferencia o una demora del servicio. El resultado de ICMP no diagnostica por sí solo la aplicación.

## 11. Miniincidente resuelto

El usuario de Campus Aurora dice: “Tenemos 100 megas, pero veo 8 al descargar; la red está mal”.

Primero pregunta por **las unidades**. Supongamos que la conexión se anuncia como 100 **Mb/s** y la aplicación indica 8 **MB/s**.

1. Convierte la tasa de la aplicación: `8 × 8 = 64 Mb/s`.
2. Ya no estás comparando 100 con 8 en la misma unidad, sino 100 con 64.
3. La cifra del enlace es nominal; la descarga registra datos útiles de una aplicación.
4. Falta delimitar el recorrido, la duración, la carga y las condiciones para decidir si el resultado es anómalo.

Un diagnóstico inicial defendible sería:

> La descarga indica 8 MB/s, equivalentes a 64 Mb/s de datos útiles. Esto no contradice por sí solo un enlace nominal de 100 Mb/s. Compararé una transferencia controlada con una referencia del mismo recorrido antes de atribuir una falla.

No es necesario prometer un porcentaje fijo de rendimiento útil: el resultado depende del escenario.

## 12. Errores comunes y cómo corregirlos

| Error | Corrección |
| --- | --- |
| Tratar 100 Mb/s como 100 MB/s | Dividir entre ocho para convertir bits/s a bytes/s |
| Suponer que MB siempre significa MiB | Leer la unidad y verificar la convención |
| Sumar capacidades de enlaces consecutivos | Identificar el cuello de botella del recorrido |
| Usar una tasa nominal como tiempo real garantizado | Explicitar supuestos y comparar con una medición |
| Interpretar “5 ms” como una tasa | ms es tiempo; Mb/s es tasa |
| Dividir RTT entre dos y afirmar la latencia exacta de ida | Reconocer que la prueba es de ida y vuelta |
| Concluir que el proveedor falla porque hubo un timeout | Recoger más evidencia y delimitar el alcance |
| Tomar tiempos de un simulador como rendimiento de hardware real | Usar la simulación para comprender comportamiento y verificar conectividad |

## 13. Vocabulario técnico

| Inglés | Significado en esta sesión | Frase reutilizable |
| --- | --- | --- |
| Bit / byte | Unidades de cantidad | One byte contains eight bits. |
| Bandwidth / link capacity | Capacidad del enlace | The link capacity is 100 megabits per second. |
| Throughput | Tasa observada bajo un método de medición | We need to measure throughput. |
| Goodput | Tasa de datos útiles nuevos | The application's goodput is lower than the link rate. |
| Latency | Retardo | High bandwidth does not guarantee low latency. |
| Round-trip time | Tiempo de ida y vuelta | Ping reports round-trip time. |
| Packet loss | Pérdida de paquetes | We observed unanswered probes in this sample. |
| Bottleneck | Tramo o recurso limitante | This link is the bottleneck in our model. |
| Baseline | Referencia de comparación | Compare the result with the baseline. |
| Timeout | Espera de respuesta agotada | A timeout does not identify the root cause. |

## 14. Comprueba tu comprensión

Responde antes de consultar las soluciones al final de la práctica S002:

1. ¿Cuántos bits contienen 250 bytes?
2. Convierte 80 Mb/s a MB/s.
3. ¿Qué diferencia hay entre 50 MB y 50 MB/s?
4. Un archivo de 120 MB atraviesa un enlace de 30 Mb/s. ¿Cuál es el tiempo del modelo ideal y qué supuestos usaste?
5. ¿Por qué `ping` con RTT bajo no demuestra gran capacidad de transferencia?
6. De 40 solicitudes, 38 obtienen respuesta. Calcula el porcentaje sin respuesta y redacta una conclusión limitada.
7. ¿Qué no sabes todavía si la única evidencia es un timeout?
8. **A file transfer averages 6 MB/s. What is the equivalent rate in Mb/s?** Escribe el cálculo y una respuesta breve en inglés.

## 15. Repaso de un minuto y siguiente paso

- **Cantidad:** bit o byte. **Tasa:** cantidad por segundo. **Retardo:** tiempo.
- Multiplica bytes por ocho para obtener bits; divide bits entre ocho para obtener bytes.
- Alinea unidades antes de calcular `tiempo = cantidad ÷ tasa`.
- Escribe “ideal” cuando usas un modelo sin sobrecargas y “observado” cuando usas datos de una prueba.
- Con `ping`, registra destino, cantidad, espera, respuestas y RTT. No lo conviertas en una prueba de velocidad de descarga.

### Lista de verificación

- [ ] Puedo explicar un resultado con unidades.
- [ ] Distingo capacidad de enlace y datos útiles recibidos.
- [ ] Declaro los supuestos de un tiempo calculado.
- [ ] Puedo calcular porcentajes de solicitudes sin respuesta.
- [ ] Evito atribuir una causa que todavía no probé.

Completa `CCNA_S002_Practica_Medir_y_comparar_el_rendimiento.md`. Si envías tus cálculos, incluye el procedimiento: interesa tanto el resultado como el razonamiento.

**Siguiente sesión prevista:** S003 — Modelos OSI y TCP/IP. Organizaremos las responsabilidades de una comunicación por capas.

## 16. Fuentes y alcance

Consultadas el **15 de octubre de 2026**. Los ejercicios, cifras y escenarios son originales. Las referencias técnicas apoyan las definiciones y la sintaxis; no es necesario estudiar los comandos avanzados de esos documentos en esta sesión.

1. [NIST: prefijos decimales y binarios, bits y bytes](https://physics.nist.gov/cuu/Units/binary.html).
2. [Cisco: medición y diagnóstico del rendimiento Wi-Fi](https://www.cisco.com/c/en/us/support/docs/wireless/catalyst-9800-series-wireless-controllers/221766-validate-wi-fi-throughput-testing-and.html).
3. [Cisco: latencia y pérdida de paquetes](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html).
4. [Cisco: variación del retardo en voz](https://www.cisco.com/c/en/us/support/docs/voice/voice-quality/18902-jitter-packet-voice.html).
5. [Microsoft: comando ping](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/ping).

El alcance curricular se mantiene conforme al [temario oficial CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf), revisado al inicio del bloque el 14/10/2026. No se atribuye a esta clase el dominio completo de 1.6 o 2.4.

---

Fin de la lección S002.
