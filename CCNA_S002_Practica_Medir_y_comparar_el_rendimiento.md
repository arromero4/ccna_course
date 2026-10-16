# CCNA S002 — Práctica: medir y comparar el rendimiento

**Fecha:** 15 de octubre de 2026.  
**Lección asociada:** bits, bytes, velocidad, latencia y pérdida.  
**Duración principal:** 40 minutos; la instalación inicial de un simulador, si fuera necesaria, requiere tiempo adicional.  
**Nivel:** principiante.  
**Objetivos de apoyo:** 1.6 y 2.4 del temario CCNA v2.0.  
**Entorno:** cálculos y escenarios de Campus Aurora; una micropráctica en Packet Tracer o una observación equivalente en un laboratorio autorizado con Windows 10/11.

## 1. Preparación

Lee primero S002. Necesitas un editor Markdown y una calculadora para comprobar resultados después de resolverlos. Si no tienes simulador o laboratorio, usa los datos ficticios de la actividad C y registra esa modalidad.

En cada resultado indica su origen:

- **Cálculo:** deriva de cantidades y supuestos del ejercicio.
- **Escenario:** interpreta datos ficticios proporcionados aquí.
- **Observación:** proviene de una prueba que tú ejecutaste.

El profesor no ha ejecutado los laboratorios de este archivo. Las salidas de ejemplo están redactadas para aprender a interpretarlas.

### Distribución sugerida

| Actividad | Tiempo |
| --- | ---: |
| A. Unidades | 8 min |
| B. Transferencia y cuello de botella | 6 min |
| C. RTT y solicitudes sin respuesta | 10 min |
| D. Micropráctica o análisis alternativo | 12 min |
| E. Diagnóstico final | 4 min |

## 2. Actividad A — Convierte con unidades

Escribe operación, resultado y unidad. En todos los casos se usan prefijos decimales.

1. ¿Cuántos bytes representan **8 000 bits**?
2. ¿Cuántos megabits contiene un archivo de **3 MB**?
3. Convierte **80 Mb/s** a **MB/s**.
4. Convierte **2,5 MB/s** a **Mb/s**.

**Verificación:** invierte cada operación y comprueba que recuperas el valor inicial. Si un resultado crece al pasar de bits a bytes, revisa la dirección de la conversión.

**Evidencia esperada:** cuatro operaciones explícitas. Un número sin unidad no demuestra una conversión completa.

## 3. Actividad B — ¿Cuánto debería tardar?

Campus Aurora necesita descargar un archivo de **100 MB**. Su recorrido atraviesa enlaces con estas capacidades:

| Tramo | Capacidad del escenario |
| --- | ---: |
| Servidor hacia tránsito | 100 Mb/s |
| Tránsito hacia oficina | 25 Mb/s |
| Switch hacia cliente | 1 000 Mb/s |

No se suman: la misma transferencia atraviesa los tres.

### B1. Predicción ideal

Identifica el tramo limitante y calcula el tiempo ideal de transferencia. Supón que no hay tráfico compartido, cabeceras adicionales, retransmisiones, espera de establecimiento ni límites de procesamiento o disco.

### B2. Medición ficticia de aplicación

En el escenario, la descarga completa tarda **50 segundos**.

- Calcula su tasa útil media en MB/s.
- Convierte ese resultado a Mb/s.
- Explica por qué ese dato no identifica por sí solo la causa de la diferencia respecto del modelo ideal.

### B3. Una propuesta que debes evaluar

Alguien propone cambiar la interfaz del cliente de 1 Gb/s a 10 Gb/s. ¿Ese cambio eliminaría el cuello de botella indicado en la tabla? Justifica usando el recorrido.

**Evidencia esperada:** capacidad limitante, tiempo ideal, tasa observada y decisión razonada. Mantén separados el cálculo ideal y la medición ficticia.

## 4. Actividad C — Interpreta dos pruebas

Las siguientes son **salidas didácticas simplificadas**, no mediciones de tu computadora. Ambas pruebas consultan el mismo destino de laboratorio y usan el mismo tamaño de datos y tiempo de espera.

### Prueba A

