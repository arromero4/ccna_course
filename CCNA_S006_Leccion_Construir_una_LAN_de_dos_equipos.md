# CCNA S006 — Construir una LAN de dos equipos en Packet Tracer

**Fecha:** 18 de octubre de 2026.  
**Tiempo sugerido:** 20 minutos de lección y 70 de práctica; sesión integradora de 90 minutos.  
**Proyecto ficticio:** Campus Aurora.  
**Práctica asociada:** `CCNA_S006_Practica_Construir_verificar_y_reparar_una_LAN.md`.  
**Examen objetivo:** 200-301 CCNA v2.0.

## 1. Alcance, vigencia y objetivos

Consulta oficial del **18/10/2026**: el examen disponible sigue siendo **v1.1**, con último día anunciado el **02/02/2027**. Cisco ya publicó el temario de **v2.0**, cuyo examen comienza el **03/02/2027**. Este programa mantiene v2.0 como objetivo [1, 2].

| ID oficial v2.0 | Relación con esta sesión |
| --- | --- |
| **1.6** | Fundamento para comprobar configuración y alcance IP de clientes cableados. La PC de Packet Tracer no sustituye la práctica posterior en Windows, macOS y Linux. |
| **2.4** | Introducción al diagnóstico de conectividad mediante consultas, ping y observación de eventos. No cubre aún todas las herramientas del objetivo. |

Utilizaremos direcciones y una máscara ya elegidas. El cálculo de subredes se estudia en las semanas 3 y 4; la conmutación detallada se desarrolla desde S011. ARP aparece como fundamento de apoyo, sin asignarle un sub-ID oficial inexistente.

Al terminar deberías poder:

1. **O1:** construir y documentar los enlaces Ethernet de dos PCs a un switch, distinguiéndolos del enlace de consola.
2. **O2:** asignar las dos direcciones indicadas y explicar por qué estos hosts se consideran vecinos IP locales.
3. **O3:** probar comunicación con ping en ambas direcciones y expresar qué demuestra y qué no.
4. **O4:** relacionar una consulta ARP y un intercambio ICMP con lo estudiado sobre Ethernet e IP.
5. **O5:** diferenciar una desconexión física de una dirección incorrecta mediante evidencia, no solo por el resultado de ping.
6. **O6:** restaurar cada cambio de prueba y documentar el estado final.

**Prerrequisitos:** S001–S005, especialmente capas, Ethernet/IP/ICMP y acceso a IOS por consola. Recibir esas sesiones no acredita haberlas estudiado. Si estás retomando S003, conserva el orden S003 → S004 → S005 antes de realizar este laboratorio. No necesitas calcular subredes ni configurar routers.

## 2. Recuerdo activo

Responde antes de continuar; las soluciones están al final de la práctica:

- **R1:** desde `Switch>`, ¿qué orden permite llegar a `Switch#`?
- **R2:** ¿qué diferencia hay entre la dirección IP y la dirección MAC en la comunicación estudiada?
- **R3:** ¿puede una consola seguir funcionando aunque falle un enlace Ethernet? Explica.

## 3. La red que vamos a construir

Campus Aurora prepara dos puestos de capacitación, **PC-A** y **PC-B**. Necesitan intercambiar tráfico dentro del mismo laboratorio. Un switch, identificado en el plano como **SW1**, conecta sus interfaces Ethernet.

Una **LAN**, *local area network*, es una red de alcance local. Tener una LAN no exige tener acceso a Internet. En esta sesión no hay router, servidor DNS ni servidor DHCP.

La topología se define por estas conexiones:

| Origen | Destino | Función |
| --- | --- | --- |
| PC-A, FastEthernet0 | SW1, FastEthernet0/1 | Datos Ethernet |
| PC-B, FastEthernet0 | SW1, FastEthernet0/2 | Datos Ethernet |
| PC-A, RS-232 | SW1, Console | Administración local por consola |

Los dos enlaces de datos usan **Copper Straight-Through** en Packet Tracer; la consola usa su cable específico [3]. PC-A puede tener ambas conexiones: sus puertos cumplen funciones distintas. La conexión serial no reemplaza al enlace Ethernet.

