# CCNA S005 — Práctica: navegar y consultar IOS

**Fecha:** 17 de octubre de 2026.  
**Lección asociada:** `CCNA_S005_Leccion_Primer_contacto_con_IOS.md`.  
**Tiempo sugerido:** 40 minutos; instalación o acceso al simulador requieren tiempo adicional.  
**Objetivo:** 200-301 CCNA v2.0.  
**IDs pertinentes:** fundamento para 2.4 y apoyo indirecto a 1.6, sin acreditar su cobertura completa.  
**Escenario:** Campus Aurora, ficticio y aislado.

## 1. Plataforma, prerrequisitos y evidencia

Utiliza **Cisco Packet Tracer de escritorio**, con una **PC-PT** y un **switch 2960 nuevo**, en el espacio lógico. Anota la versión instalada en **Help → About** o su equivalente. La guía utiliza funciones básicas; no se afirma haber comprobado empíricamente una versión concreta.

Lee primero S005 y recupera R1–R3 de la lección. No necesitas configurar IP, crear una VLAN ni saber subnetting. Este laboratorio es independiente de las topologías anteriores: utiliza un escenario nuevo para conservarlas.

**El profesor no ejecutó este laboratorio al redactarlo.** Marca tus notas como:

- **Predicción:** resultado que esperas antes de actuar.
- **Observación en simulador:** comando o paso que tú ejecutaste en Packet Tracer y resultado observado.
- **Análisis didáctico:** interpretación de la transcripción ficticia del apartado 8.

No presentes un ejemplo como salida obtenida por ti. Si todavía no puedes ejecutar Packet Tracer, completa el análisis alternativo; la ejecución quedará sin confirmar.

Packet Tracer no implementa toda la CLI de IOS/IOS XE. Si una orden no aparece, comprueba modo, ayuda y modelo; documenta la limitación. S005 no requiere CML. Para funciones posteriores fuera del alcance del simulador se utilizará, cuando corresponda, un laboratorio autorizado de IOS XE/CML con imágenes oficiales. Todos los cambios de esta sesión se realizan en el escenario simulado.

| Actividad | Minutos | Objetivos de la lección |
| --- | ---: | --- |
| A. Reconocer prompts | 3 | O2 |
| B. Elegir ayuda | 4 | O3 |
| C. Consola, navegación y consulta | 24 | O1, O2, O3, O4, O6 |
| D. Diagnosticar errores | 6 | O5 |
| E. Explicar y entregar evidencia | 3 | O1, O4, inglés técnico |

## 2. Actividad A — Reconoce el contexto

Escribe el nombre del modo correspondiente a cada prompt:

| Código | Prompt | Modo |
| --- | --- | --- |
| A1 | `Switch>` | Por completar |
| A2 | `Switch#` | Por completar |
| A3 | `Switch(config)#` | Por completar |
| A4 | `Switch(config-if)#` | Por completar |
| A5 | `Switch(config-line)#` | Por completar |

**Evidencia:** cinco asociaciones. Observa el sufijo completo; no clasifiques cualquier línea con `#` como EXEC privilegiado.

## 3. Actividad B — Descubre la sintaxis

1. **B1:** necesitas descubrir qué palabras empiezan por `sh` y después qué opciones admite `show`. Escribe las dos solicitudes de ayuda y explica por qué el espacio cambia su significado.
2. **B2:** en EXEC privilegiado, un intento con `show ip` devuelve `% Incomplete command.`. ¿Qué escribirías para descubrir la parte que falta? Propón después una consulta completa que muestre un resumen de interfaces IP.

**Evidencia:** entradas exactas y explicación. Este apartado plantea un caso didáctico; aún no lo registres como ejecutado.

## 4. Actividad C — Acceso local a un switch

### C1. Escenario y conexión

**Scenario — English reading:**

You are preparing a lab switch for Campus Aurora. The switch has no management IP address. Your task is to inspect IOS, test a temporary hostname, and restore the original value.