```text
Destino: 192.168.10.30
Solicitudes enviadas: 20
Respuestas recibidas: 20
Solicitudes sin respuesta: 0
RTT mínimo: 4 ms
RTT promedio: 5 ms
RTT máximo: 6 ms
```

### Prueba B

```text
Destino: 192.168.10.30
Solicitudes enviadas: 20
Respuestas recibidas: 18
Solicitudes sin respuesta: 2
RTT mínimo: 4 ms
RTT promedio: 10 ms
RTT máximo: 42 ms
```

Contesta:

1. Calcula el porcentaje de solicitudes sin respuesta de cada prueba.
2. ¿Qué cambió en las respuestas observadas y en el RTT?
3. ¿Por qué “el promedio de B es 10 ms” no describe toda la muestra?
4. ¿Puedes afirmar que la tasa de descarga de B es 10 Mb/s? Explica la confusión de magnitudes.
5. ¿Puedes localizar una falla en un cable usando solo estos resúmenes?
6. Propón una siguiente prueba que mantenga condiciones comparables y permita reducir la incertidumbre.

**Pista metodológica:** el promedio de RTT corresponde a las respuestas recibidas. No asignes 0 ms a una solicitud que no recibió respuesta; cero representaría un resultado inexistente.

**Evidencia esperada:** cálculos con denominador correcto y una conclusión sobre esta muestra, no sobre toda la red para siempre.

## 5. Actividad D — Observa una prueba acotada

Elige una modalidad. No necesitas realizar las tres.

### D1. Packet Tracer — Micropráctica guiada

Utiliza dos PCs y un switch nuevo del simulador, por ejemplo un 2960. Anota la versión de Packet Tracer instalada en tu tabla de evidencia. Las etiquetas de interfaz pueden variar; identifica las de tu modelo. Esta actividad no requiere comandos de configuración del switch ni acceso a Internet.

#### Topología y parámetros

| Extremo 1 | Puerto | Extremo 2 | Puerto | Conexión |
| --- | --- | --- | --- | --- |
| PC-A | FastEthernet0 | SW1 | FastEthernet0/1 | Cable de cobre directo |
| PC-B | FastEthernet0 | SW1 | FastEthernet0/2 | Cable de cobre directo |

| Equipo | IPv4 | Máscara | Gateway | DNS |
| --- | --- | --- | --- | --- |
| PC-A | 192.168.10.20 | 255.255.255.0 | Vacío | Vacío |
| PC-B | 192.168.10.30 | 255.255.255.0 | Vacío | Vacío |

Los equipos están en la misma subred del ejercicio y conectados al mismo switch, con su configuración inicial. No necesitan gateway para esta conversación local. El cálculo de subredes se estudiará después; hoy utiliza los parámetros dados.

#### Pasos

1. Crea un escenario nuevo. Coloca PC-A, PC-B y SW1.
2. Conecta los dos cables de la tabla y espera a que los enlaces estén listos.
3. En cada PC, abre **Desktop → IP Configuration** y asigna su IPv4 y máscara. Deja vacíos gateway y DNS.
4. En PC-A, abre **Desktop → Command Prompt** y ejecuta:

   ```text
   ping 192.168.10.30
   ```

5. Conserva el primer resultado. Una primera prueba puede diferir de las siguientes mientras se completa la preparación de la comunicación; no elimines esa evidencia.
6. Repite el mismo comando cinco veces para obtener una muestra más amplia. Cuenta las solicitudes y respuestas que realmente muestre tu versión. Si cada ejecución envía cuatro solicitudes, cinco ejecuciones suman veinte.
7. Registra porcentaje sin respuesta y RTT mostrado. Si solo ves `<1 ms`, conserva ese texto: no lo conviertas en 0 ms exactos.
8. Para una falla controlada, retira **solo el cable entre SW1 y PC-B dentro del simulador**. Repite una vez la prueba y anota qué cambió. Si existe cualquier conexión alternativa, no presupongas el resultado.
9. **Reversión:** vuelve a conectar el mismo cable a los puertos originales, espera a que el enlace esté listo y repite el ping. Compara con el estado inicial.

