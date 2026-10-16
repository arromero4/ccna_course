# CCNA S001 — Práctica: observar y describir una red

**Fecha prevista:** 14 de octubre de 2026.  
**Lección asociada:** CCNA S001 — Qué es una red.  
**Duración:** 35–40 minutos, más 5–10 minutos de revisión.  
**Requisitos:** haber leído S001; no necesitas Packet Tracer para esta práctica.  
**Entorno:** observación de tu Windows o análisis equivalente de datos ficticios.  
**Propósito:** reconocer componentes y parámetros, distinguir evidencias de suposiciones y redactar un diagnóstico inicial.

## 1. Preparación y reglas del ejercicio

Trabajaremos con Campus Aurora, un caso ficticio. No habrá cambios de configuración. Solo se consultarán datos y, si eliges hacerlo en tu computadora, se ejecutará una prueba local de loopback.

Cada respuesta debe identificar su origen: **observación real**, **dato del escenario** o **hipótesis**. Así aprenderás desde el primer día a no presentar una suposición como resultado de laboratorio.

Reserva una sección de notas en este mismo Markdown. No necesitas crear imágenes ni enviar archivos de otro formato.

## 2. Actividad A — Identifica las funciones

**Tiempo:** 7 minutos.

La oficina tiene:

- Dos PCs que utilizan una aplicación de solicitudes.
- Un servidor conectado por cable.
- Un switch al que llegan los cables de las PCs y el servidor.
- Un punto de acceso para una laptop.
- Un router que comunica la red de la oficina con otra red.

Completa:

| Necesidad | Equipo o función que la atiende | Por qué |
| --- | --- | --- |
| Solicitar una página de la aplicación | ... | ... |
| Responder a la solicitud de esa aplicación | ... | ... |
| Interconectar dispositivos cableados de la LAN | ... | ... |
| Integrar la laptop por Wi-Fi | ... | ... |
| Enviar paquetes hacia otra red IP | ... | ... |

**Resultado esperado:** cinco funciones identificadas y justificadas. No se pide una marca o modelo comercial.

## 3. Actividad B — Lee la configuración de un cliente

**Tiempo:** 10 minutos.

### Ruta 1: observación en Windows

1. Abre Símbolo del sistema o Terminal.
2. Ejecuta el comando siguiente:

   ```text
   ipconfig
   ```

3. Localiza el adaptador que utilizas para conectarte. Si hay varios, escribe cómo decidiste cuál observar.
4. Anota IPv4, máscara y puerta de enlace, si aparecen. Si un campo está vacío, registra “no observado” en lugar de inventarlo.
5. Si necesitas más contexto, consulta:

   ```text
   ipconfig /all
   ```

6. Completa la tabla de evidencia de abajo. No cambies direcciones ni deshabilites adaptadores.

### Ruta 2: análisis sin computadora Windows

Usa esta **salida didáctica ficticia**, simplificada para el ejercicio. No representa una lectura de tu equipo:

```text
Adaptador Ethernet Ethernet:
   Dirección IPv4 . . . . . . . . . . : 192.168.10.20
   Máscara de subred . . . . . . . . : 255.255.255.0
   Puerta de enlace predeterminada . : 192.168.10.1

Adaptador de LAN inalámbrica Wi-Fi:
   Estado de los medios . . . . . . : medios desconectados
```

### Tabla de evidencia

| Campo | Lo que observaste | Tipo de evidencia |
| --- | --- | --- |
| Adaptador elegido | ... | Real / escenario |
| IPv4 | ... | Real / escenario |
| Máscara | ... | Real / escenario |
| Puerta de enlace | ... | Real / escenario |
| Otro adaptador y su estado | ... | Real / escenario |

Responde:

1. ¿Qué parte de la salida te ayudó a elegir el adaptador?
2. ¿Qué campo delimita la subred?
3. ¿Ver una puerta de enlace en la salida demuestra que responde? Explica.

**Resultado esperado:** parámetros identificados y límites de la evidencia reconocidos. La mera existencia de una dirección no demuestra comunicación exitosa.

## 4. Actividad C — Interpreta una prueba de loopback

**Tiempo:** 6 minutos.

En Windows puedes ejecutar:

```text
ping 127.0.0.1
```

Este es un ejemplo ficticio de parte de una respuesta:

```text
Respuesta desde 127.0.0.1: bytes=32 tiempo<1m TTL=128
Paquetes: enviados = 4, recibidos = 4, perdidos = 0
```

El idioma, la presentación del tiempo y otros detalles pueden variar. Observa dirección de destino, respuestas y pérdida; no necesitas interpretar el TTL todavía.