**Apoyo en español:** prepararás un switch sin IP de administración. Lo consultarás mediante consola, cambiarás temporalmente su nombre y lo restaurarás. La conexión de consola permite trabajar antes de configurar conectividad IP.

**Topología textual:** un solo enlace de administración; no hay conexiones Ethernet de datos.

| Equipo | Puerto | Equipo | Puerto | Cable |
| --- | --- | --- | --- | --- |
| PC-A, una PC-PT | RS-232 | Switch de laboratorio, modelo 2960 | Console | Console, azul claro en Packet Tracer |

No corresponde asignar direcciones IP para este enlace serial. Deja IP, máscara, gateway y DNS sin configurar. El nombre del icono del switch puede ser `Switch0`; el hostname inicial en IOS suele ser `Switch`. Son referencias distintas: registra lo que muestre la CLI.

1. Añade la PC y el switch en un escenario nuevo. Trabaja en **Realtime** y espera a que termine el arranque del switch.
2. **English instruction 1: Connect PC-A to the switch console port.** Selecciona **Connections → Console**, pulsa PC-A y elige **RS-232**; conecta el otro extremo al puerto **Console** del switch.
3. En **PC-A → Desktop → Terminal**, comprueba estos valores:

   | Parámetro | Valor del ejercicio |
   | --- | --- |
   | Bits Per Second | 9600 |
   | Data Bits | 8 |
   | Parity | None |
   | Stop Bits | 1 |
   | Flow Control | None |

4. Pulsa **OK** y después Enter si aparece `Press RETURN to get started!`. Si el equipo nuevo pregunta `Would you like to enter the initial configuration dialog? [yes/no]:`, responde `no` y continúa hasta el prompt. No necesitas el asistente para esta actividad.
5. Anota el prompt completo. Si pide una contraseña desconocida, comprueba que elegiste un switch nuevo del laboratorio; no intentes recuperar acceso a equipos ajenos. Si la ventana está vacía, consulta la tabla de diagnóstico del apartado 7.

**Evidencia C1:** versión de Packet Tracer, modelo elegido, puertos conectados, parámetros seriales y primer prompt. Explica en una oración por qué no configuraste IP.

### C2. Recorrer los modos

**English instruction 2: Read the prompt before each command.** Antes de escribir cada orden, identifica el modo y predice el sufijo que aparecerá después.

Desde `Switch>`, escribe **una línea a la vez**, sin incluir el prompt:

```text
enable
configure terminal
interface FastEthernet0/1
exit
line console 0
end
disable
```

No escribas ningún comando de configuración adicional dentro de interfaz o línea. Entrar en esos contextos y salir permite observar la navegación. Si el modelo no tiene `FastEthernet0/1`, comprueba la selección del 2960; si trabajas con otra interfaz válida, documenta la equivalencia.

Completa:

| Comando | Modo anterior | Prompt resultante observado |
| --- | --- | --- |
| `enable` | Por completar | Por completar |
| `configure terminal` | Por completar | Por completar |
| `interface FastEthernet0/1` | Por completar | Por completar |
| `exit` | Por completar | Por completar |
| `line console 0` | Por completar | Por completar |
| `end` | Por completar | Por completar |
| `disable` | Por completar | Por completar |

**Evidencia C2:** tabla y diferencia entre `exit` y `end`. El recorrido debe terminar de nuevo en EXEC de usuario.

### C3. Explorar la ayuda

1. Desde EXEC de usuario escribe `?`. Anota dos comandos que aparezcan. La lista puede variar entre plataformas; no hay un número fijo obligatorio.
2. Ejecuta `enable`. Ya en EXEC privilegiado, consulta por separado `sh?`, `show ?` y `show ip ?`.
3. Después de cada ayuda, revisa la línea pendiente y bórrala con Backspace antes de empezar otra. El signo `?` normalmente muestra ayuda sin pulsar Enter.
4. Escribe `show ver` y pulsa la tecla Tab. Anota si completa `show version`. Luego ejecuta la orden completa con Enter. Si Tab no funciona en tu interfaz, escríbela entera y registra esa limitación.