**Resultado esperado del escenario:** con configuración correcta hay respuestas; al quitar el único enlace de PC-B se interrumpe ese recorrido; al restaurarlo debe recuperarse. Si observas otra cosa, regístrala y revisa la topología.

**Límite del simulador:** los tiempos y tasas mostrados por Packet Tracer no son un benchmark del hardware real. Hoy se utiliza para comprobar recorrido, observación de respuestas y efecto de una falla conocida. No concluyas que tu red física tiene la misma latencia ni que has medido throughput de 100 Mb/s.

No es necesario entregar un archivo del simulador. Documenta los resultados en este Markdown.

### D2. Windows 10/11 — Si ya tienes un laboratorio autorizado

Usa una PC y un destino del laboratorio que controles o tengas permiso para probar. El ejemplo presupone que el destino **192.168.10.30** existe allí; si no existe, sustituye esa dirección por la de tu destino real autorizado.

Ejecuta una prueba acotada:

```text
ping /n 20 /w 1000 192.168.10.30
```

- Envía veinte solicitudes.
- Espera hasta 1 000 ms por respuesta según la opción indicada.
- Registra cantidad enviada, recibida y sin respuesta, junto con el RTT mostrado.
- Repite una segunda muestra al mismo destino con las mismas opciones y compara.

No se cambia configuración en esta modalidad, por lo que no se requiere reversión. Si necesitas interrumpir la prueba, utiliza `Ctrl+C` y registra la cantidad realmente enviada. No desactives protecciones ni cambies configuraciones de equipos de trabajo para conseguir respuestas.

La ausencia de respuesta puede deberse a filtrado o al destino; no demuestra por sí sola que el enlace esté caído. Tampoco uses resultados de loopback para caracterizar el recorrido hasta otra máquina.

### D3. Sin simulador ni laboratorio

Usa las pruebas A y B de la actividad C para completar la tabla siguiente. En modalidad escribe **análisis de escenario; no ejecutado**. Explica qué dato adicional necesitarías obtener con un laboratorio real.

### Tabla de evidencia de D

| Campo | Primera muestra | Segunda muestra |
| --- | --- | --- |
| Modalidad y plataforma | ... | ... |
| Origen y destino | ... | ... |
| Comando o fuente del escenario | ... | ... |
| Cantidad enviada | ... | ... |
| Respuestas recibidas | ... | ... |
| Porcentaje sin respuesta | ... | ... |
| RTT mínimo / promedio / máximo | ... | ... |
| Condiciones de la prueba | ... | ... |
| Conclusión limitada | ... | ... |

Si realizaste la falla controlada de D1, añade por separado el resultado con el cable retirado y el resultado tras restaurarlo. No mezcles esa muestra con la de funcionamiento normal.

## 6. Actividad E — Diagnóstico y desafío

Caso ficticio: una aplicación descarga a **2 MB/s**. La interfaz local indica **100 Mb/s**. Una muestra de ping al servidor devuelve veinte respuestas, promedio de 5 ms.

Redacta cuatro elementos:

1. **Hecho:** convierte la tasa útil de la aplicación a Mb/s y separa las dos unidades.
2. **Límite de la evidencia:** explica qué no demuestra ese ping sobre la descarga.
3. **Hipótesis:** propone una causa posible, identificándola como hipótesis.
4. **Prueba siguiente:** explica cómo compararías condiciones para investigarla.

### Desafío opcional, sin puntos adicionales

Un archivo mide **16 MiB** y un enlace tiene **8 Mb/s**. Calcula el tiempo ideal usando `1 MiB = 1 048 576 bytes`. ¿Por qué no son exactamente 16 segundos?

## 7. Cómo verificar o corregir tus resultados

| Síntoma en tu trabajo | Comprobación |
| --- | --- |
| El tiempo parece ocho veces menor | Comprueba si mezclaste MB con Mb/s |
| La tasa cambia de unidad pero conservas el número | Revisa el factor de ocho |
| Obtienes pérdida mayor del 100% | Revisa cantidades y denominador |
| Interpretas ms como velocidad | Distingue tasa y retardo |
| El primer ping y los siguientes difieren | Conserva ambos y compara la preparación inicial y las condiciones |
| Hay pérdidas en el simulador | Revisa enlaces, direcciones y máscara antes de atribuir un problema de rendimiento |
| El ping de Windows falla y no conoces el destino | Verifica la dirección autorizada; no pruebes destinos al azar |
| La reversión no recupera el laboratorio | Comprueba puertos, cable y parámetros originales de la tabla |