Si no ejecutas el comando, analiza el ejemplo y márcalo como escenario.

Contesta:

1. ¿Cuál fue el destino de la prueba?
2. ¿La solicitud necesitó alcanzar el router de la oficina?
3. ¿Qué conclusión limitada puedes escribir si recibes respuestas?
4. ¿Qué dos cosas todavía no has demostrado?

**Resultado esperado:** describir alcance local de la prueba sin afirmar conectividad a Internet.

## 5. Actividad D — Sigue dos conversaciones

**Tiempo:** 7 minutos.

Todos los enlaces y dispositivos del escenario funcionan. PC-A y el servidor están en la misma VLAN. Usan estos parámetros:

| Equipo | IPv4 | Máscara | Gateway |
| --- | --- | --- | --- |
| PC-A | 192.168.10.20 | 255.255.255.0 | 192.168.10.1 |
| Servidor local | 192.168.10.30 | 255.255.255.0 | 192.168.10.1 |
| Servidor de sucursal | 192.168.20.30 | 255.255.255.0 | 192.168.20.1 |

No se te pide configurar las rutas. Para el segundo caso, supón que los routers tienen rutas correctas de ida y vuelta.

### Conversación 1

PC-A solicita una página al servidor local `192.168.10.30`.

- ¿Ambos están en la misma subred de este ejemplo?
- ¿Qué función permite el intercambio dentro de la LAN?
- ¿Debe esa comunicación pasar por Internet?

### Conversación 2

PC-A solicita una página al servidor de sucursal `192.168.20.30`.

- ¿El destino está en la misma subred de PC-A?
- ¿Qué función adicional necesita el recorrido?
- ¿Qué dirección tiene el gateway de PC-A?
- ¿Por qué sería insuficiente comprobar solo el recorrido de ida?

**Resultado esperado:** diferenciar destino local y remoto, y reconocer que una conversación necesita respuestas de regreso.

## 6. Actividad E — Redacta un diagnóstico breve

**Tiempo:** 5 minutos.

Escenario nuevo:

- PC-A y PC-B imprimen en la impresora local.
- Desde ambas falla una aplicación externa.
- No se han probado otros servicios externos ni la resolución de nombres.

Redacta tres frases:

1. Un hecho observado.
2. Una conclusión limitada que ese hecho permite.
3. Una siguiente prueba que ayude a reducir la incertidumbre.

Evita presentar “el router está roto” o “el proveedor falló” como hechos si todavía no los has comprobado. Puedes proponerlos como hipótesis y explicar cómo las verificarías.

## 7. Si te atoras

| Dificultad | Siguiente paso útil |
| --- | --- |
| Aparecen muchos adaptadores | Revisa si estás usando cable o Wi-Fi y observa sus estados |
| No aparece IPv4 | Registra la ausencia; puedes usar el escenario ficticio para aprender los campos |
| No sabes qué significa un campo | Vuelve a la tabla de la sección 5 de la lección |
| Confundes dirección IP y gateway | Señala la dirección del cliente y después la dirección a la que delega tráfico remoto |
| La salida es distinta al ejemplo | Anota lo que realmente aparece; un ejemplo no obliga a tu equipo a mostrar lo mismo |
| Solo puedes completar una parte | Entrega esa parte e identifica la duda concreta |

## 8. Tu entrega para revisión

Copia y completa este formato en tus notas o en el chat:

```text
CCNA S001
Ruta utilizada: Windows real / escenario ficticio

A. Funciones identificadas:
1.
2.
3.
4.
5.

B. Parámetros:
Adaptador:
IPv4:
Máscara:
Gateway:
Lo que todavía no puedo concluir:

C. Loopback:
Destino:
Conclusión permitida:
Dos cosas no demostradas:

D. Recorridos:
Local:
Sucursal:
Importancia del retorno:

E. Diagnóstico en tres frases:
1.
2.
3.

Preguntas 1–8 de la lección:
1.
2.
3.
4.
5.
6.
7.
8.

Mi principal duda:
```

El profesor revisará tus respuestas y el razonamiento. Completar una tabla con datos ficticios se registra como análisis de escenario, no como laboratorio ejecutado en hardware.

## 9. Rúbrica de autoevaluación

| Criterio | Puntos |
| --- | ---: |
| A: cinco funciones justificadas | 5 |
| B: identifica parámetros y limita sus conclusiones | 5 |
| C: interpreta correctamente el alcance del loopback | 3 |
| D: distingue ambos recorridos y el retorno | 4 |
| E: separa hecho, conclusión y siguiente prueba | 3 |
| **Total** | **20** |

