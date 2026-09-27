# PRD — Descripciones culturales para After the End

> Mod: **AteCultureDescription** · Juego: Crusader Kings III 1.19 · Base: After the End (Workshop 3192256710)
> Fecha: 2026-09-26 · Fuente: `documento_producto_mod_culturas_ck3.pdf` + investigación del motor y de Elder Kings 2 (ver `scripts/context.md`).

## Enunciado del Problema

En After the End las religiones tienen una descripción narrativa que se ve en la interfaz, pero las culturas no: son solo un nombre, una herencia, pilares y tradiciones. Con ~464 culturas en un mundo post-apocalíptico inventado, el jugador a menudo no sabe *quiénes son* los Neomoor, los Sunshiner o los Motowner, de dónde salieron ni qué los hace distintos de sus vecinos. El lore existe en la cabeza de los autores y en foros, pero no dentro del juego.

El problema empeora con las culturas híbridas y divergentes: aparecen durante la partida (creadas por el jugador o por la IA) sin ningún texto que las identifique. Y cuando el jugador crea su propia cultura, el juego le deja elegir nombre, sustantivo colectivo y prefijo, pero no le deja contar su historia — a diferencia de la creación de religiones, que sí tiene un campo de descripción.

## Solución

Al pasar el mouse sobre cualquier cultura, en cualquier parte del juego, el tooltip de cultura muestra una descripción narrativa breve (2–4 frases, en inglés, con el tono de ATE):

- Las **culturas base de ATE** muestran una descripción escrita específicamente para ellas: origen, rasgos identitarios, relación con otras culturas y contexto geográfico.
- Las **culturas híbridas y divergentes** reciben automáticamente, al nacer, una descripción genérica elegida según su herencia de ATE y su forma de creación (híbrida o divergente). Si ninguna encaja, reciben un texto de respaldo que sirve para cualquier cultura.
- **Ninguna cultura aparece jamás sin texto**: ni en partidas ya empezadas, ni con culturas añadidas por otros mods.
- El **jefe cultural** del jugador puede cambiar la descripción de su cultura eligiendo entre varias plantillas predefinidas mediante una decisión.
- Se dedica una **fase de investigación** a determinar si es posible que el jugador escriba una descripción propia al crear una cultura; si resulta viable, se convierte en una fase de implementación.

La descripción queda asociada a la cultura (no a su nombre), sobrevive a renombres y se guarda con la partida.

## Historias de Usuario

### Descripciones de culturas base

1. Como jugador de ATE, quiero ver una descripción narrativa al pasar el mouse sobre una cultura, para poder entender quiénes son sin salir del juego.
2. Como jugador nuevo en ATE, quiero que la descripción explique el origen post-apocalíptico de la cultura, para poder situarme en un mundo que no conozco.
3. Como jugador, quiero que la descripción mencione la relación de la cultura con sus culturas padre o vecinas, para poder entender el mapa cultural de la región.
4. Como jugador, quiero que la descripción aluda al contexto geográfico de la cultura, para poder ubicarla en el mapa de la Norteamérica de ATE.
5. Como jugador, quiero que las descripciones sean breves (2–4 frases), para poder leerlas en un tooltip sin interrumpir el juego.
6. Como jugador, quiero que las descripciones tengan el tono y estilo de ATE (humor seco, instituciones modernas convertidas en mitos), para poder sentirlas parte del mod y no un añadido externo.
7. Como jugador, quiero ver la descripción de una cultura desde cualquier lugar donde aparezca su nombre con tooltip (personaje, condado, ventana de cultura, lentes del mapa), para poder consultarla sin navegar a otra pantalla.
8. Como jugador, quiero que el tooltip no se deforme ni se estire con textos largos, para poder seguir interactuando con la interfaz normalmente.
9. Como jugador que juega con la cultura Neomoor, quiero que su descripción sea coherente con el lore Shriner/Neomoor ya existente, para poder disfrutar de una narrativa consistente.
10. Como jugador que habla inglés, quiero que todas las descripciones estén en inglés correcto, para poder leerlas sin errores ni claves crudas.

### Culturas híbridas y divergentes

