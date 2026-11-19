# v250 (preparada en local, NO subida a GitHub)

## Que cambia
1. **Texto como pie de la primera foto.** Se quito el filtro `navigator.canShare(...)` que en Android puede responder "no" a archivo+texto y hacia caer en silencio a "foto sola". Ahora se llama `navigator.share({files:[foto1], text})` directo; solo si ese combo falla se comparte la foto sola. El pie se limita a ~1000 caracteres (WhatsApp corta el pie); el texto completo siempre queda en el portapapeles.
2. **Diagnostico.** Cada compartir guarda `cfg.shDiag` (version, largo del texto, resultado de canShare, si salio "ok+texto" o "solo foto" y el error). Se ve en Opciones > "Diagnostico del ultimo envio > Ver". Asi, si el texto aun no llega, sabemos la causa real en vez de adivinar.
3. **Sin cartel bloqueante.** Despues de compartir ya no sale "¿Se envio en WhatsApp?". Las fotos se marcan (`p.sh`) pero **siguen en Pendientes**; en Inicio aparece un aviso fijo "¿Se enviaron N fotos?" con [Si, se enviaron] / [No se enviaron]. Solo "Si" las pasa a Historial. "No" quita la marca. Si nadie responde (o se cierra la app) siguen en Pendientes y el aviso reaparece.
4. **Garantia:** ninguna foto sale de Pendientes sin confirmacion; no se vuelve a borrar al resolver el share (v248). Si falla el guardado en Historial, las fotos se quedan en Pendientes. El recordatorio automatico no reenvia fotos marcadas.
5. Intacto: sin foto Lista, sin cartel de enviar lista aparte, offline, lotes de 8, 1a compartida = 1 foto + texto, arreglos de Historial v247.
6. `APP_VER='v250'`, cache `receiving-v250`.

## Por que
En Android `navigator.share` resuelve al elegir WhatsApp, no al pulsar Enviar; una web app no puede saber si se envio. Por eso la confirmacion no se puede eliminar sin riesgo de perder fotos; ahora es un aviso no bloqueante que no estorba.

## Pruebas hechas (Playwright, share simulado)
- Cancelar en WhatsApp: 3 fotos siguen en Pendientes, sin aviso.
- Compartir completo: 1er share = 1 foto + texto; fotos siguen en Pendientes con aviso; el aviso sobrevive a recargar.
- "No": quedan pendientes. "Si": pasan a Historial (pend 0, hist 1).
- `node --check` OK; cero referencias a `_Lista`/`hasLista`/`listaSent`.
- No se puede probar en Galaxy S26 / WhatsApp real desde aqui.

## Como probar en el telefono
1. Subir (con aprobacion), abrir/cerrar la app 2-3 veces, verificar "v250" en Opciones.
2. Tomar 1 foto con apt, Compartir > WhatsApp: ¿llega el texto como pie? Si no, Opciones > Diagnostico del ultimo envio y mandar lo que dice.
3. Compartir y cancelar dentro de WhatsApp: la foto sigue en Pendientes; tocar "No se enviaron".
4. Compartir y enviar: tocar "Si, se enviaron": pasa a Historial.

# v251 (19 nov)
- Causa: tras la primera foto (con la lista) Android ya no deja abrir WhatsApp sin un toque; los lotes siguientes fallaban en silencio.
- Ahora: tras cada compartir sale un boton "Enviar N fotos" (el toque da permiso); lotes de 8. "Ahora no" deja el resto en Pendientes.
- Quitado el aviso "¿Se enviaron?": lo compartido pasa a Historial (con sus fotos, se puede reenviar); lo no enviado sigue en Pendientes.

# v252
- Compartir manda TODAS las fotos en un solo compartir (como Guardar copia) con la lista como texto (pie de la primera). Si el telefono rechaza el combo, cae al metodo v251 (1a foto + botones de 8).

# v253
- Camara a veces pequena: el video tomaba su tamano del flujo y la altura del visor cambiaba en iOS. Ahora el video llena el espacio (height:0 + flex) y la camara fija su altura al visor real al abrir y al cargar el video.