**Meta interna:** 16/20 y poder explicar con tus palabras qué hace el router. Si obtienes menos, repasa las secciones relacionadas y vuelve a resolver con nombres y direcciones distintos. Esta meta es del curso; no es una puntuación oficial del CCNA.

Las ocho preguntas de la lección sirven para comprobar conceptos y detectar qué conviene repasar; no suman puntos adicionales en esta rúbrica.

---

## 10. Soluciones razonadas — Consulta después de intentarlo

### Actividad A

1. El navegador funciona como **cliente** porque solicita el servicio.
2. La aplicación del servidor ofrece la respuesta. El servidor puede ser software y no exige una caja física exclusiva.
3. El **switch** conecta los dispositivos de la LAN y conmuta sus tramas.
4. El **punto de acceso** integra el cliente inalámbrico.
5. La función de **router o enrutamiento de capa 3** permite avanzar entre redes IP.

### Actividad B

En el escenario ficticio, Ethernet tiene `192.168.10.20`, máscara `255.255.255.0` y gateway `192.168.10.1`. Wi-Fi aparece desconectado. La máscara delimita la subred. La salida muestra configuración; aún no prueba que el gateway responda.

Si hiciste la ruta real, tus valores pueden ser distintos y seguir siendo correctos. Se evalúa que identifiques lo observado y no que reproduzcas las direcciones del ejemplo.

### Actividad C

El destino es `127.0.0.1`, el propio equipo. La prueba no necesita alcanzar el router. Las respuestas muestran que la prueba local de eco al loopback respondió. No demuestran funcionamiento del enlace Ethernet o Wi-Fi ni acceso a Internet.

### Actividad D

En la conversación local, ambos equipos pertenecen a la misma subred bajo la máscara dada y están en la misma VLAN. La función de conmutación permite el intercambio; Internet no es necesario.

El servidor de sucursal pertenece a otra subred. PC-A utiliza la ruta correspondiente; en el escenario simple, el gateway `192.168.10.1` recibe el tráfico remoto. La comunicación requiere enrutamiento. El servidor también necesita un camino de respuesta para completar la conversación.

### Actividad E

Una respuesta posible:

> Ambos clientes pueden imprimir localmente. Esto demuestra que la comunicación necesaria para esa impresión funciona, pero no valida todos los servicios de la LAN o de Internet. Probaré otro servicio externo y separaré la comprobación del nombre de la comprobación del destino para delimitar la falla.

También es válido empezar verificando alcance al gateway, siempre que expliques lo que esa prueba permite concluir y lo que no.

### Preguntas de la lección

1. Permite intercambiar información y utilizar servicios compartidos, como la aplicación de solicitudes.
2. El cliente solicita; el servidor ofrece un servicio. Son roles que pueden coexistir en un equipo.
3. La función de enrutamiento. Puede estar en un router o en un dispositivo de capa 3 adecuado.
4. Sí, si cliente e impresora conservan la conectividad y los servicios locales necesarios; la caída externa no implica por sí sola que ese recorrido local falle.
5. Delimita la parte de red de la dirección IPv4 y permite evaluar pertenencia a una subred.
6. No. Solo prueba ese intercambio con el loopback local.
7. No. Puede haber filtrado, un problema de camino u otra causa; falta evidencia para afirmar que está apagado.
8. **A router.** La frase pregunta qué dispositivo reenvía paquetes entre redes IP diferentes. Reconoce `forwards packets` y `different IP networks`.

## 11. Próximo paso

- [ ] Registrar si la práctica fue observación real o análisis de escenario.
- [ ] Enviar respuestas para retroalimentación.
- [ ] Anotar una confusión y una conclusión que ya puedes justificar.
- [ ] Si lo deseas, abrir el [curso oficial de introducción a Packet Tracer](https://www.netacad.com/courses/getting-started-cisco-packet-tracer/1000) para preparar los siguientes laboratorios.

## Referencias técnicas

- [Microsoft: ipconfig](https://learn.microsoft.com/es-es/windows-server/administration/windows-commands/ipconfig).
- [Microsoft: ping](https://learn.microsoft.com/es-es/windows-server/administration/windows-commands/ping).
- [Microsoft: comprobación de conectividad local](https://learn.microsoft.com/es-es/troubleshoot/windows-client/networking/ping-exe-check-microsoft-broadband-network-adapter).
- Las fuentes de conceptos de red están también en la lección S001.

---

Fin de la práctica S001.
