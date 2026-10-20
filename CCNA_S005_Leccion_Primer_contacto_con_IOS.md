# CCNA S005 — Primer contacto con IOS: consola, modos, ayuda y consulta

**Fecha:** 17 de octubre de 2026.  
**Tiempo sugerido:** 35 minutos de lección y 40 de práctica.  
**Nivel:** desde cero; semana 1.  
**Proyecto ficticio:** Campus Aurora.  
**Práctica asociada:** `CCNA_S005_Practica_Navegar_y_consultar_IOS.md`.  
**Examen objetivo:** 200-301 CCNA v2.0.

## 1. Vigencia, objetivos y prerrequisitos

**Disponibilidad verificada el 17/10/2026:** v1.1 sigue siendo el examen disponible, con último día anunciado el **02/02/2027**. Cisco publicó el temario de v2.0 y anunció su examen desde el **03/02/2027**. Estudiamos para **v2.0**; no se cambió el objetivo del programa [1, 2].

| Referencia v2.0 | Alcance de S005 |
| --- | --- |
| **2.4** | Fundamento para consultar el estado del dispositivo y utilizar comandos de diagnóstico. Hoy reconoceremos modos y consultas básicas; no cubrimos todavía todo el objetivo. |
| **1.6** | Apoyo indirecto: separar el acceso administrativo por consola de la conectividad IP de un cliente. Hoy no configuramos ni diagnosticamos Windows, macOS o Linux. |

La navegación básica de IOS es un prerrequisito del curso, no un sub-ID oficial inventado. El acceso seguro, las VLAN y la configuración detallada de interfaces se desarrollan más adelante.

Al terminar deberías poder:

1. **O1:** explicar por qué la consola serial del laboratorio funciona sin una dirección IP.
2. **O2:** identificar cinco modos a partir del prompt y elegir el comando para entrar o salir.
3. **O3:** utilizar `?`, el espacio y Tab para descubrir sintaxis sin adivinarla.
4. **O4:** seleccionar consultas para conocer versión, configuración e interfaces y delimitar qué demuestran.
5. **O5:** diferenciar un error de sintaxis de una falla de conectividad.
6. **O6:** verificar y revertir un cambio mínimo de hostname, distinguiendo configuración activa y de arranque.

**Prerrequisitos:** S001–S004: equipo, interfaz, cliente, capas, Ethernet e IP. No suponemos que las has estudiado por haber recibido los archivos. Si todavía estás retomando S003, estúdiala antes de S004 y S005. No necesitas saber subnetting ni memorizar comandos previos.

Desde esta sesión incorporamos más inglés: vocabulario con ejemplos y tres de las ocho preguntas de comprensión en inglés. Las explicaciones siguen en español. Las traducciones de las preguntas y todas sus soluciones aparecen al final de la práctica.

## 2. Recuerdo activo

Antes de leer, responde brevemente:

- **R1:** ¿Qué identificador usa una trama Ethernet para su destino en la LAN?
- **R2:** ¿Un ping exitoso demuestra que HTTP funciona? ¿Por qué?
- **R3:** ¿Qué diferencia hay entre un puerto TCP y una interfaz física del switch?

Estas preguntas recuperan S003 y S004; no requieren haber utilizado IOS.

## 3. IOS y CLI: software e interfaz de administración

**Cisco IOS** es software de sistema empleado en familias de equipos Cisco. **IOS XE** es otra plataforma de software que comparte muchos comandos, pero no es idéntica. El modelo y la versión determinan qué funciones y sintaxis existen.

Una **CLI**, *command-line interface*, permite administrar mediante texto. Escribes un comando, el equipo lo interpreta y muestra una respuesta o cambia un ajuste. La CLI no es una aplicación de mensajería ni un editor de código: cada orden se interpreta dentro de un contexto.

En Campus Aurora necesitas consultar un switch recién incorporado al laboratorio. Primero averiguas cómo acceder, en qué modo estás y qué consulta responde a tu pregunta. Aprender esa secuencia es más útil que pegar una lista sin entenderla.

Packet Tracer simula dispositivos y una parte de su CLI. El texto de `show version` dentro del simulador describe el dispositivo simulado; no demuestra que esté ejecutándose una imagen completa de IOS o IOS XE.

## 4. Consola: acceder sin depender de IP

En el laboratorio conectaremos **RS-232 de una PC simulada** al **puerto Console de un switch 2960**, usando el cable Console de Packet Tracer. La aplicación **Desktop → Terminal** presenta el diálogo con el switch [3, 4].

| Elemento | Función |
| --- | --- |
| Puerto Console | Entrada de administración local del switch. |
| Cable Console | Enlace serial entre terminal y dispositivo. |
| Aplicación Terminal | Envía pulsaciones y muestra la salida de la consola. |
| Dirección IP | No es necesaria para este enlace serial directo. |