La verificación incluye repetir una prueba equivalente. Si cambias destino, tamaño, cantidad, espera y medio a la vez, será difícil saber qué explica la diferencia.

## 8. Entrega para revisión

Completa este formato en tus notas o envíalo aquí:

```text
CCNA S002

A1:
A2:
A3:
A4:

B1. Cuello de botella y tiempo ideal:
B2. Tasa útil observada y límites de la conclusión:
B3. Decisión sobre cambiar la interfaz:

C1. Porcentajes:
C2. Cambio observado:
C3. Promedio y máximo:
C4. Por qué ms no equivale a Mb/s:
C5. Causa aún no demostrada:
C6. Siguiente prueba:

D. Modalidad y plataforma:
Resultados de las dos muestras:
Falla y reversión, solo si se hizo D1:
Qué pude demostrar:
Qué no pude demostrar:

E. Hecho:
E. Límite:
E. Hipótesis:
E. Prueba:

Preguntas 1–8 de la lección:

Mi principal duda:
```

## 9. Rúbrica y repaso espaciado

| Criterio | Puntos |
| --- | ---: |
| A: cuatro conversiones con operación y unidad | 4 |
| B: tiempo ideal, tasa útil y cuello de botella, 2 puntos cada uno | 6 |
| C: porcentajes, interpretación del retardo y límites de las conclusiones, 2 puntos cada uno | 6 |
| D: fuente, condiciones, conteo, interpretación y limitación, 1 punto cada uno | 5 |
| E: hecho, límite, hipótesis y prueba, 1 punto cada uno | 4 |
| **Total** | **25** |

**Meta interna:** 20/25, equivalente al 80%. No es una nota oficial del examen. No marques dominio si solo leíste la solución; vuelve a resolver con cantidades distintas. La modalidad D3 puede demostrar análisis, pero no cuenta como ejecución de un laboratorio.

- **Al día siguiente:** convierte 40 Mb/s a MB/s y explica qué mide un RTT.
- **A los siete días:** resuelve otra transferencia y otra muestra con solicitudes sin respuesta.
- **Alrededor de un mes:** integra unidades y métricas en un diagnóstico de conectividad.

Las preguntas conceptuales de la lección y el desafío opcional sirven para detectar dudas; no añaden puntos a esta rúbrica.

---

## 10. Soluciones razonadas — Abrir después de intentarlo

### A. Unidades

1. `8 000 bits ÷ 8 = 1 000 bytes`.
2. `3 MB × 8 = 24 Mbit`.
3. `80 Mb/s ÷ 8 = 10 MB/s`.
4. `2,5 MB/s × 8 = 20 Mb/s`.

En las conversiones de tasa, la parte “por segundo” permanece. Cambia la unidad de cantidad, no el intervalo temporal.

### B. Transferencia

El tramo limitante del modelo es **25 Mb/s**.

```text
100 MB × 8 = 800 Mbit
800 Mbit ÷ 25 Mbit/s = 32 segundos ideales
```

Para la descarga ficticia de 50 segundos:

```text
100 MB ÷ 50 s = 2 MB/s
2 MB/s × 8 = 16 Mb/s de datos útiles
```

La medición no identifica por sí sola si la diferencia se debe a sobrecarga, tráfico compartido, servidor, protocolo u otro límite. Cambiar la interfaz del cliente a 10 Gb/s no elimina el tramo de 25 Mb/s que sigue en el recorrido.

### C. Pruebas de eco