**Evidencia C3:** un ejemplo de ayuda sin espacio, otro con espacio y el resultado de Tab. No copies la lista completa de comandos.

### C4. Consultar y registrar el estado inicial

Desde EXEC privilegiado ejecuta por separado:

```text
show version
show running-config
show startup-config
show ip interface brief
```

Si aparece `--More--`, avanza con la barra espaciadora, o línea a línea con Enter. Puedes salir del paginador con `q` cuando esté admitido. Espera al prompt antes de escribir la siguiente consulta.

Registra únicamente la evidencia pertinente:

| Dato | Qué debes anotar |
| --- | --- |
| Software y dispositivo | Versión y modelo que muestre `show version`; son datos del dispositivo simulado. |
| Nombre original | Línea `hostname` de la configuración activa o nombre mostrado en el prompt; conservar mayúsculas y minúsculas. |
| Configuración de arranque | Si existe; si existe, su hostname, y si no, el mensaje de ausencia observado. |
| Interfaz | Nombre, `IP-Address`, `Status` y `Protocol` de FastEthernet0/1 o su equivalente. |

En un puerto sin cable de datos es razonable esperar `down/down`; `unassigned` indica ausencia de IP en esa interfaz. No se espera un enlace Ethernet activo porque solo conectaste consola. Anota tus valores reales si difieren y explica el contexto.

**Evidencia C4:** cuatro datos y una conclusión limitada: ¿qué permite confirmar cada consulta y qué no demuestra sobre toda la red?

### C5. Cambiar un nombre, verificarlo y revertirlo

Comprueba que anotaste el nombre original antes de continuar. En este procedimiento se supone que era `Switch`; si era otro, utiliza ese valor exacto en la restauración.

1. Predice qué cambiará al asignar `AURORA-SW1`: ¿el prompt, una dirección IP o la configuración de arranque?
2. Desde EXEC privilegiado ejecuta:

   ```text
   configure terminal
   hostname AURORA-SW1
   end
   show running-config
   show startup-config
   ```

3. Busca el nuevo hostname en la configuración activa y en el prompt. Compara la configuración de arranque con C4. No ejecutes una orden de guardado; este cambio es temporal.
4. **English instruction 3: Restore the original hostname and verify the result.** Restaura el nombre inicial, comprueba el prompt y consulta de nuevo. Si el valor inicial era `Switch`, escribe:

   ```text
   configure terminal
   hostname Switch
   end
   show running-config
   show startup-config
   ```

5. Registra el hostname restaurado y el estado de la configuración de arranque. Si no existía al inicio, debe continuar ausente. Si existía, compara el hostname y el hecho de no haber ejecutado un guardado; no afirmes igualdad byte a byte sin comprobarla.

**Evidencia C5:** estado inicial, cambio observado y restauración verificada. Escribir `end` no revierte ni guarda el hostname. La reversión es asignar explícitamente el valor original.

## 5. Actividad D — Errores de CLI y diagnóstico

### D1. Contexto equivocado

Desde EXEC privilegiado, entra con `configure terminal` e intenta `show version`. En la CLI habitual del ejercicio se espera un rechazo porque es una consulta EXEC introducida en configuración.

1. Copia el prompt, la entrada y el mensaje que realmente recibas. Si tu implementación la admite, registra la diferencia y utiliza el caso didáctico del apartado 8 para analizar el error; no inventes un rechazo.
2. Explica por qué este resultado no prueba una falla de cable ni exige reiniciar.
3. Vuelve con `end`, ejecuta `show version` y compara. Termina en EXEC privilegiado, con el hostname original.

### D2. Orden incompleta

Desde EXEC privilegiado intenta `show ip`. Se espera un mensaje de orden incompleta en el modelo del ejercicio. Utiliza `show ip ?` para descubrir una continuación y termina con `show ip interface brief`.

