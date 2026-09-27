# Plan: Descripciones culturales para After the End

> PRD de origen: `prds/prd-descripciones-culturales-ate.md` · Contexto técnico: `scripts/context.md`

## Decisiones arquitectónicas

Decisiones duraderas que aplican a todas las fases:

- **Plataforma**: submod de After the End para CK3 1.19, solo inglés. Carga después de ATE y no sobrescribe archivos de ATE. Todo archivo `.txt`/`.yml` en UTF-8 con BOM y tabs reales.
- **Prefijo del mod**: `ate_cd_` para variables, flags, efectos, eventos, decisiones y claves de localización.
- **Modelo de datos (por cultura)**:
  - `ate_cd_culture_key` — variable de cultura con un flag cuyo nombre es la raíz de la clave de localización de su descripción. Es la única fuente de verdad sobre qué texto muestra una cultura.
  - `ate_cd_original_key` — variable de cultura con la clave asignada antes de elegir cualquier plantilla; permite restaurar.
- **Convención de claves de localización**:
  - Culturas base: `ate_cd_<id_cultura>_desc`.
  - Genéricas: `ate_cd_generic_<familia>_<hybrid|divergent>_desc`.
  - Plantillas: `ate_cd_template_<n>_desc`.
  - Respaldo universal: `ate_cd_fallback_desc`.
  - La interfaz construye la clave como `<valor del flag>` + sufijo fijo y la localiza (técnica de Elder Kings 2).
- **Visualización**: solo en el tooltip de cultura compartido del juego base (ATE no lo sobrescribe). Texto multilínea con `max_width` obligatorio. Respaldo mediante widgets con visibilidad complementaria, nunca `SelectLocalization`.
- **Regla del registro**: solo se registran culturas base cuya descripción ya está escrita; el resto muestra el respaldo. El registro es **idempotente**: nunca pisa una `ate_cd_culture_key` existente. Se ejecuta al inicio de partida y se re-ejecuta en un pulso global periódico para llegar a partidas guardadas y a descripciones añadidas en versiones posteriores.
- **Creación de culturas**: handler propio agregado a `on_culture_created` de forma aditiva (sin override); root = cultura nueva, `scope:founder` = fundador.
- **Clasificación de genéricas**: las 107 herencias de ATE se agrupan en familias; tipo = híbrida (dos culturas padre) o divergente (una). La cadena de selección termina siempre en el respaldo universal.
- **Herramientas**: Python 3 solo con biblioteca estándar. ATE se lee en solo lectura desde la carpeta del Workshop. Tests con `unittest`. Las herramientas viven en `scripts/` (no se sincronizan a la carpeta de Paradox).
- **Verificación in-game**: consola de depuración + `error.log` filtrado por `ate_cd_` sin entradas nuevas.

---

## Fase 1: Prueba mínima con Sunshiner — ✅

**Historias de usuario**: 1, 7, 8, 9, 10

### Qué construir

Camino completo para una sola cultura: al iniciar una partida nueva, Sunshiner recibe su `ate_cd_culture_key`; el tooltip de cultura, en cualquier lugar donde aparezca Sunshiner, muestra su descripción de 2–4 frases en inglés debajo del contenido habitual, separada por un divisor. El resto de las culturas se ven exactamente como antes. Valida de punta a punta la técnica de variable de cultura + clave concatenada dentro del tooltip de ATE.

### Criterios de aceptación

- [x] En una partida nueva, el tooltip de Sunshiner muestra su descripción desde el personaje y el condado. *(OK 2026-09-26. La ventana de cultura no muestra el tooltip de su propia cultura → no aplica; mostrar la descripción dentro de la ventana requiere override de `window_culture.gui`, fuera de alcance del PRD)*
- [x] El texto de prueba (lorem ipsum, 3 frases) se ve completo y bien ajustado al ancho. El contenido real se escribe en la fase 6.

