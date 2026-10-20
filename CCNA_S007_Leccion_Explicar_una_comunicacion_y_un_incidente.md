# CCNA S007 — Repaso: explicar una comunicación y redactar un incidente

**Fecha:** 19 de octubre de 2026.  
**Duración orientativa:** 30 minutos en total: 7 de síntesis y 23 de recuperación, caso y corrección.  
**Práctica asociada:** `CCNA_S007_Practica_Repaso_y_reporte_de_incidente.md`.  
**Proyecto ficticio:** Campus Aurora.  
**Objetivo:** 200-301 CCNA v2.0.

## 1. Qué repasamos y para qué

S007 cierra la primera semana de materiales. Reúne S001–S006; no introduce configuración avanzada. Haber recibido las sesiones no confirma que las hayas estudiado. Si todavía estás retomando S003, continúa en orden antes de usar este repaso como evaluación.

Al terminar deberías poder:

- **O1:** explicar el recorrido de un ping local relacionando Ethernet, IPv4, ARP e ICMP.
- **O2:** distinguir bits, bytes, capacidad, tiempo de transferencia, latencia y pérdida.
- **O3:** seleccionar el contexto correcto para una consulta y diferenciar consola de Ethernet.
- **O4:** separar observación, hipótesis y causa comprobada.
- **O5:** redactar un incidente con evidencia, cambio mínimo, verificación y límites.

**Referencias oficiales v2.0:** apoyo a **1.6**, comprobación de conectividad del cliente, y **2.4**, diagnóstico básico mediante consultas y pruebas. No se acredita todavía la cobertura completa de esos objetivos ni la práctica en los tres sistemas operativos previstos. Las conversiones y los modelos de capas son fundamentos de apoyo, sin sub-IDs oficiales añadidos.

**Vigencia consultada el 19/10/2026:** v1.1 continúa disponible hasta el **02/02/2027**; v2.0 tiene temario publicado y examen anunciado desde el **03/02/2027**. El objetivo del programa sigue siendo v2.0 [1, 2].

## 2. Recuperación: intenta estas ocho preguntas primero

Dedica seis minutos, sin leer la síntesis siguiente. Una frase precisa puede bastar. Conserva el primer intento y después corrígelo con otro texto, sin borrarlo. Las traducciones de Q2, Q5 y Q8 y todas las soluciones están al final de la práctica.

1. **Q1:** ¿Dos computadoras conectadas a un switch pueden formar una LAN sin acceso a Internet? Explica.
2. **Q2:** A 40 MB file crosses an 80 Mb/s link. Ignoring overhead, what is the minimum transfer time? Does this determine latency?
3. **Q3:** Relaciona Ethernet, IP y TCP/UDP con las capas OSI 2, 3 y 4. ¿Ping utiliza TCP o UDP?
4. **Q4:** PC-A quiere enviar a una IPv4 de su misma LAN y desconoce su MAC. ¿Qué aporta ARP y qué asociación guarda la PC?
5. **Q5:** You are at `Switch(config-if)#`. How do `exit` and `end` change the prompt? Do they save the configuration?
6. **Q6:** ¿Dónde ejecutarías `ipconfig` y dónde `show ip interface brief` en nuestro laboratorio? Nombra la ventana y el equipo consultado.
7. **Q7:** Un ping obtiene cuatro respuestas de cuatro solicitudes. Escribe una conclusión válida y una afirmación sobre aplicaciones que no puedes sostener con esa prueba.
8. **Q8:** PC-B has no Ethernet link, but the console still works. Does this prove that the whole switch has failed? Name one check before changing settings.

Para Q2, MB y Mb/s se usan en sentido decimal; ignora cabeceras, esperas y cualquier otra limitación. Es un cálculo ideal, no una medición.

## 3. Síntesis de las seis sesiones