1. A: `(20 − 20) ÷ 20 × 100 = 0%`. B: `(20 − 18) ÷ 20 × 100 = 10%`.
2. B tuvo dos solicitudes sin respuesta, un promedio mayor y un máximo más alto. El mínimo permaneció igual.
3. El promedio de 10 ms no muestra por sí solo la respuesta que tardó 42 ms ni las solicitudes sin respuesta. El promedio se calcula sobre respuestas recibidas.
4. No. Los 10 ms son tiempo de ida y vuelta; no expresan megabits por segundo.
5. No. Los resúmenes no localizan el punto o la causa de la diferencia.
6. Es válido repetir al mismo destino bajo iguales opciones y registrar carga y medio; también comparar con un destino de control del laboratorio, dejando explícito que cambió el destino. Si sospechas un tramo, necesitarás evidencia de ese tramo antes de atribuirle la causa.

### D. Observación

No existe una salida real única que debas copiar. Se evalúa que documentes exactamente lo observado.

- **Packet Tracer:** la falla controlada aporta evidencia del efecto de quitar ese enlace en esa topología; la reversión se comprueba con una nueva prueba. No es una medición del rendimiento del hardware físico.
- **Windows:** informa porcentajes de tu muestra y RTT; una prueba corta no certifica capacidad ni ausencia permanente de pérdida. No confundes una respuesta ICMP con el éxito de una aplicación.
- **Datos ficticios:** identifica explícitamente la modalidad. Puedes obtener conclusiones del escenario, pero no afirmar que ejecutaste comandos.

Si las solicitudes recibidas superaran las enviadas o cambiaste criterios entre muestras, revisa cómo combinaste salidas antes de sacar conclusiones.

### E. Diagnóstico posible

1. La aplicación entrega **2 MB/s = 16 Mb/s** de datos útiles en el escenario.
2. El ping muestra veinte respuestas y promedio de 5 ms para esa muestra; no mide la capacidad de descarga ni garantiza que el servicio esté libre de límites.
3. Hipótesis posible: existe un límite de entrega del servidor. Es una posibilidad, no una causa confirmada.
4. Prueba: comparar una transferencia controlada desde otro servidor autorizado usando el mismo cliente y recorrido lo más equivalente posible, registrando tamaño e intervalo. Si cambian otras condiciones, documentarlas; una diferencia aislada sigue necesitando interpretación.

Otras hipótesis son aceptables si propones cómo obtener evidencia que las apoye o descarte.

### Desafío opcional

```text
16 MiB = 16 × 1 048 576 = 16 777 216 bytes
16 777 216 × 8 = 134 217 728 bits
134 217 728 ÷ 8 000 000 = 16,777216 segundos
```

No son exactamente 16 segundos porque MiB usa un múltiplo binario y Mb/s usa un múltiplo decimal. Se mantienen los supuestos ideales de transferencia.

### Preguntas de la lección S002

1. `250 × 8 = 2 000 bits`.
2. `80 ÷ 8 = 10 MB/s`.
3. 50 MB es cantidad; 50 MB/s es una tasa de cantidad por tiempo.
4. `120 × 8 ÷ 30 = 32 segundos` bajo los supuestos ideales explicados. No es una garantía de tiempo real.
5. Ping observa mensajes pequeños de eco y sus tiempos; no realiza una medición de transferencia sostenida de datos útiles.
6. `(40 − 38) ÷ 40 × 100 = 5%`. En esa muestra, el 5% no tuvo respuesta dentro del criterio de la prueba; no localiza la causa.
7. No sabes si falló la ida, el retorno, el tratamiento de ICMP, un dispositivo o la espera elegida, entre otras posibilidades. Hace falta más evidencia.
8. `6 × 8 = 48 Mb/s`. **The equivalent rate is 48 megabits per second.**

## 11. Referencias

Consultadas para la preparación de S002 el **15 de octubre de 2026**:

- [NIST: unidades y prefijos binarios](https://physics.nist.gov/cuu/Units/binary.html).
- [Microsoft: sintaxis y opciones de ping](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/ping).
- [Cisco: latencia y pérdida de paquetes](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-9300-series-switches/225617-troubleshooting-network-latency-and.html).
- Para preparar el simulador, usa el recurso oficial ya indicado en el plan: [introducción a Cisco Packet Tracer](https://www.netacad.com/courses/getting-started-cisco-packet-tracer/1000).

---

Fin de la práctica S002.