La consola del ejercicio no es una sesión TCP ni transmite comandos mediante Ethernet. Aunque un conector de consola pueda parecerse al de una interfaz Ethernet, sus funciones son distintas. Más adelante accederemos por la red; ese acceso sí tendrá otros requisitos.

Los parámetros del Terminal de Packet Tracer para esta práctica son **9600 bits por segundo, 8 bits de datos, sin paridad, 1 bit de parada y sin control de flujo** [3]. Es una configuración del canal serial, no la velocidad Ethernet del switch. En un equipo físico se comprueba la documentación del modelo y la configuración vigente.

## 5. El prompt te dice dónde estás

Observa esta línea:

```text
Switch> enable
```

`Switch>` es el **prompt** que muestra el dispositivo. Tú escribes únicamente `enable`. El nombre anterior al símbolo identifica al equipo dentro de la sesión; el sufijo indica el modo.

| Modo | Prompt de ejemplo | Entrada habitual | Salida que utilizaremos |
| --- | --- | --- | --- |
| User EXEC: EXEC de usuario | `Switch>` | Abrir la sesión | `enable` para subir a EXEC privilegiado |
| Privileged EXEC: EXEC privilegiado | `Switch#` | `enable` desde `>` | `disable` para volver a `>` |
| Global configuration: configuración global | `Switch(config)#` | `configure terminal` desde `#` | `end` para volver a EXEC privilegiado |
| Interface configuration: configuración de interfaz | `Switch(config-if)#` | `interface FastEthernet0/1` desde configuración global | `exit` para volver a configuración global |
| Line configuration: configuración de línea | `Switch(config-line)#` | `line console 0` desde configuración global | `exit` para volver a configuración global |

Los ejemplos corresponden al switch del ejercicio [5]. En otro equipo debes verificar el nombre de interfaz antes de seleccionarla. Una *line* de administración, como `console 0`, no es un puerto Ethernet.

**El símbolo `#` por sí solo no basta:** también aparece en los modos de configuración. Lee el prompt completo. `Switch#` y `Switch(config)#` aceptan órdenes diferentes.

En un equipo configurado, `enable` puede pedir una contraseña. No presupongas acceso libre porque el switch nuevo de Packet Tracer lo permita. En esta sesión utilizaremos exclusivamente ese dispositivo nuevo de laboratorio.

## 6. Navegar sin cambiar parámetros

Esta es una **transcripción didáctica**, no una ejecución realizada por el profesor:

```text
Switch> enable
Switch# configure terminal
Switch(config)# interface FastEthernet0/1
Switch(config-if)# exit
Switch(config)# line console 0
Switch(config-line)# end
Switch# disable
Switch>
```

En esta secuencia seleccionas contextos y vuelves a salir. No introduces comandos que cambien el estado del puerto o los parámetros de consola.

- `exit` sale del contexto actual. Desde configuración de interfaz vuelve a configuración global; desde configuración global vuelve a EXEC privilegiado. En EXEC puede cerrar la sesión, por lo que no es una salida universal a un mismo modo.
- `end`, desde los modos de configuración, vuelve directamente a EXEC privilegiado. **No guarda ni deshace cambios.**
- `disable` vuelve de EXEC privilegiado a EXEC de usuario.

Por ahora usa los nombres completos. Abreviaturas como `conf t` pueden ser válidas si son inequívocas en ese contexto, pero aprenderlas antes de entender el comando completo dificulta diagnosticar errores.

## 7. Pedir ayuda sin ejecutar a ciegas

IOS dispone de ayuda contextual: muestra opciones que corresponden al modo y al texto que llevas escrito [6].

| Entrada | Qué preguntas al equipo |
| --- | --- |
| `?` | ¿Qué comandos existen en este modo? |
| `sh?` | ¿Qué palabras disponibles empiezan por `sh`? |
| `show ?` | ¿Qué opciones pueden seguir al comando `show`? |
| `show ip ?` | ¿Qué opciones puedo consultar dentro de `show ip`? |
| `show ip interface ?` | ¿Qué argumento u opción admite esta consulta? |

**El espacio importa:** `sh?` completa una palabra; `show ?` busca la siguiente. El signo de interrogación normalmente muestra ayuda inmediatamente, sin Enter. Después de verla, puede quedar texto en la línea: bórralo con Backspace antes de iniciar una consulta diferente.

Para practicar Tab, escribe `show ver` y pulsa la **tecla Tab**, no las letras “Tab”. Si la palabra es inequívoca y el terminal admite la tecla, se completa a `show version`. Enter ejecuta la orden completa. Si Tab no responde, escribe la orden entera y registra la limitación de la interfaz.