Anota el error y su solución. Si el comportamiento de tu plataforma es distinto, documenta la diferencia y resuelve el caso B2 como análisis. La finalidad es usar la ayuda, no forzar una frase de error idéntica.

**Evidencia D:** síntoma, hipótesis, corrección y prueba posterior. Separa los errores de navegación de cualquier cambio de configuración: estas consultas rechazadas no configuran una IP ni levantan una interfaz.

## 6. Actividad E — Inglés técnico y entrega

Escribe **dos o tres oraciones en inglés** que expliquen cómo accediste y qué consulta utilizaste. Si trabajaste con datos didácticos, usa expresiones de análisis o intención; no afirmes haber ejecutado el laboratorio.

Apóyate en estas estructuras:

- Observación: `I used ... to access ...` y `The command ... displayed ...`.
- Análisis: `A console connection allows ...` y `I would use ... to display ...`.
- Vocabulario útil: `console port`, `management IP address`, `prompt`, `show version`.

La respuesta modelo aparece al final. Se evaluará por separado el contenido técnico y la claridad del inglés; un error gramatical que no cambie la idea no se convierte en un error de redes.

**Desafío opcional, sin puntos:** partiendo de `Switch(config-line)#`, escribe la ruta para consultar la versión, volver a configuración global y terminar en EXEC de usuario. No cambies parámetros. Explica por qué `disable` no es el primer paso desde configuración de línea.

Entrega en texto o Markdown:

1. Modalidad: observación en Packet Tracer o análisis didáctico; versión del simulador si lo utilizaste.
2. R1–R3, A1–A5 y B1–B2.
3. Evidencia C1–C5 y diagnóstico D1–D2.
4. Explicación breve E y respuestas Q1–Q8 de la lección.
5. Una duda técnica y, si corresponde, una palabra o frase en inglés que dificultó la comprensión.

No necesitas entregar un archivo de simulador, imágenes ni audio. Esta sesión produce únicamente sus dos Markdown.

## 7. Fallas de preparación, comprobación y cierre

| Síntoma | Hipótesis | Prueba o corrección delimitada |
| --- | --- | --- |
| Terminal sin prompt | Arranque pendiente, cable o puerto equivocados | Esperar el arranque, pulsar Enter y verificar RS-232 a Console. |
| Abriste Command Prompt en la PC y las órdenes IOS fallan | Aplicación incorrecta | Abrir Desktop → Terminal; la consola de la PC y la CLI del switch son contextos distintos. |
| Texto ilegible o falta de respuesta | Parámetros seriales incorrectos | Comparar con 9600, 8, None, 1, None en este laboratorio. |
| `configure terminal` falla desde `Switch>` | Falta entrar en EXEC privilegiado | Usar `enable`, comprobar `Switch#` y repetir. |
| Una orden válida en una guía no aparece | Modo, modelo, versión o límite del simulador | Revisar prompt, `?`, modelo y `show version`; registrar la diferencia. |
| La salida parece detenerse en `--More--` | Paginación | Usar Espacio/Enter y esperar el prompt. |
| Cambió el nombre del icono, pero no el de IOS | Se editó una etiqueta de la interfaz gráfica | Verificar el hostname en la CLI y en `show running-config`. |

**Método:** observar el contexto, formular una hipótesis, corregir una variable y repetir la misma prueba. No cambies direccionamiento para arreglar una conexión serial.

**Verificación final:**

- Hostname original restaurado y comprobado en prompt/configuración activa.
- Estado de la configuración de arranque documentado antes y después.
- Ningún parámetro de interfaz o consola modificado durante la navegación.
- Versión, consultas, errores y limitaciones anotados como evidencia o análisis, según corresponda.
- Al terminar puedes cerrar Terminal y el escenario temporal; no sobrescribas tus laboratorios anteriores. El informe escrito conserva el aprendizaje aunque no guardes un `.pkt`.

