---
paths:
  - "RA*/**/*.html"
  - "index.html"
---

# Cómo se escribe una página

## Forma del fichero

```html
<head>  … <style> /* === SINAPSI-CSS INICI */ … /* === SINAPSI-CSS FI */ </style>
<body>
  <header class="cq-header"> …título (h1) y entradilla… </header>
  <div class="cq-block" data-kind="objectius"> <h2>…</h2> … </div>
  …
  <script> …motor del test… </script>
```

El bloque entre los marcadores `SINAPSI-CSS` es **idéntico byte a byte** en
todas las páginas, extraído en `_identitat/sinapsi.css`. Para una página nueva
se copia de otra sin tocarlo; si hay que cambiarlo, se cambia ahí y lo propaga
`aplica.py`.

La navegación vive **fuera** de los bloques, entre los marcadores `NAVEGACIÓ` y
`PEU DE NAVEGACIÓ`, y la escribe `aplica.py` a partir del `index.html` del RA.
No se toca a mano.

## Etiquetas de bloque (`data-kind`)

`objectius` · `teoria` · `referència` · `exemple` · `cas real` · `escenari` ·
`enunciat` · `pas a pas` · `recordatori` · `observació` · `bones pràctiques` ·
`problemes` · `comprovació` · `resum` · `lliurament` · `currículum` · `quiz`

- `teoria` es **solo** de las páginas de teoría.
- Dentro de una guía, el bloque que explica la sintaxis del producto (los
  cuatro bloques de `kea-dhcp4.conf`, las directivas de `vsftpd.conf`) es
  `referència`: explica el producto, no el servicio.
- Los procedimientos de **diagnóstico** (`dig MX`, `dig +trace`, la sesión FTP,
  el `tcpdump` del DORA) van en la teoría como `observació`. Así `pas a pas`
  significa siempre «guía».
- `currículum` cierra las teorías, las actividades y las prácticas (salvo en `RAX/`), **después** de la
  navegación de contenido y antes del pie. No se escribe a mano: lo genera
  `curriculum.py`, y una edición manual se pierde en la siguiente pasada.
- Inventar una etiqueta nueva es válido: sale en neutro y no rompe nada.

Cuatro tienen color propio: `recordatori` (naranja), `bones pràctiques`
(verde), `problemes` (rojo) y el grupo azul de `objectius`, `pas a pas`,
`lliurament` y `resum`.

## Orden de los bloques

**Teoría**: objetivos → explicación → funcionamiento interno → ejemplo → caso
real → `observació` (solo si hay algo que mirar) → buenas prácticas → problemas
habituales → resumen → test → `currículum`.

**Guía** (`guies/g<ra>-<n>-<producte>.html`, etiqueta `G<ra>.<n>`): objetivos
(qué cubre, de qué se parte, enlace a la documentación oficial) → `referència` y
`pas a pas`, los que hagan falta → `comprovació` → buenas prácticas → problemas
habituales → `resum` en forma de ficha rápida. **Sin test.** El `comprovació` es
la comprobación de conjunto, y desde el cliente siempre que se pueda: no repite
lo que ya verifica el último paso de cada `pas a pas`.

**Práctica** (`practiques/p<ra>-<n>-<nom>.html`, etiqueta `P<ra>.<n>`): misma
forma que una actividad, con el enunciado dividido en fases; `curriculum.py`
añade el bloque `rúbrica` antes del `currículum`.

**Actividad**: objetivos → escenario → enunciado → de dónde sacar cada cosa
(apunta a la **guía**) → recordatorio → observación técnica → comprobación →
problemas → entrega. Sin paso a paso ni ficheros de configuración completos. En
comprobación **sí** van comandos de verificación: verificar no es resolver.
Cierra con `currículum`, con los criterios de evaluación que se evalúan.

## Tests

```html
<div class="cq-question" data-id="t2-3-refused" data-type="single">
  <div class="statement">La pregunta</div>
  <button type="button" aria-pressed="false" class="cq-option"
          data-correct="true">Opció bona</button>
  <button type="button" aria-pressed="false" class="cq-option"
          data-correct="false">Opció dolenta</button>
  <div><button type="button" class="cq-check-btn">Comprova la resposta</button></div>
  <div class="cq-feedback" role="status" aria-live="polite"></div>
</div>
```

Reutiliza esta estructura y cambia el texto, las opciones y el `data-id`.
`revisa.py` valida los tipos, las respuestas y la unicidad de los
identificadores.

Los atributos no son decorativos: el `type="button"` evita que un test dentro
de un formulario de Moodle envíe la página, el `aria-pressed` lo mantiene al
día el motor del test, y el `aria-live` hace que un lector de pantalla anuncie
la corrección.

Los `cq-question` son **solo de las teorías**. Las actividades no llevan test:
la pregunta de justificación va en prosa dentro del bloque `lliurament`.

## Detalles no automatizados

- Dentro de un `<pre>`, la salida del comando va en `<span class="o">` (verde) y
  los comentarios en `<span class="c">`. Sin el `$` del prompt delante.
- Sin desbordamiento horizontal a 375 px.
- Las notas `cq-note` de versión solo dicen cosas comprobadas en 26.04; si una
  nota no aporta nada, no se pone.

El resto de requisitos mecánicos los comprueba `revisa.py` y no se duplican
aquí.