11. Como jugador, quiero que una cultura híbrida recién creada tenga una descripción automática, para poder distinguirla aunque nadie la haya escrito.
12. Como jugador, quiero que una cultura divergente recién creada tenga una descripción automática, para poder distinguirla de su cultura madre.
13. Como jugador, quiero que la descripción genérica refleje la herencia de ATE de la nueva cultura, para poder sentir que el texto encaja con ella.
14. Como jugador, quiero que la descripción genérica diga si la cultura nació de una hibridación o de una divergencia, para poder entender su origen.
15. Como jugador, quiero que la descripción genérica pueda nombrar a la propia cultura y a sus culturas padre dinámicamente, para poder leer un texto específico aunque sea una plantilla.
16. Como jugador, quiero que una cultura creada por la IA reciba también una descripción automática, para poder conocer a las culturas rivales que surgen durante la partida.
17. Como jugador, quiero que si ninguna descripción genérica encaja se use un texto de respaldo neutro, para poder no ver nunca un tooltip vacío o una clave cruda.
18. Como jugador, quiero que la descripción asignada a una cultura nueva sea permanente, para poder encontrar el mismo texto al volver a consultarla más tarde.

### Plantillas del jugador

19. Como jefe cultural, quiero una decisión que me permita cambiar la descripción de mi cultura, para poder darle la identidad que imagino.
20. Como jefe cultural, quiero ver varias plantillas entre las cuales elegir, para poder escoger la que mejor encaja con mi historia.
21. Como jefe cultural, quiero ver una vista previa del texto de cada plantilla antes de elegirla, para poder decidir con conocimiento.
22. Como jefe cultural, quiero poder volver a la descripción original de mi cultura, para poder deshacer un cambio del que me arrepienta.
23. Como jefe cultural, quiero poder cambiar la descripción más de una vez, para poder adaptarla a la evolución de mi partida.
24. Como jugador que acaba de crear una cultura, quiero que se me ofrezca elegir una plantilla justo después de crearla, para poder definir su identidad en el momento en que me importa.
25. Como jugador que no es jefe cultural, quiero que la decisión no me aparezca, para poder no editar culturas que no controlo.
26. Como jugador, quiero que la IA no use la decisión de plantillas, para poder confiar en que las descripciones de las culturas IA no cambian sin motivo.
27. Como jefe cultural, quiero que la elección de plantilla se mantenga al guardar y cargar la partida, para poder no repetir la elección.

### Robustez y compatibilidad

28. Como jugador que renombra su cultura, quiero que la descripción se mantenga, para poder no perder la identidad narrativa por un cambio de nombre.
29. Como jugador que añade el mod a una partida ya empezada, quiero que todas las culturas existentes muestren una descripción, para poder beneficiarme del mod sin empezar de nuevo.
30. Como jugador que añade el mod a una partida ya empezada, quiero que las culturas que ya tenían descripción no se sobrescriban, para poder conservar mis elecciones.
31. Como jugador que usa otros mods que añaden culturas, quiero que esas culturas muestren un texto de respaldo, para poder no ver claves crudas.
32. Como jugador, quiero que el mod no genere entradas en `error.log`, para poder confiar en que no rompe nada.
33. Como jugador, quiero que el mod no altere mecánicas de hibridación, divergencia ni tradiciones, para poder jugar ATE exactamente igual salvo por los textos.
34. Como jugador, quiero que el mod funcione cuando ATE se actualiza y añade o renombra culturas, para poder seguir usándolo entre versiones.

### Autor del mod

35. Como autor del mod, quiero generar automáticamente el registro de claves a partir de los archivos de culturas de ATE, para poder no escribir 464 líneas a mano ni olvidar culturas.
36. Como autor del mod, quiero regenerar el registro cuando ATE se actualice, para poder incorporar culturas nuevas en minutos.
37. Como autor del mod, quiero que el generador detecte culturas añadidas o eliminadas respecto al registro anterior, para poder saber qué descripciones faltan escribir.
38. Como autor del mod, quiero un validador que liste culturas sin descripción, claves huérfanas, archivos sin BOM y textos fuera de 2–4 frases, para poder publicar sin errores.
39. Como autor del mod, quiero escribir descripciones en tandas (por herencia o región), para poder avanzar de forma incremental y publicar versiones parciales.
40. Como autor del mod, quiero que el generador esté cubierto por tests automáticos, para poder modificarlo sin romper el registro.

### Investigación: descripción editable al crear una cultura

41. Como jugador que crea una cultura híbrida o divergente, quiero escribir mi propia descripción en la ventana de creación, para poder contar la historia exacta de mi cultura (sujeto al resultado de la investigación).
42. Como autor del mod, quiero un informe de investigación con las vías exploradas y su veredicto (viable / no viable / viable con límites), para poder decidir si se implementa y cómo.
43. Como autor del mod, quiero que si la vía es viable se evalúe si el texto sobrevive a guardar/cargar y se ve en multijugador, para poder no publicar una función que pierde datos.