Si realizaste un cambio adicional no previsto, anótalo y restaura su valor original antes de marcar la reversión como completada. No marques una comprobación pendiente como exitosa.

## 8. Alternativa didáctica sin ejecución

Todo este apartado contiene **datos ficticios**, no una captura ni una sesión ejecutada por el profesor. Si no tienes acceso al simulador, analiza el siguiente recorrido y completa las preguntas indicando “análisis didáctico”.

```text
Switch> enable
Switch# configure terminal
Switch(config)# hostname AURORA-SW1
AURORA-SW1(config)# end
AURORA-SW1#
```

El conjunto ficticio de evidencias es:

| Momento | Prompt EXEC privilegiado | Hostname en running-config | Startup-config |
| --- | --- | --- | --- |
| Inicial | `Switch#` | `Switch` | No existe |
| Tras cambiar nombre | `AURORA-SW1#` | `AURORA-SW1` | No existe |
| Tras restaurar | `Switch#` | `Switch` | No existe |

También se proporciona esta fila ficticia de `show ip interface brief`:

```text
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/1         unassigned      YES unset  down                  down
```

Y estos errores para D:

- Desde `Switch(config)#`, `show version` es rechazado con `% Invalid input detected at '^' marker.`; desde `Switch#`, la consulta devuelve información de software.
- Desde `Switch#`, `show ip` devuelve `% Incomplete command.`; `show ip ?` enumera opciones, incluida `interface`, y la consulta completa permite obtener el resumen.

No se proporciona un modelo exacto de hardware, número de versión ni lista completa de ayuda: escribe “no suministrado” donde corresponda. No inventes esos campos. Reconstruye los comandos de restauración y explica por qué el puerto Ethernet puede estar caído mientras funciona la consola.

## 9. Rúbrica

| Criterio | Puntos | Evidencia requerida |
| --- | ---: | --- |
| A: cinco modos | 5 | Un punto por cada asociación correcta. |
| B: ayuda contextual | 4 | Dos por distinguir las solicitudes de B1; dos por ayuda y consulta completa en B2. |
| C1: acceso por consola | 3 | Enlace correcto, parámetros y explicación de por qué no requiere IP. |
| C2: recorrido de modos | 3 | Prompts coherentes, diferencia entre exit/end y retorno previsto. |
| C3–C4: ayuda y consultas | 4 | Uso razonado de ayuda/Tab, versión, configuración e interpretación de interfaz, con límites de evidencia. |
| C5: cambio y reversión | 4 | Estado inicial, cambio comprobado, restauración y distinción respecto de startup-config. |
| D: diagnóstico | 5 | Tres por D1: contexto, corrección y verificación; dos por D2: ayuda y consulta completa. |
| E: explicación técnica breve | 2 | Acceso y consulta técnicamente correctos, expresados en inglés; observaciones lingüísticas aparte. |
| **Total** | **30** | **24/30 = 80%, criterio interno de entrenamiento.** |

R1–R3, Q1–Q8 y el desafío aportan retroalimentación adicional y no duplican puntos. Los umbrales del plan son internos, nunca notas oficiales de aprobación de Cisco.

Si trabajas con el apartado 8, evalúa lo que pueda deducirse y marca como no suministrados los datos ausentes. Una puntuación conceptual no acredita ejecución. Las limitaciones del simulador no se confunden con errores técnicos cuando están documentadas y el razonamiento es correcto.

Si al corregir hay errores, identificaremos si proceden del concepto, del manejo de la CLI o de la lectura en inglés. No aumentaremos automáticamente la proporción de inglés sin evidencia de comprensión.

## 10. Referencias

Consultadas el **17/10/2026**. Las preguntas y el escenario de Campus Aurora son originales.