En la ayuda, **`<cr>`** significa que puedes terminar la orden con Enter. No se escribe literalmente. Una indicación como `WORD` suele pedir un valor; tampoco debes copiarla como si fuera siempre el valor adecuado.

No conviertas los ejemplos de ayuda en una lista para pegar: consulta una línea, observa qué devuelve y decide el siguiente paso.

## 8. Consultar con una pregunta concreta

Para evitar ambigüedades de acceso, ejecutaremos las siguientes consultas desde **EXEC privilegiado**, con prompt `Switch#`.

| Pregunta | Comando | Evidencia que debes extraer |
| --- | --- | --- |
| ¿Qué software y modelo presenta este dispositivo? | `show version` | Línea de versión y modelo, si están disponibles. |
| ¿Qué ajustes están activos? | `show running-config` | Por ejemplo, la línea `hostname`. |
| ¿Qué configuración está guardada para el arranque? | `show startup-config` | Contenido guardado o indicación de que no existe. |
| ¿Qué direcciones y estados resume el equipo por interfaz? | `show ip interface brief` | Interfaz, IP y columnas `Status` y `Protocol`. |

La primera consulta también suele estar disponible en EXEC de usuario del modelo de laboratorio. Que un comando funcione en un modo no significa que todos los comandos `show` estén autorizados allí.

Si aparece `--More--`, la salida está paginada. La barra espaciadora permite avanzar una página; Enter suele avanzar una línea; `q` permite salir del paginador cuando está admitido. No confundas el paginador con un bloqueo del switch.

**Ejemplo didáctico reducido** de una interfaz Ethernet sin cable de datos:

```text
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/1         unassigned      YES unset  down                  down
```

`unassigned` significa que esa interfaz no tiene una dirección IP asignada. En un puerto de switch utilizado en capa 2 eso no implica por sí solo una avería. `down/down` es coherente con un puerto físico sin enlace en nuestro escenario. **La consola puede seguir funcionando**, porque es otro acceso. No interpretes una sola fila como prueba de la salud de toda la red.

El formato, las interfaces y algunos valores pueden variar. Copia lo que observas y explica sus límites; no copies esta fila como evidencia de una ejecución propia.

## 9. Configuración activa, arranque y cambio reversible

La **running configuration** describe los ajustes activos. La **startup configuration** contiene la configuración guardada que se utiliza al arrancar. Pueden ser diferentes [7].

En IOS, un comando de configuración aceptado normalmente puede afectar al estado activo inmediatamente. Salir con `end` no equivale a aplicar, guardar o cancelar un formulario.

Haremos un único cambio: el nombre del switch. En un dispositivo nuevo cuyo nombre inicial es `Switch`, escribe desde EXEC privilegiado:

```text
configure terminal
hostname AURORA-SW1
end
show running-config
```

Después de `hostname`, el prompt debe incorporar `AURORA-SW1`. Busca también esa línea en la configuración activa. El comando establece un nombre, **no una dirección IP** [8]. No confundas el nombre del icono de Packet Tracer con la comprobación del hostname en IOS.

Para restaurar el nombre inicial `Switch`, desde EXEC privilegiado:

```text
configure terminal
hostname Switch
end
show running-config
```

Si el nombre original era otro, la reversión consiste en restaurar ese valor exacto. Por eso se registra el estado inicial antes de cambiar algo.

Como referencia, `copy running-config startup-config` guarda la configuración activa como configuración de arranque. **No lo ejecutaremos en esta práctica**, cuyo cambio es temporal. Consultar `show startup-config` antes y después permite documentar que el cambio de hostname no se guardó allí. Si el archivo no existía al inicio, esa ausencia también es un resultado válido que debes registrar.

## 10. Un error de comando no es una falla de red

| Mensaje | Qué investigar | Cómo avanzar |
| --- | --- | --- |
| `% Incomplete command.` | Faltan argumentos u opciones. | Añadir un espacio y `?` a la parte válida. |
| `% Ambiguous command` | La abreviatura puede corresponder a más de una orden. | Escribir más caracteres o el nombre completo. |
| `% Invalid input detected at '^' marker.` | Sintaxis, contexto o función no admitida. | Mirar prompt y marcador, consultar ayuda y comprobar plataforma. |

El marcador `^` orienta hacia la parte que el intérprete no pudo aceptar. No demuestra por sí solo que un cable o el software estén averiados.

Por ejemplo, desde `Switch(config)#`, intentar `show version` normalmente no corresponde a ese modo. En esta sesión volveremos con `end` y ejecutaremos `show version` en `Switch#`. En plataformas que lo admiten, `do show version` permite una consulta EXEC desde configuración, pero no es necesario para completar S005.