> Cambio 2026-09-26: se usa Sunshiner en vez de Neomoor porque Neomoor solo existe en el mod AteNeomoorCulture, no en ATE (el mod es ATE-only).
- [~] *(Saltada por decisión del usuario 2026-09-26 — las descripciones reales tienen espacios y el lorem ipsum ya se ajusta a 400px)* Un texto de ~260 caracteres sin espacios no estira el tooltip (el `max_width` limita el ancho) y los tooltips de al lado siguen respondiendo al mouse.
- [x] Las demás culturas no muestran cambios visibles ni claves crudas. *(Dixie OK 2026-09-26)*
- [x] `error.log` sin entradas nuevas relacionadas con el mod. *(tras el arreglo del flag, sesión 14:41 limpia)*
- [x] Anotado en `scripts/context.md` cómo se detecta en la GUI que la variable no existe (insumo para la fase 2).

---

## Fase 2: Respaldo universal — ✅

**Historias de usuario**: 17, 31, 32

### Qué construir

Toda cultura sin `ate_cd_culture_key` (culturas base aún sin texto, culturas de otros mods) muestra en su tooltip el texto de respaldo neutro, que sirve para cualquier cultura y puede nombrarla dinámicamente. Ningún tooltip de cultura muestra nunca una clave cruda ni un hueco vacío.

### Criterios de aceptación

- [x] Una cultura base sin registrar muestra el respaldo, con su propio nombre insertado correctamente. *(Dixie OK 2026-09-26)*
- [x] Sunshiner sigue mostrando su descripción específica, sin el respaldo debajo.
- [x] El respaldo se ve correctamente en partidas nuevas y cargadas. *(es solo GUI: no depende de estado guardado)*
- [x] Ninguna entrada `Data error in loc string` ni `data context` en `error.log`. *(sesión 14:48 limpia)*

---

## Fase 3: Generador y registro de todas las culturas base — ✅

**Historias de usuario**: 28, 29, 30, 34, 35, 36, 37, 40

### Qué construir

Una herramienta Python, desarrollada con TDD, que lee las culturas de ATE y las descripciones ya escritas del mod, y genera el script de registro solo para las culturas que tienen texto, agrupado por herencia. También emite un informe de diferencias contra el registro anterior (culturas añadidas/eliminadas en ATE). El registro generado se ejecuta al inicio de partida y en un pulso global periódico, de forma idempotente. Al terminar la fase, el registro de Neomoor hecho a mano se reemplaza por el generado.

### Criterios de aceptación

- [x] Tests `unittest` en verde (14 tests, 2026-09-26) para: extracción de culturas de nivel superior en varios archivos, agrupación por herencia, ignorar comentarios y bloques anidados, tolerar BOM/tabs/CRLF/llaves en distintas líneas, salida con BOM y tabs reales, salida determinista, detección de culturas añadidas y eliminadas, inclusión solo de culturas con descripción escrita.
- [x] El generador corre contra la carpeta real de ATE y encuentra **422** culturas (106 herencias en uso; el ~464 del skill era incorrecto).
- [x] Añadir mid-game una descripción nueva y cargar una partida guardada: tras el pulso, la cultura muestra su texto. *(Dixie: respaldo el 4-jul-2666 → su texto el 1-ene-2667, 2026-09-27)*
- [x] Una cultura con clave previa no se sobrescribe al re-ejecutarse el registro. *(Sunshiner conservó su texto tras el pulso; el caso clave-distinta se re-prueba en la fase 7)*
- [x] Renombrar una cultura conserva su descripción. *(por diseño: la clave vive en una variable de la cultura; CK3 no permite renombrar culturas ya creadas, no se puede probar directo)*
- [x] Añadir el mod a una partida guardada sin el mod: todas las culturas muestran su descripción o el respaldo. *(cubierto por la prueba de Dixie: respaldo hasta el pulso, luego su texto)*

---

## Fase 4: Genéricas automáticas al crear una cultura — ✅

**Historias de usuario**: 11, 12, 13, 14, 15, 16, 18