| Sesión | Idea que debes recuperar | Error que conviene evitar |
| --- | --- | --- |
| S001 | Una red conecta dispositivos para intercambiar datos. Un servicio es algo concreto que se utiliza sobre ella. | Equiparar LAN con Internet. |
| S002 | Un byte son ocho bits. La capacidad expresa una tasa; la latencia, una demora; la pérdida, datos o respuestas que no llegaron en la prueba. | Confundir Mb/s con MB/s o concluir que más capacidad elimina toda demora. |
| S003 | Los modelos organizan funciones: Ethernet en enlace, IP en red, TCP/UDP en transporte. En TCP/IP de cuatro capas se agrupan enlace, Internet, transporte y aplicación. | Tratar un modelo como si fuera un equipo físico. |
| S004 | Ethernet transporta tramas; IP, paquetes. TCP ofrece un flujo de bytes ordenado y mecanismos de recuperación; UDP transporta datagramas sin esas garantías propias de TCP. | Afirmar que todo tráfico IP utiliza TCP, o que UDP necesariamente pierde datos. |
| S005 | El prompt identifica el modo de IOS. El comando disponible depende del contexto; salir de configuración no guarda ni revierte ajustes. | Copiar el prompt como parte de la orden o interpretar un rechazo de CLI como avería de red. |
| S006 | Dos hosts correctamente direccionados en la misma LAN pueden comunicarse localmente. Se verifica el estado antes y después de cada cambio. | Dar por terminada la reparación solo porque se modificó un valor. |

En un cálculo ideal: **tiempo en segundos = tamaño en bits ÷ tasa en bits/s**. Por ejemplo, 12 MB equivalen a 96 Mb y tardarían al menos 4 s a 24 Mb/s. Este dato no determina por sí solo la latencia de un paquete ni el rendimiento de una aplicación.

## 4. Explicar una comunicación, paso a paso

Retoma el laboratorio conocido: PC-A `192.168.10.10/24`, PC-B `192.168.10.20/24`, un switch y dos enlaces Ethernet. `/24` equivale aquí a `255.255.255.0`; no hay router, gateway ni DNS configurados.

1. PC-A compara dirección y máscara y determina que PC-B pertenece a la misma subred.
2. Si no conoce su MAC, utiliza ARP para resolver la dirección del vecino IPv4. Una asociación ya aprendida puede evitar un nuevo intercambio ARP.
3. Envía el paquete que contiene ICMP dentro de una trama Ethernet. El switch reenvía las tramas por la LAN.
4. PC-B responde con ICMP Echo Reply. La respuesta debe regresar a PC-A para que ping registre éxito [3].

No necesitas añadir TCP o UDP a esa explicación. Tampoco necesitas configurar una IP en los puertos de capa 2 del switch para que conmuten las tramas. La gestión por consola es un acceso distinto.

## 5. Elegir una consulta y limitar la conclusión

| Pregunta | Contexto y consulta | Qué queda fuera de esa evidencia |
| --- | --- | --- |
| ¿Qué IPv4 y máscara tiene la PC? | PC → Desktop → Command Prompt: `ipconfig` | No demuestra alcance hacia otra PC. |
| ¿Qué IP/MAC conoce el host? | Command Prompt de la PC: `arp -a` | Una entrada en caché no garantiza disponibilidad actual. |
| ¿Hay respuesta ICMP del otro host? | PC-A: `ping 192.168.10.20` | No prueba HTTP, DNS ni Internet. |
| ¿Cómo aparecen las interfaces del switch? | Terminal de PC-A hacia SW1, EXEC privilegiado: `show ip interface brief` | No verifica todos los ajustes ni aplicaciones de los hosts. |

Las herramientas de la PC simulada y Terminal cumplen funciones distintas [4]. En IOS, antes de cambiar algo revisa el prompt y la ayuda [5].

**Ejemplo didáctico de navegación, no ejecutado al redactar:**

```text
Switch(config-if)# end
Switch# show ip interface brief
```