- [Cisco: temario oficial CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf), apoyo curricular a 2.4 y 1.6.
- [Cisco: transición de versiones](https://blogs.cisco.com/learning/stay-on-track-get-certified-before-the-ccna-refresh): v1.1 disponible hasta el 02/02/2027; v2.0, objetivo de este programa, anunciada desde el 03/02/2027.
- [Cisco Networking Academy: Navigate the IOS en Packet Tracer](https://contenthub.netacad.com/courses/itn-dl/_common/2.3.7-packet-tracer---navigate-the-ios.pdf), conexión de consola y ayuda.
- [Cisco Networking Academy: Terminal y parámetros de consola](https://contenthub.netacad.com/courses/itn-dl/_common/2.3.8-packet-tracer---navigate-the-ios-using-a-terminal-client-for-console-connectivity---physical-mode.pdf). La referencia utiliza modo físico; nuestro escenario nuevo usa la vista lógica y el mismo tipo de conexión.
- [Cisco: modos CLI del Catalyst 2960, IOS 15.2(2)E](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/15-2_2_e/command/reference/cr_2960/intro.html).
- [Cisco: ayuda y errores CLI](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swcli.html), [hostname](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swadmin.html) y [configuración activa y de arranque](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swiosfs.html).

---

## 11. Soluciones razonadas — consultar después del intento

### Recuperación R1–R3

- **R1:** la MAC de destino identifica al destinatario de la trama en el contexto del enlace.
- **R2:** no. Ping utiliza ICMP; no establece TCP ni solicita una página HTTP.
- **R3:** un puerto TCP es un identificador de transporte; una interfaz física es un punto de conexión del equipo. El mismo término “puerto” aparece con sentidos diferentes.

### Actividad A

| Caso | Modo correcto |
| --- | --- |
| A1 | EXEC de usuario, User EXEC |
| A2 | EXEC privilegiado, Privileged EXEC |
| A3 | Configuración global, Global configuration |
| A4 | Configuración de interfaz, Interface configuration |
| A5 | Configuración de línea, Line configuration |

### Actividad B

- **B1:** `sh?` busca continuaciones de la palabra; `show ?` busca opciones después del comando completo. El espacio separa los elementos de la orden.
- **B2:** `show ip ?` muestra las opciones siguientes. Una orden completa pertinente es `show ip interface brief`; “incomplete” indica que faltan elementos, no que la interfaz IP esté necesariamente averiada.

### Actividad C

**C1:** la PC se conecta por RS-232 al puerto Console con el cable de consola. El Terminal utiliza 9600, 8, None, 1, None. No hay transporte TCP/IP en este enlace serial directo. La versión instalada de Packet Tracer y el primer prompt deben proceder de tu observación; no se suministran valores reales en las soluciones.

**C2, recorrido esperado con hostname inicial `Switch`:**

| Comando | Modo anterior | Prompt resultante |
| --- | --- | --- |
| `enable` | EXEC de usuario | `Switch#` |
| `configure terminal` | EXEC privilegiado | `Switch(config)#` |
| `interface FastEthernet0/1` | Configuración global | `Switch(config-if)#` |
| `exit` | Configuración de interfaz | `Switch(config)#` |
| `line console 0` | Configuración global | `Switch(config-line)#` |
| `end` | Configuración de línea | `Switch#` |
| `disable` | EXEC privilegiado | `Switch>` |

Entrar en un contexto no equivale a cambiar todos sus parámetros. `exit` retrocede desde interfaz a global; `end` vuelve desde configuración directamente a EXEC privilegiado.

**C3:** las listas concretas dependen del modo y la implementación. Debes demostrar que distingues una ayuda sobre la palabra de una ayuda sobre el siguiente argumento. Tab puede completar `version` cuando resulta inequívoco; si no funciona en tu interfaz, la alternativa correcta es escribir el comando completo, no inventar una captura.

**C4:** `show version` informa sobre software/modelo; `show running-config`, sobre configuración activa; `show startup-config`, sobre configuración guardada o su ausencia; `show ip interface brief`, sobre el resumen de interfaces. Un puerto Ethernet `down/down` sin cable es coherente con este escenario y no impide usar la consola. Ninguna consulta aislada valida todas las aplicaciones de la red.

**C5:** cambia el prompt y la línea hostname activa. No se configura una IP ni se guarda automáticamente startup-config. Con nombre original `Switch`, la secuencia de restauración desde EXEC privilegiado es:

```text
configure terminal
hostname Switch
end
show running-config
show startup-config
```

La evidencia final debe mostrar el nombre original y documentar el estado de arranque. La ausencia de un archivo startup-config es válida si también estaba ausente inicialmente. `end` no es una orden de reversión ni de guardado.

### Actividad D

**D1:** la hipótesis inicial es un contexto inadecuado para la consulta. Leer `(config)#` orienta el diagnóstico. Con `end` vuelves a EXEC privilegiado y con `show version` verificas la consulta. Este resultado local no permite atribuir la falla al cable ni al encaminamiento IP. Si tu implementación admite la orden original, explica esa observación y analiza el caso ficticio por separado.

**D2:** el intérprete necesita más información después de `show ip`. `show ip ?` permite descubrir opciones; `show ip interface brief` completa la consulta. La corrección consiste en completar sintaxis, no en reiniciar ni configurar una dirección IP.

### Actividad E y desafío

**Modelo para quien sí lo observó:** “I used a console connection to access the switch without a management IP address. The show version command displayed information about the simulated software.”

**Modelo de análisis sin ejecución:** “A console connection allows local access without a management IP address. I would use show version to display information about the device software.”

Adapta la primera versión solo si describe lo que hiciste. En una corrección, confundir *console port* con *TCP port* es un error técnico; una preposición imperfecta con significado claro se trata como una observación de inglés.

**Desafío:** desde configuración de línea, una ruta válida es:

```text
end
show version
configure terminal
end
disable
```

`disable` se utiliza desde EXEC privilegiado; primero debes salir del contexto de configuración. El paso de volver a configuración global satisface el recorrido pedido y no cambia parámetros por sí solo.

### Preguntas Q1–Q8 de la lección

1. **Q1:** la consola del ejercicio es un enlace serial local, independiente del direccionamiento IP y del acceso por Ethernet.
2. **Q2 — Traducción:** “¿Qué comando te lleva desde `Switch>` a EXEC privilegiado y qué prompt esperas?”. **Respuesta:** `enable`; el prompt esperado es `Switch#`. Se refiere al modo, no a activar una interfaz.
3. **Q3:** desde configuración de interfaz, `exit` lleva a `Switch(config)#`; `end`, a `Switch#`. Ninguno guarda ni deshace por sí solo cambios previos.
4. **Q4:** `sh?` busca completar una palabra; `show ?` busca opciones posteriores. El prompt lo muestra el equipo y no se escribe como parte del comando.
5. **Q5 — Traducción:** “¿Salir del modo de configuración con `end` guarda la configuración activa en la configuración de arranque? Explica”. **Respuesta:** no; solo cambia el modo. Guardar requiere una acción explícita, como `copy running-config startup-config`, que no se ejecuta en este ejercicio temporal.
6. **Q6:** no hay contradicción. El puerto Ethernet carece de enlace de datos; la consola es otro canal de administración.
7. **Q7:** revisaría el prompt/modo, la sintaxis completa con ayuda contextual y si el modelo/versión admite esa orden. Un mensaje del intérprete no demuestra por sí solo una avería del equipo.
8. **Q8 — Traducción:** “Cambiaste el hostname y después restauraste su valor original. ¿Qué evidencia recogerías para verificar la restauración?”. **Respuesta:** hostname inicial anotado, prompt final y línea correspondiente de running-config, además de comparar el estado de startup-config antes y después. Si falta una comprobación, se registra pendiente.

---

**Estado:** material preparado; estudio, ejecución, nivel de inglés y dominio de redes pendientes de evidencia del estudiante.

Fin de la práctica S005.