## Decisiones de Implementación

### Alcance y plataforma

- Solo After the End, CK3 1.19. Solo inglés.
- Submod independiente que carga después de ATE. No sobrescribe archivos de ATE.
- Todos los archivos del mod en UTF-8 con BOM y tabs reales.

### Mecanismo central (probado por Elder Kings 2)

- Cada cultura guarda una **variable de cultura** (`culture_key`) cuyo valor es un flag que codifica la raíz de su clave de localización. La interfaz construye la clave concatenando esa raíz con un sufijo fijo y la localiza. Esto desacopla la descripción del nombre de la cultura (sobrevive a renombres) y persiste en la partida porque las variables de cultura se guardan.
- Se descartan explícitamente: construir la clave a partir del nombre de la cultura (se rompe al renombrar) y `customizable_localization` (el motor no tiene precedente de `type = culture`).
- Todas las claves y variables del mod llevan un prefijo propio para no colisionar con ATE ni con otros mods.

### Módulos

1. **Banco de descripciones fijas** — archivo(s) de localización en inglés con una clave por cultura base de ATE. Organizado por herencia para escribir por tandas. El contenido se entrega incrementalmente; mientras una cultura no tenga texto escrito, el validador la reporta y la interfaz cae al respaldo.

2. **Registro de claves** *(módulo profundo)* — un efecto con parámetro de cultura que asigna su `culture_key`. Interfaz: "dada esta cultura, dale su clave". Se invoca para todas las culturas base al inicio de partida. **Solo asigna si la cultura no tiene ya una clave**, para no pisar elecciones previas (partidas guardadas, plantillas elegidas).

3. **Selector de descripción genérica** *(módulo profundo)* — un efecto que, dada una cultura recién creada, decide su clave genérica. Interfaz: "dada esta cultura nueva, asígnale la mejor genérica". Internamente: clasifica por herencia de ATE (agrupando las 107 herencias en familias manejables), luego por tipo de creación (híbrida si tiene dos culturas padre, divergente si tiene una), y termina siempre en una clave de respaldo universal. La cadena de decisión es un `if/else_if` con respaldo final obligatorio.

4. **Enganche de creación** — un handler propio agregado a `on_culture_created` de forma aditiva (los on_action se fusionan entre archivos; ATE ya hace lo mismo con ccu). Llama al selector y, si el fundador es un jugador, dispara la oferta de plantillas.

5. **Plantillas del jugador** — una decisión visible solo para el jefe de su cultura (`is_ai = no`), que abre un evento con varias plantillas más la opción de restaurar la original. Elegir una cambia la `culture_key` de la cultura. La cultura recuerda su clave original en una segunda variable para permitir restaurarla. La decisión requiere bloque `picture` y `ai_check_interval` (gotchas conocidos de ATE).

6. **Visualización (tooltip)** — override del tooltip de cultura compartido del juego base (ATE no lo sobrescribe). Añade, al final del tooltip, un divisor y un texto multilínea con `max_width` obligatorio (sin él, el `autoresize` estira el panel y deja zonas muertas para el mouse). Si la cultura no tiene `culture_key`, muestra el texto de respaldo mediante dos widgets con visibilidad complementaria (no `SelectLocalization`, que pierde el contexto de datos).

7. **Herramientas Python** *(módulo profundo)*:
   - **Generador**: lee los archivos de culturas de ATE (solo lectura), extrae los identificadores de cultura de nivel superior con su herencia, y emite el archivo de script con las invocaciones del registro, agrupadas por herencia. Además emite un informe de diferencias contra el registro anterior (culturas añadidas / eliminadas).
   - **Validador**: cruza culturas registradas contra claves de localización; reporta faltantes, huérfanas, archivos sin BOM y textos fuera de 2–4 frases.
   - Solo usa la biblioteca estándar de Python (3.14 instalado).

8. **Investigación: descripción editable al crear cultura** — fase de spike con entregable un informe (no código de producción). Vías a explorar:
   - Volcar los *data types* del motor (`script_docs` / `dump_data_types` desde consola) y buscar cualquier función de `HybridizationWindow`, `DivergenceWindow` o `Culture` que acepte texto o descripción.
   - Probar si una caja de texto puede escribir en el sistema de variables de la GUI (`GetVariableSystem`) y si ese valor persiste al guardar/cargar y se ve en multijugador.
   - Evaluar reutilizar un campo de texto existente de la cultura (p. ej. sustantivo colectivo o prefijo) y sus efectos secundarios.
   - Revisar mods de la Workshop que hayan intentado algo similar.
   - Veredicto: viable / no viable / viable con límites. Si es viable, se convierte en una fase de implementación posterior; si no, se documenta el porqué y se cierra.

