LAB 11 — Enterprise Network Challenge | CCNA 200-301
Herramienta: Cisco Packet Tracer
Fecha: 22/11/2026
Objetivo
Diseñar, configurar y verificar una red empresarial con redundancia de capa 2, enrutamiento dinámico, servicios IP y controles de acceso. Evaluar su comportamiento ante fallas reales simuladas.
Topología
- Routers: R1, R2, R3 e ISP.
- Switches: SW1, SW2, SW3 y SW4 (segmento de tránsito OSPF).
- Equipos finales: PC-A, PC-B, PC-C y SRV-1.
- Redes: VLAN 15 (ADMIN), 25 (USERS), 35 (SERVERS), 55 (MGMT), 90 (OSPF-TRANSIT) y 999 (nativa).
Tecnologías configuradas
Área	Implementación
Switching	VLANs, VTP, trunks 802.1Q y EtherChannel LACP (Po1/Po2)
Redundancia	Rapid PVST+ y enlaces alternativos
Routing	Router-on-a-Stick, OSPF área 0, ruta por defecto y rutas de retorno
Servicios	DHCP, NAT/PAT
Seguridad	ACL extendida para restringir acceso desde USERS al servidor


Verificación y troubleshooting
Prueba	Falla simulada / validación	Resultado observado
EtherChannel	Apagado de SW1 Fa0/21	Po1 continuó SU; Fa0/22 permaneció P. Al restaurar, ambos miembros volvieron a P.
STP	Apagado de Po1 en SW1	Para VLAN 15, Fa0/20 pasó a Root FWD y ofreció el camino alternativo vía SW3 hacia SW2 (root).
OSPF	Apagado de R2 G0/0 (DR)	R3 pasó de BDR a DR. Al restaurar R2, su adyacencia con R3 volvió a FULL.
ACL 101	Bloqueo VLAN 25 → 192.168.35.10	Regla aplicada in en R1 G0/0.25, con coincidencias deny registradas.


Problemas encontrados y soluciones
Problema	Diagnóstico y solución
R1 continuaba como DR	Aunque R2 tenía prioridad 255 y R3 prioridad 100, R1 ya había sido elegido DR. Se configuró ip ospf priority 0 en R1 G0/1 y se realizó una nueva elección para obtener R2=DR, R3=BDR y R1=DROTHER. Cambiar la prioridad por sí solo no desplaza al DR existente.
Falla de conectividad hacia el ISP	Se identificó la ausencia de una ruta de retorno a 10.90.0.0/24 en el ISP. Se agregó la ruta estática vía R1.
Comportamiento inesperado de STP en Po1	Se revisaron los roles STP y el estado de los enlaces; al reiniciar los puertos miembros de Po1 en Packet Tracer se recuperó el estado esperado.
OSPF no anunciaba correctamente una loopback	Se corrigió el direccionamiento de R3 y la declaración de redes OSPF para anunciar las interfaces locales correspondientes.


Aprendizaje clave: OSPF DR/BDR
La elección de DR/BDR no es preemptiva. Aunque R2 regrese con prioridad 255, no desplaza al DR activo (R3). Se espera que R2 quede como BDR; falta conservar la salida final que confirme ese rol.
Comandos de comprobación
show etherchannel summary
show interfaces trunk
show spanning-tree vlan 15
show ip ospf neighbor
show ip ospf interface g0/0
show access-lists 101
show ip nat translations
Evidencias pendientes antes de publicar
- Adjuntar el archivo .pkt y una captura de la topología.
- Confirmar y registrar el rol final de R2 tras el failover.
- Conservar una prueba explícita del bloqueo de la ACL y las traducciones PAT.
- Adjuntar pings de conectividad durante la falla de Po1, si se realizan.