### Qué construir

Cuando nace una cultura híbrida o divergente (del jugador o de la IA), recibe automáticamente una clave genérica elegida por familia de herencia de ATE y por tipo de creación. Las genéricas pueden nombrar a la cultura y a sus culturas padre. Si ninguna familia encaja, cae al respaldo universal. La asignación es permanente.

### Criterios de aceptación

- [x] Documentado el agrupamiento de las herencias en familias. *(106 herencias en 15 familias + `fallback: dead_pre_event`, aprobado 2026-09-27, en `scripts/heritage_families.txt`; el generador avisa de herencias sin familia)*
- [x] Una cultura híbrida muestra la genérica híbrida de su familia. *(Gullah-Sunshiner → "(southern, hybrid)", 2026-09-27. Nombres de culturas padre: pendiente, no hay accessor en la GUI)*
- [x] Una cultura divergente muestra la genérica divergente de su familia. *(Gatorfolk → "(southern, divergent)", 2026-09-27)*
- [x] Una cultura con herencia sin familia asignada muestra el respaldo. *(cubierto por tests del selector: familia `fallback` sin rama; en ATE solo aplica a dead_pre_event)*
- [x] Una cultura creada por la IA recibe su genérica. *(forzado por consola 2026-09-27: el jefe de Dixie (IA) hibridó con Sunshiner → Sunshiner-Dixie muestra "(southern, hybrid)"; error.log limpio)*
- [x] La genérica persiste tras guardar y cargar. *(Gatorfolk y Gullah-Sunshiner tras cargar, 2677; el pulso del 1-ene no las pisó)*
- [x] El handler convive con el `on_culture_created` de ATE (ccu) sin alterar su comportamiento. *(0 errores de script en archivos ate_cd ni en ccu tras crear 2 culturas)*

---

## Fase 5: Validador — ✅

**Historias de usuario**: 38

### Qué construir

Una herramienta Python que cruza culturas de ATE, registro y localización, y produce un informe legible: culturas sin descripción (agrupadas por herencia para planificar tandas), claves huérfanas, archivos sin BOM, textos fuera de 2–4 frases y claves genéricas/plantillas faltantes. Sirve como check previo a cada publicación.

### Criterios de aceptación

- [x] Corre contra el estado real del mod y reporta la cobertura (culturas con texto / total). *(2/422, 0 errores, 2026-09-27)*
- [x] Detecta un archivo sin BOM, una clave huérfana y un texto de 5 frases puestos a propósito. *(copia temporal con 8 fallos: los 8 detectados, exit 1)*
- [x] Detecta una genérica o plantilla referenciada por script pero sin localización. *(genéricas: sí; plantillas: se añadirá en la fase 7 cuando existan)*
- [x] Documentado en `scripts/context.md` cómo ejecutarlo.

---

## Fase 6: Tandas de descripciones fijas — ✅ genéricas / ⏳ textos específicos (comunidad)