### Orden de fases

1. Prueba mínima: Neomoor con descripción visible en el tooltip (valida los módulos 2 y 6 de punta a punta).
2. Generador + registro completo de culturas base + respaldo universal.
3. Selector de genéricas + enganche de creación.
4. Banco de descripciones fijas, por tandas de herencia.
5. Plantillas del jugador.
6. Investigación de descripción editable.
7. Validador y pulido para publicación.

## Decisiones de Testing

- **Qué hace un buen test:** verifica el comportamiento externo del módulo (entradas → salidas observables), no sus detalles internos. Para el generador: dado un árbol de archivos de culturas de ejemplo, se comprueba el script generado y el informe de diferencias, no cómo se parsea internamente.
- **Módulo testeado con tests automáticos: solo el generador Python.** Casos mínimos:
  - Extrae todas las culturas de nivel superior de varios archivos y las agrupa por herencia.
  - Ignora comentarios, bloques anidados y claves que no son culturas.
  - Tolera BOM, tabs, CRLF y llaves en la misma línea o en la siguiente.
  - El script emitido tiene BOM y tabs reales.
  - El informe de diferencias detecta culturas añadidas y eliminadas.
  - Salida determinista (mismo input → mismo output byte a byte).
- **Framework:** `unittest` de la biblioteca estándar (pytest no está instalado; evitar dependencias). Fixtures con archivos de cultura mínimos escritos en el propio test.
- **Antecedentes:** los generadores Python de los mods Scoreboard (`gen_scoreboard.py`, `ladder.py`) son el precedente de herramientas de generación, aunque no tienen tests; JoyMapper es el precedente de trabajo con TDD.
- **Sin tests automáticos:** el script CK3 (registro, selector, enganche, plantillas, GUI) y el validador. Se verifican in-game: consola de depuración, crear culturas híbridas/divergentes, guardar/cargar, renombrar, y `error.log` limpio filtrando por el prefijo del mod.

## Fuera de Alcance

- **Descripción de texto libre** escrita por el jugador — el motor solo la ofrece para religiones (`OnEditDescription`); las ventanas de cultura solo exponen nombre, sustantivo colectivo y prefijo. Queda sujeta al resultado de la fase de investigación (módulo 8).
- Sección de descripción en la ventana de cultura (solo tooltip en esta versión).
- Otros idiomas distintos del inglés.
- CK3 vanilla y otros mods de conversión total.
- Compatibilidad avanzada con mods que también sobrescriban el tooltip de cultura (el último cargado gana; riesgo aceptado).
- Descripciones específicas para culturas añadidas por otros mods (reciben el respaldo).
- Generación procedural de textos.
- Cambios en mecánicas de hibridación, divergencia, tradiciones o innovaciones.
- Reescritura del lore existente de ATE.

## Notas Adicionales

- **Referencia técnica:** Elder Kings 2 (Workshop 2887120253) implementa el mecanismo central en su tooltip de cultura y en un efecto de registro llamado al inicio de partida; su respaldo es un único texto genérico. Este mod lo mejora con genéricas por herencia, respaldo en la GUI y generación automática del registro.
- **Contexto técnico completo:** `scripts/context.md`.
- **Volumen:** ~464 culturas base en 162 archivos y 107 herencias. Escribir las descripciones es el grueso del trabajo; el diseño permite publicar con cobertura parcial gracias al respaldo.
- **Riesgo de conflicto GUI:** el override del tooltip compartido chocará con cualquier otro mod que lo modifique. Mitigación: cambios mínimos y delimitados con comentarios para facilitar parches de compatibilidad.
- **Mantenimiento con ATE:** cada actualización de ATE puede añadir/renombrar culturas; el flujo es regenerar el registro, revisar el informe de diferencias y escribir las descripciones nuevas. ATE 0.22 "Stella Polaris" está anunciado y podría añadir culturas caribeñas/británicas.
- **Pendiente de verificar en la prueba mínima:** que `Culture.MakeScope.Var(...)` funcione igual en el tooltip de ATE que en Elder Kings, y cómo detectar en la GUI que la variable no existe para mostrar el respaldo.