El switch nuevo mantiene sus puertos en la misma VLAN predeterminada. Una VLAN permite agrupar puertos dentro de una red de capa 2; por ahora utilizamos el estado inicial del equipo, sin diseñar ni configurar otras VLAN.

**No hace falta asignar una IP al switch para que conmute las tramas entre estas dos PCs.** Una dirección de administración del switch serviría para otro propósito. Aquí lo consultaremos por consola.

## 4. Direcciones: lo mínimo necesario para este laboratorio

La dirección IPv4 identifica la interfaz del host en la red IP. La máscara permite decidir qué parte corresponde a la red y qué parte al host [4]. Usaremos este plan fijo:

| Equipo | Interfaz | IPv4 | Máscara | Gateway y DNS |
| --- | --- | --- | --- | --- |
| PC-A | FastEthernet0 | `192.168.10.10` | `255.255.255.0` | Sin configurar |
| PC-B | FastEthernet0 | `192.168.10.20` | `255.255.255.0` | Sin configurar |
| SW1 | Puertos Ethernet de capa 2 | Sin IP | No corresponde | No corresponde |

Con **esta máscara concreta**, las dos PCs pertenecen a `192.168.10.0/24`: comparten los primeros tres números y tienen un último número distinto. `/24` es otra forma de expresar la máscara `255.255.255.0`. No generalices la regla de “comparar tres números” a otras máscaras.

Cada PC debe tener su propia dirección. Poner `192.168.10.10` en ambas no significa “ponerlas en la misma red”: crea una duplicación. Tampoco uses `192.168.10.0` ni `192.168.10.255` para estas PCs; en este `/24` se reservan para identificar la red y para difusión, respectivamente.

La **default gateway**, puerta de enlace predeterminada, permite enviar tráfico hacia destinos de otras redes cuando corresponde utilizar esa salida. Estas PCs se comunican directamente dentro de su subred; el escenario no tiene gateway. No inventes `192.168.10.1` por costumbre. DNS tampoco interviene al probar una dirección numérica.

En Packet Tracer, **Desktop → IP Configuration → Static** permite introducir los valores. **Desktop → Command Prompt** ofrece consultas del host [5]. Si gateway o DNS aparecen como `0.0.0.0`, interprétalos aquí como no configurados; no como un equipo al que hacer ping.

## 5. Tres ventanas, tres propósitos

| Ventana o contexto | Uso | Ejemplo |
| --- | --- | --- |
| PC → IP Configuration | Introducir dirección y máscara | Campos gráficos de PC-A y PC-B |
| PC → Command Prompt | Consultar o probar desde esa PC | `ipconfig`, `ping 192.168.10.20`, `arp -a` |
| PC-A → Terminal → IOS de SW1 | Consultar el switch por consola | `enable`, `show interfaces status` |

`ipconfig` no es un comando de IOS. `show interfaces status` no se ejecuta en la consola de comandos de la PC. Leer el contexto evita diagnosticar como falla de red lo que solo es una orden introducida en la ventana equivocada.

Desde EXEC privilegiado del switch puedes utilizar:

```text
show interfaces status
show ip interface brief
```

La primera consulta resume el estado de los puertos; la segunda muestra direcciones y estados de interfaces. En puertos de capa 2, `unassigned` es compatible con que Ethernet funcione [6].

Un fragmento **didáctico**, no obtenido por ejecución en esta sesión, podría ser:

```text
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/1         unassigned      YES unset  up                    up
FastEthernet0/2         unassigned      YES unset  up                    up
```

Esto apoya que esos enlaces están activos. No prueba que PC-A y PC-B tengan direcciones correctas ni que una aplicación funcione. Un indicador verde también tiene un alcance limitado: no valida por sí solo toda la comunicación.

## 6. Qué ocurre al hacer ping

Desde **Command Prompt de PC-A**, escribe:

```text
ipconfig
ping 192.168.10.20
arp -a
```

Desde **Command Prompt de PC-B**:

```text
ipconfig
ping 192.168.10.10
arp -a
```

Cada orden se ejecuta por separado. Ping utiliza mensajes **ICMP Echo Request** y **ICMP Echo Reply**; no establece una conexión TCP ni envía una solicitud HTTP [7]. La respuesta debe volver al origen para que la prueba tenga éxito.

El recorrido conceptual es:

1. PC-A compara el destino con su dirección y máscara; en este caso decide que PC-B es local.
2. Para enviar una trama Ethernet necesita una MAC de destino. Si no la conoce, **ARP**, *Address Resolution Protocol*, permite resolver la IPv4 local a una dirección MAC. El host conserva asociaciones temporalmente en su tabla ARP.
3. El switch transporta las tramas entre sus puertos dentro de la LAN. La lógica detallada de aprendizaje de MAC llegará en S011.
4. PC-B procesa la solicitud ICMP y genera una respuesta hacia PC-A.
5. PC-A registra la respuesta y el tiempo de ida y vuelta.

`arp -a` permite observar asociaciones del host. Una entrada ARP no es una prueba completa de que el destinatario continúe disponible: puede conservar información aprendida antes. No confundas la tabla ARP de la PC, que relaciona IP y MAC, con la tabla MAC del switch, que relaciona MAC y puerto dentro de una VLAN [8].

Al inicio puede agotarse alguna espera mientras se preparan los dispositivos o se resuelve ARP [9]. No debe ocurrir obligatoriamente. Repite después de estabilizar el escenario y registra ambas pruebas; si la pérdida persiste, investiga. Un primer fallo aislado tampoco identifica por sí solo su causa.

**Ejemplo didáctico de resumen exitoso:**

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

En tu práctica conserva la salida real del simulador, aunque el formato difiera. Este resultado verifica esas cuatro solicitudes a ese destino y en ese momento. No demuestra acceso a Internet, DNS, HTTP ni rendimiento real de hardware. Los tiempos simulados no son una medición de tu red doméstica.

## 7. Una prueba fallida admite varias causas

Considera dos cambios independientes:

| Cambio controlado | Evidencia esperada | Acción mínima para volver al estado correcto |
| --- | --- | --- |
| Desconectar el enlace Ethernet de PC-B | Puerto hacia PC-B sin enlace y ping sin respuesta del destino | Restaurar el cable en los mismos puertos |
| Cambiar solo la IP de PC-B a `192.168.20.20`, conservando `/24` | Enlaces activos, pero direccionamiento distinto del plan y ausencia de comunicación local prevista | Restaurar `192.168.10.20` y repetir las pruebas |

En el segundo caso, PC-B pertenece a otra subred IPv4. Poner ambas PCs en el mismo switch no crea una ruta entre subredes. Cambiar la IP del switch o agregar un DNS no repara el error del plan de direccionamiento.

El mensaje exacto de ping puede variar: tiempo agotado o destino inaccesible no son, por sí solos, diagnósticos de una causa única. Correlaciona el mensaje con el enlace y la configuración.

El método de Campus Aurora es: **síntoma → alcance → evidencia → hipótesis → cambio mínimo → verificación**. Cambia una variable cada vez y vuelve al estado correcto antes de introducir el siguiente incidente. Así puedes relacionar la reparación con su efecto.

## 8. Errores comunes

- Usar Console en lugar de Ethernet para transportar el tráfico entre PCs.
- Copiar la misma IPv4 a los dos hosts o aceptar una máscara distinta sin revisarla.
- Probar desde el switch sin haber configurado allí una dirección IP y asumir que ese fallo representa la prueba entre PCs.
- Dar por validada toda la red porque los enlaces están verdes.
- Concluir que la red está averiada por un único primer intento, o ignorar pérdidas persistentes atribuyéndolas siempre a ARP.
- Cambiar varios parámetros a la vez y perder la posibilidad de identificar cuál corrigió el incidente.
- Presentar los ejemplos didácticos como comandos ejecutados por el alumno.

## 9. Vocabulario: ocho expresiones

| Inglés | Significado | Ejemplo de uso |
| --- | --- | --- |
| Local area network | Red de alcance local | The two computers belong to the same local area network. |
| Straight-through cable | Cable Ethernet directo | Connect each PC with a straight-through cable. |
| IP address | Dirección de una interfaz en una red IP | Each PC needs a different IP address. |
| Subnet mask | Máscara que delimita red y host | Check the subnet mask on both PCs. |
| Default gateway | Salida predeterminada hacia otras redes | This local test does not need a default gateway. |
| Echo reply | Respuesta ICMP de eco | An echo reply returned to PC-A. |
| Reachability | Posibilidad de alcanzar un destino | Ping tests reachability to a specific address. |
| Restore the baseline | Volver al estado de referencia | Restore the baseline before testing another fault. |