> **Cambio de enfoque 2026-09-27.** En vez de escribir las 422 culturas por tandas:
> - Claude escribió **45 genéricas reales** (15 familias × base/hybrid/divergent). Toda cultura muestra un texto acorde a su región. Verificado en el juego.
> - Los textos específicos por cultura los escribe la **comunidad** en la hoja (https://docs.google.com/spreadsheets/d/1l1CXGpG0bm9sCHKsWecNKvg0Pni-3jLZ94Ml3ykzkDM/edit). **Sin importador**: el usuario descargará la hoja y se pasan los textos juntos. El flujo por tanda de abajo sigue valiendo para ese momento.
> - Línea de feedback bajo cada descripción que invita a mejorarla en la hoja.

**Historias de usuario**: 2, 3, 4, 5, 6, 39

### Qué construir

Escribir descripciones para culturas base por tandas de familia de herencia, empezando por Florida y el Caribe (sunshiner y sus vecinas; Neomoor no es de ATE). Cada tanda termina regenerando el registro, pasando el validador y verificando en el juego. Esta fase se repite hasta cubrir todas las culturas; cada tanda es publicable.

### Criterios de aceptación (por tanda)

- [ ] Cada cultura de la tanda tiene 2–4 frases en inglés con origen, rasgos, relación con culturas vecinas/padre y contexto geográfico, en el tono de ATE.
- [ ] Registro regenerado; el informe de diferencias no muestra sorpresas.
- [ ] Validador sin errores para la tanda.
- [ ] Muestreo en el juego: 3 culturas de la tanda muestran su texto en una partida nueva y en una cargada.

---

## Fase 7: Plantillas del jugador — ❌ OMITIDA

> Omitida por decisión del usuario el 2026-09-27. No se escribió código. Los jugadores mejoran las descripciones a través de la hoja comunitaria en vez de plantillas dentro del juego.

**Historias de usuario**: 19, 20, 21, 22, 23, 25, 26, 27

### Qué construir

Una decisión visible solo para el jugador que es jefe de su cultura, que abre un evento con varias plantillas (cada opción muestra una vista previa del texto) y una opción para restaurar la descripción original. Elegir una plantilla cambia la clave de la cultura y guarda la original la primera vez. Puede usarse más de una vez. La IA no la ve ni la usa.

### Criterios de aceptación

- [ ] La decisión aparece solo para el jefe cultural jugador y tiene imagen (sin spam de `No valid picture found`).
- [ ] Cada opción muestra la vista previa de su plantilla.
- [ ] Tras elegir, el tooltip de la cultura muestra la plantilla; restaurar devuelve la descripción original (específica, genérica o respaldo).
- [ ] La elección persiste al guardar y cargar, y el pulso de registro no la pisa.
- [ ] Un jugador que no es jefe cultural no ve la decisión.
- [ ] `error.log` limpio.

---

## Fase 8: Escribir la descripción propia al crear una cultura — ⚠️ APROBADA (pendiente del prototipo)

> 2026-09-27: el usuario mantiene la fase (solo se omitió la 7), pero la definición original dependía de las plantillas. **Redefinición aprobada** por el usuario el 2026-09-27, a partir del informe de la fase 10 (`scripts/fase10_informe_texto_editable.md`): el jugador escribe texto libre en un evento. Definición original (elegir plantilla) descartada. Orden acordado: primero publicar lo ya terminado (commit, Workshop, hoja, Reddit) y después el prototipo.

**Historias de usuario**: 24, 41

### Qué construir

Justo después de que el jugador crea una cultura híbrida o divergente, recibe un evento con un **campo de texto** (`event_window_widget_enter_text`) para escribir la descripción de su cultura, o la opción de quedarse con la genérica asignada. El texto se guarda en el personaje autor (`store_localized_text_in_death`) y la cultura guarda una variable que apunta a él (`ate_cd_text_holder`). El componente GUI compartido muestra ese texto con prioridad sobre la genérica, en el tooltip y en la ventana. La IA no recibe el evento.

**Paso 0 — prototipo:** un evento de prueba lanzado por consola sobre la cultura del jugador, antes de construir la fase completa. Si falla guardar/cargar o la muerte del autor, se descarta el texto libre y se registra en `scripts/context.md`; si solo falla lo de los caracteres especiales, sigue siendo viable.

### Criterios de aceptación

- [ ] Prototipo: el texto escrito aparece en el tooltip y en la ventana de la cultura.
- [ ] Prototipo: el texto sobrevive a **guardar y cargar**.
- [ ] Prototipo: el texto sobrevive a la **muerte del autor** (matar por consola, guardar, cargar).
- [ ] Prototipo: `[ ]`, `$` y `#` escritos por el jugador no se interpretan como código.
- [ ] Crear una cultura como jugador dispara el evento una sola vez; la IA no lo recibe.
- [ ] "Quedarse con la genérica" no cambia nada.
- [ ] Si el autor es purgado o el texto se pierde, la cultura vuelve a su genérica sin mostrar texto vacío ni clave cruda.
- [ ] Una descripción por personaje (opción (a) del riesgo 4, la clave del widget es fija): el evento no se ofrece si el personaje ya describió otra cultura que sigue existiendo; su heredero sí puede describir la suya.
- [ ] Campo de varias líneas con tope de caracteres: copia propia del widget con `multiline = yes` y `maxcharacters` (como el campo de descripción de religión), más `max_width` en la GUI.
- [ ] `error.log` sin entradas del mod.

---

## Fase 9: Descripción en la ventana de cultura — ✅

**Historias de usuario**: 44, 45, 46, 47

> Añadida 2026-09-27 a pedido del usuario (antes estaba fuera de alcance: solo tooltip). La antigua fase 9 pasa a ser la 10.

### Qué construir

La ventana de cultura (la que se abre con "Click to view <cultura>") muestra la misma descripción que el tooltip (texto específico, genérica o respaldo) en una sección propia, junto con la línea de invitación a mejorarla. La lógica de qué texto mostrar vive en **un solo tipo de GUI compartido** por el tooltip y la ventana, para no duplicarla. Override de `window_culture.gui` (ATE no lo sobrescribe), con cambios mínimos y delimitados con `#ATE_CD ADDITION`.

### Criterios de aceptación

- [x] Decidida la ubicación: justo debajo del banner del ethos, en "Traditions and Pillars" (decisión del usuario 2026-09-27).
- [x] La ventana muestra la descripción correcta para una cultura con texto propio (Sunshiner) y una ajena (Dixie). *(híbridas/divergentes y respaldo usan el mismo componente ya probado en el tooltip; el ancho del respaldo en la ventana no se verificó visualmente)*
- [x] La línea de feedback aparece bajo la descripción.
- [x] El texto no deforma la ventana; tradiciones y pilares siguen visibles y con tooltip (Southern Knights en Dixie).
- [x] Tooltip y ventana usan el mismo tipo de GUI (`ate_cd_culture_description`).
- [x] La ventana funciona igual para la cultura propia y para culturas ajenas.
- [x] `error.log` sin entradas del mod. *(sesión 14:57)*

---

## Fase 10: Investigación: descripción editable al crear una cultura — ✅ INFORME HECHO: VIABLE CON LÍMITES

> 2026-09-27. Informe: `scripts/fase10_informe_texto_editable.md`. Resumen: las ventanas de cultura no aceptan texto, pero el widget de evento `event_window_widget_enter_text` sí. El motor guarda texto libre **solo en personajes** (`Character.GetLocalizedText`; `copy_localized_text` / `store_localized_text_in_death` / `remove_localized_text` → `Supported Scopes: character`; no existe `Culture.GetLocalizedText`, confirmado con `dump_data_types` y `script_docs`). La implementación y las pruebas de persistencia pasan a la **fase 8 redefinida**.

**Historias de usuario**: 41, 42, 43

### Qué construir

Un spike con entregable un informe (no código de producción) que determina si el jugador puede escribir su propia descripción al crear una cultura. Vías: volcar los *data types* del motor y buscar funciones de texto/descripción en las ventanas de hibridación/divergencia o en `Culture`; probar si una caja de texto puede escribir en el sistema de variables de la GUI y si ese valor persiste al guardar/cargar y en multijugador; evaluar reutilizar un campo de texto existente de la cultura; revisar mods de la Workshop con intentos similares.

### Criterios de aceptación

- [x] Informe en `scripts/` con cada vía explorada, evidencia y resultado.
- [x] Veredicto explícito: **viable con límites**.
- [~] Si es viable: propuesta de implementación añadida (fase 8 redefinida). Guardar/cargar y muerte del autor → se prueban en el prototipo de la fase 8. Multijugador: sin probar (las mascotas usan el mismo mecanismo).
- [x] ~~Si no es viable~~: no aplica. Veredicto registrado en `scripts/context.md`.