Escribe únicamente los comandos, no los prompts. `end` vuelve a EXEC privilegiado; no corrige un cable ni guarda una configuración. Si ya estás en `Switch#`, ejecuta directamente la consulta.

## 6. De una observación a un reporte útil

“No funciona la red” es demasiado amplio. “PC-A no recibe respuestas de PC-B; el acceso por consola permanece disponible” describe un síntoma y delimita su alcance.

| Elemento | Qué debe contener |
| --- | --- |
| Síntoma y alcance | Qué falla, desde dónde, hacia dónde y qué sigue disponible. |
| Evidencia | Valores, comandos y resultados; distinguir datos propios de ejemplos suministrados. |
| Hipótesis | Una causa posible y una comprobación que permita contrastarla. |
| Cambio y reversión | La acción mínima y cómo recuperar el estado correcto documentado. |
| Verificación | Repetir la prueba fallida y una prueba de control; indicar lo pendiente. |

**Ejemplo completo independiente del caso de la práctica:** un alumno intenta `ipconfig` en IOS y recibe un rechazo. Observa que está en la CLI del switch. La hipótesis es una ventana equivocada. Abre Command Prompt de la PC y obtiene su configuración con `ipconfig`. El informe puede cerrar el problema de consulta; no puede afirmar que quedó verificada toda la LAN. No cambió configuración, por lo que no necesita revertir parámetros.

Un puerto `down/down` orienta hacia un enlace no operativo, pero no distingue por sí solo entre un cable desconectado, un extremo apagado u otras causas. Registra primero el dato y después la inspección que confirma la causa específica. No inventes quién ocasionó un incidente si no existe evidencia.

## 7. Seis expresiones para comunicar el diagnóstico

| Inglés | Español | Ejemplo |
| --- | --- | --- |
| Symptom | Síntoma observado | The symptom is a failed ping. |
| Scope | Alcance del problema | The scope is the connection to PC-B. |
| Evidence | Evidencia que apoya una conclusión | The evidence shows that the link is down. |
| Hypothesis | Explicación que debe comprobarse | My hypothesis is a disconnected cable. |
| Restore | Restaurar el estado correcto | Restore the original connection. |
| Verify | Comprobar mediante una prueba | Verify connectivity in both directions. |

## 8. Repaso espaciado y siguiente paso

Después de corregir, anota **un error real de tu intento**, su corrección y una nueva pregunta que permita comprobarlo. Si no has respondido, registra “pendiente de intento”; no inventes un error ni una puntuación.

- Al día siguiente del estudio: vuelve a explicar el recorrido del ping y el límite de su resultado.
- A los siete días: resuelve otro incidente separando evidencia e hipótesis, sin mirar el modelo.
- Aproximadamente al mes: relaciona estos pasos con las máscaras y herramientas aprendidas entonces.

La siguiente sesión del calendario es **S008: cobre, conectores, pinout y elección de cable**. Las respuestas determinarán si hace falta un refuerzo antes. Sin respuestas, seguirá la secuencia de materiales con aprendizaje sin verificar y la misma mezcla inicial de inglés.

## 9. Fuentes oficiales

Consultadas el **19/10/2026**. La síntesis, los ejemplos y las preguntas son propios del curso; no son preguntas de un banco real.

1. [Cisco: transición de CCNA v1.1 a v2.0](https://blogs.cisco.com/learning/stay-on-track-get-certified-before-the-ccna-refresh).
2. [Cisco: temario oficial 200-301 CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf).
3. [Cisco: funcionamiento y alcance de ping](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13730-ext-ping-trace.html).
4. [Packet Tracer: herramientas y configuración de las PCs](https://tutorials.ptnetacad.net/help/default/config_PCs.htm).
5. [Cisco: modos, ayuda y errores de CLI del Catalyst 2960](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swcli.html).

---

**Estado:** material preparado. No se ejecutó un laboratorio al redactarlo ni se evaluó todavía el dominio del estudiante.

Fin de la lección S007.