Un error conceptual frecuente es “si falta un comando, el equipo está mal”. Primero compara modo, nombre completo, modelo y versión. Packet Tracer no implementa todas las funciones documentadas para hardware real.

## 11. Vocabulario: ocho expresiones con uso

| Inglés | Español | Ejemplo en inglés |
| --- | --- | --- |
| Prompt | Indicador del equipo y modo actual | Read the prompt before typing a command. |
| Console port | Puerto de consola | The PC is connected to the console port. |
| Privileged EXEC mode | Modo EXEC privilegiado | Use enable to enter privileged EXEC mode. |
| Global configuration mode | Modo de configuración global | The hostname is changed in global configuration mode. |
| Context-sensitive help | Ayuda dependiente del contexto | Use context-sensitive help to check the syntax. |
| Running configuration | Configuración activa | The running configuration contains the current hostname. |
| Startup configuration | Configuración de arranque | The startup configuration has not been changed. |
| Incomplete command | Comando al que le faltan elementos | An incomplete command needs more information. |

## 12. Ocho preguntas de comprensión

Responde antes de consultar la sección final de soluciones de la práctica. **Q2, Q5 y Q8 están en inglés**. Puedes redactar el razonamiento técnico en español; la actividad breve de expresión en inglés está en la práctica.

1. **Q1:** ¿Por qué no necesitas configurar una IP en la PC para administrar el switch por la consola serial de este ejercicio?
2. **Q2:** Which command takes you from `Switch>` to privileged EXEC mode, and what prompt should you expect?
3. **Q3:** Estás en `Switch(config-if)#`. ¿A dónde te lleva `exit` y a dónde te lleva `end`?
4. **Q4:** Explica la diferencia entre escribir `sh?` y `show ?`. ¿Debes copiar el prompt al introducir un comando?
5. **Q5:** Does leaving configuration mode with `end` save the running configuration to the startup configuration? Explain.
6. **Q6:** Un puerto FastEthernet sin cable aparece `down/down`, pero puedes consultar IOS por consola. ¿Es contradictorio? Justifica.
7. **Q7:** Intentas una consulta, IOS la rechaza y propones reiniciar el switch. ¿Qué tres comprobaciones harías antes de concluir que el dispositivo falla?
8. **Q8:** You changed the hostname and then restored its original value. What evidence would you collect to verify the restoration?

## 13. Repaso espaciado y siguiente paso

- **Al día siguiente de estudiar S005:** identifica los modos de cinco prompts y explica `enable`, `end` y `disable` sin consultar.
- **A los siete días:** recupera la diferencia entre `show running-config` y `show startup-config`, y resuelve un error de sintaxis con ayuda contextual.
- **Aproximadamente un mes después:** interpreta una transcripción nueva y distingue acceso por consola, enlace Ethernet y conectividad IP.

Son intervalos desde el estudio real, no desde la fecha de entrega. La próxima sesión del calendario es **S006: construir una LAN de dos equipos en Packet Tracer**. Si falta una base, se añadirá el refuerzo correspondiente.

## 14. Fuentes oficiales y alcance

Consultadas el **17/10/2026**. Se contrastaron disponibilidad del examen, IDs pertinentes y documentación de CLI. Los manuales del 2960 son referencias de esa plataforma; no implican que sea equipo actual recomendado para comprar ni que Packet Tracer reproduzca todo su software. Los ejercicios y ejemplos de Campus Aurora son propios de este programa.

1. [Cisco: fechas de transición de CCNA](https://blogs.cisco.com/learning/stay-on-track-get-certified-before-the-ccna-refresh).
2. [Cisco: temario oficial 200-301 CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf).
3. [Cisco Networking Academy: Packet Tracer, consola mediante Terminal](https://contenthub.netacad.com/courses/itn-dl/_common/2.3.8-packet-tracer---navigate-the-ios-using-a-terminal-client-for-console-connectivity---physical-mode.pdf), parámetros seriales y distinción del acceso de consola.
4. [Cisco Networking Academy: Packet Tracer — Navigate the IOS](https://contenthub.netacad.com/courses/itn-dl/_common/2.3.7-packet-tracer---navigate-the-ios.pdf), conexión RS-232/Console y ayuda.
5. [Cisco: modos de CLI, Catalyst 2960, IOS 15.2(2)E](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/15-2_2_e/command/reference/cr_2960/intro.html).
6. [Cisco: ayuda, abreviaturas y errores de CLI, IOS 12.2(55)SE](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swcli.html).
7. [Cisco: archivos de configuración activa y de arranque](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swiosfs.html).
8. [Cisco: administración y hostname](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swadmin.html).

---

**Estado:** material preparado. No se ejecutó el laboratorio al redactarlo; estudio, práctica y dominio del estudiante permanecen pendientes de evidencia.

Fin de la lección S005.