## 10. Ocho preguntas de comprensión

Intenta responder antes de leer las soluciones al final de la práctica. Q2, Q5 y Q8 están en inglés; su traducción también se reserva para las soluciones.

1. **Q1:** ¿Qué dos conexiones de PC-A tienen propósitos diferentes en la topología y cuál transporta su ping?
2. **Q2:** Why do PC-A and PC-B not need a default gateway to communicate in this lab?
3. **Q3:** ¿Puede SW1 conmutar las tramas de estas PCs sin una IP de administración? Explica la diferencia entre conmutar y administrar por IP.
4. **Q4:** ¿Por qué comparar los primeros tres números de una IPv4 es útil en nuestro ejemplo, pero no una regla válida para cualquier máscara?
5. **Q5:** Both links are up, but ping fails. What two host settings should you check first, and why?
6. **Q6:** ¿Qué asociación muestra `arp -a` y por qué una entrada no garantiza que el host siga disponible?
7. **Q7:** Obtienes cuatro respuestas de cuatro intentos. Escribe una conclusión válida y dos afirmaciones que esa prueba no permite sostener.
8. **Q8:** After restoring PC-B's original address, what evidence would you collect before closing the incident?

## 11. Repaso y continuidad

Después de estudiar, explica sin mirar qué aporta cada elemento: cable, switch, IP, máscara, ARP e ICMP. Si no puedes, marca el punto concreto para refuerzo.

- **Al día siguiente del estudio:** reconstruye la tabla de direccionamiento y distingue Terminal de Command Prompt. S007 será el repaso de comunicación y redacción de un incidente.
- **A los siete días:** compara un puerto sin enlace con un host mal direccionado usando evidencia nueva.
- **Aproximadamente un mes después:** justifica la decisión local/remota con las máscaras que hayas aprendido entonces.

Los intervalos parten del estudio real. La entrega de S006 no confirma aprendizaje ni un nivel de inglés; por ahora se mantiene la mezcla inicial acordada.

## 12. Fuentes oficiales y límites

Consultadas el **18/10/2026**. Los casos, direcciones, preguntas y criterios de evaluación son elaboración propia. Las referencias amplían conceptos; no es necesario completar sus actividades para realizar S006.

1. [Cisco: fechas de transición de CCNA](https://blogs.cisco.com/learning/stay-on-track-get-certified-before-the-ccna-refresh).
2. [Cisco: temario oficial 200-301 CCNA v2.0](https://learningcontent.cisco.com/documents/marketing/exam-topics/200-301_CCNA_v2.0_Exam_Topics_PDF.pdf).
3. [Packet Tracer: conexiones, cables e indicadores](https://tutorials.ptnetacad.net/help/default/connectionsLinks.htm).
4. [Cisco: direcciones IPv4 y máscaras](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html). Se utiliza la relación entre dirección y máscara; el cálculo completo se estudiará después.
5. [Packet Tracer: configuración de PCs y herramientas de escritorio](https://tutorials.ptnetacad.net/help/default/config_PCs.htm).
6. [Cisco: consultas de interfaces, Catalyst 2960, IOS 15.0(2)EZ](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/15-0_2_ez/command/reference/cr2960/cli2.html). La disponibilidad en el simulador depende del modelo y de la implementación.
7. [Cisco: funcionamiento de ping](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13730-ext-ping-trace.html). Hoy se usa ping básico desde la PC, no ping extendido de IOS.
8. [Cisco Networking Academy: observación de ARP y tablas MAC en Packet Tracer](https://contenthub.netacad.com/courses/itn-dl/_common/9.2.9-packet-tracer---examine-the-arp-table_es-XL.pdf).
9. [Cisco Networking Academy: pruebas de ping y resolución inicial de ARP](https://contenthub.netacad.com/legacy/NetEss/1.0/es/course/files/4.1.1.2%20Packet%20Tracer%20-%20Connecting%20to%20a%20Web%20Server.pdf).

---

**Estado:** material preparado. El laboratorio no fue ejecutado al redactar esta lección. Estudio, ejecución y dominio del estudiante pendientes de evidencia.

Fin de la lección S006.
