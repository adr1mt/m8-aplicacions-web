# Guía de trabajo del módulo 0228

Manual de uso del repositorio: qué hay, cómo se edita y cómo se publica.
Está en castellano porque es documentación interna para el profesorado; todo
el material del alumnado va en catalán.

Las reglas de contenido y el manejo del repositorio están aquí. La estructura
de cada tipo de página está en `.claude/rules/redaccio.md`.

---

## 1. Qué es esto

Material didáctico del módulo **0228 · Aplicacions Web** (CFGM SMX, primer
curso), en HTML autocontenido, con la misma identidad visual (SINAPSI) y las
mismas herramientas que el módulo 0227. Parte del material original de
`rusben/smx-m08`, guardado tal cual en `_aportacio/`.

Cada RA tiene la misma forma: **teoría → guías (manuales) → actividades →
prácticas finales de instalación y configuración**. Las prácticas son lo que
más pesa en la nota del RA.

---

## 2. Estructura

```
smx-m08/
├── index.html          portal del módulo
├── GUIA.md · CLAUDE.md · README.md
├── aplica.py · revisa.py · editor.py · fes-web.py · publica.py
├── Programación_didactica/
│   ├── curriculum.py          trazabilidad con el currículum 0228
│   ├── cobertura.html         GENERADO
│   └── curriculum-oficial/    el currículum de la Generalitat
├── _identitat/         sinapsi.css, quiz.js y web-marc.html
├── _aportacio/         material original en Markdown, no se publica
├── _pendent/           RA en borrador que las herramientas no ven (RA2)
└── RA1/
    ├── index.html      índice del RA: fuente de la navegación
    ├── teoria/ · guies/ · activitats/ · img/
    └── _identitat/ra1-web.html   GENERADO
```

Las herramientas solo descubren carpetas `RA*` en la raíz. Un RA que aún no se
ha revisado se deja en `_pendent/` y se mueve a la raíz cuando esté listo.

---

## 3. Las herramientas

### `aplica.py` — lo que no se escribe a mano

```bash
python3 aplica.py          # todo el módulo
python3 aplica.py RA2      # solo un RA
```

Reescribe en todas las páginas las tres cosas que deben ser iguales en todas y que
no tiene sentido mantener a mano. Es idempotente: pasarlo dos veces seguidas
deja los ficheros igual, así que se puede lanzar siempre que haya dudas.

| Qué | De dónde sale |
|---|---|
| El bloque CSS entre los marcadores `SINAPSI-CSS` | `_identitat/sinapsi.css` |
| La barra superior y el pie de anterior/siguiente | El `index.html` de cada RA |
| Los enlaces a `T2.4`, `G4.1`, `A3.2`, `RA1`… | El mismo índice |

La navegación vive **fuera** de los bloques de contenido, entre los marcadores
`NAVEGACIÓ` y `PEU DE NAVEGACIÓ`, así que el editor nunca la toca: él escribe
los bloques y la cabecera, y este script, el resto. Los enlaces cruzados sí van
dentro de los bloques, pero nunca dentro de un `<pre>`, un `<code>` o un enlace
que ya existiera.

Para cambiar el estilo del módulo entero se edita `_identitat/sinapsi.css` y se
lanza esto; `revisa.py` confirma después que todas las páginas quedaron idénticas.
Si reordenas las páginas de un RA en su `index.html`, la navegación se rehace
sola.

### `editor.py` — editar textos sin tocar código

```bash
python3 editor.py
```

Abre `http://localhost:8765`, elige página y edita como si fuera un documento.
Un solo editor para todo el módulo: la portada lista todas las páginas agrupadas
por RA y por sección, con su referencia, su título y su número de bloques.

| Qué | Cómo |
|---|---|
| Editar texto | Clic sobre cualquier párrafo, título, celda o línea de código |
| Guardar | Botón **Desa** o `Ctrl+S` |
| Cambiar la etiqueta del bloque | Desplegable de la barra del bloque |
| Mover, duplicar, borrar bloque | Flechas, ⧉ y ✕ de esa misma barra |
| Añadir bloque | Botón **+ bloc nou al final**, y luego se sube a su sitio |
| Ver el ancho de móvil | Casilla **375 px** de la barra superior |
| Saltar de página | Flechas ‹ › o el desplegable |

Cada bloque muestra su **número de palabras**, en naranja al pasar de 135: es
el aviso de densidad, no un error. La regla es exactamente la de `revisa.py`
—el editor la importa de ahí, no la reimplementa—, así que solo se pinta en
naranja lo que el verificador también avisaría.

**Lo que el editor no puede romper**: el bloque CSS, la cabecera del documento
y el motor de los tests quedan fuera de su alcance. Mientras editas, los
botones de los tests están desactivados.

**Al guardar**: hace una copia con fecha en `_copies/`, escribe el `.html`
real, regenera la web de revisión de ese RA y vuelve a pasarle `revisa.py`,
cuyo resultado aparece en la barra. Si no has cambiado la estructura, el
fichero queda idéntico byte a byte salvo en lo que hayas tocado.

Si el puerto está ocupado, busca el siguiente libre y lo dice en la terminal.
Se para con `Ctrl+C`.

### `fes-web.py` — la web de revisión

```bash
python3 fes-web.py         # todos los RA
python3 fes-web.py RA2     # solo uno
```

Junta todas las páginas de un RA en un solo fichero navegable,
`RAn/_identitat/raN-web.html`, para repasarlo del tirón antes de subirlo. El
editor ya lo ejecuta solo en cada guardado; solo hace falta lanzarlo a mano si
editas los `.html` con otro programa.

La lista de páginas **sale del `index.html` del propio RA**: de ahí lee el
enlace, la referencia, el título y el subtítulo de cada una. No hay ninguna
lista que mantener en paralelo, así que no se puede desincronizar. Si el
índice enlaza una página que no existe, el script se para y lo dice.

### `Programación_didactica/curriculum.py` — la trazabilidad con el currículum

```bash
python3 Programación_didactica/curriculum.py
python3 Programación_didactica/curriculum.py --matriu
```

El diccionario `MAP` del propio script dice qué criterios de evaluación y qué
contenidos del currículum de la Generalitat trabaja cada página. De ahí salen
las dos cosas, así que no se pueden desincronizar:

- un bloque `currículum` al final de cada teoría y cada actividad, con el
  código y el enunciado del criterio;
- `Programación_didactica/cobertura.html`, la matriz de los ocho RA con la página que cubre cada
  criterio y los que quedan sin cubrir.

La matriz es documento de programación: `publica.py` no la copia a `docs/`, así
que no llega al alumnado. Al añadir una página nueva hay que darla de alta en
`MAP`: si no, no sale en la matriz.

### `revisa.py` — el verificador

```bash
python3 revisa.py          # todo el módulo
python3 revisa.py RA2      # solo un RA
```

Comprueba de una pasada la estructura HTML, los enlaces, los tests, la
separación entre tipos de página, el currículum, el CSS común y la densidad.
El propio `revisa.py` es la fuente de verdad de las comprobaciones automáticas;
no se mantiene otra lista completa en la documentación.

Para la cuenta de palabras solo mira la **prosa**: no cuenta el código, las tablas,
los diagramas ni el test. Lo enumerable debe ir en tabla o lista, así que contarlo
como prosa penalizaría precisamente hacerlo bien.

Los errores (`✗`) hay que corregirlos; los avisos (`·`) son criterio tuyo.
Devuelve código 1 si hay errores, y por eso `publica.py` lo usa de portero.

### `publica.py` — publicar la web

```bash
python3 publica.py                    # comprueba y regenera docs/
python3 publica.py --puja             # además, actualiza la web pública
python3 publica.py --sense-comprovar  # salta la comprobación
```

**Es el único comando que hace falta antes de publicar.** Pasa la cadena
entera —`aplica.py`, `Programación_didactica/curriculum.py --matriu` y
`revisa.py`— y si `revisa.py`
encuentra un solo error, se planta y no copia nada.

Copia a `docs/` el portal y las páginas de todos los RA. **No copia** los
documentos de origen, la carpeta `Programación_didactica/` ni las herramientas de
`_identitat/`.

Con `--puja`, vuelca `docs/` en el repositorio público del sitio y hace push.
La web tarda un minuto largo en reconstruirse.


---

## 5. Cómo está hecha una página

La forma del fichero, la lista de etiquetas `data-kind`, el orden de los
bloques de cada tipo de página y el formato de los tests están en
**`.claude/rules/redaccio.md`**, que es lo que lee Claude al abrir una página.
No lo dupliques aquí: se desincronizaría.

Lo que conviene saber para editar a mano: el bloque entre los marcadores
`SINAPSI-CSS` es **idéntico byte a byte** en todas las páginas y está extraído
en `_identitat/sinapsi.css`; para cambiarlo se edita ahí y se pasa `aplica.py`.
La navegación vive entre los marcadores `NAVEGACIÓ` y `PEU DE NAVEGACIÓ`, fuera
de los bloques, y también la escribe `aplica.py`.

### Prosa no copiable, código sí

La **prosa** lleva `user-select: none` en el CSS común y un bloque de
JavaScript que cancela `copy`, `cut` y `dragstart`. Es un **disuasorio**, no
una protección: quien mire el código fuente tiene el texto. Sirve para que el
material no se copie y pegue de un vistazo si alguien encuentra la web fuera
de Moodle.

**El código no entra en el trato.** Los `<pre>`, los `<code>` y las tablas se
seleccionan y se copian con normalidad: el alumnado trabaja por SSH y tiene
que pegar las órdenes. El menú contextual tampoco se bloquea, para poder
copiar un comando o abrir un enlace en otra pestaña.

Todo se desactiva además dentro de `[contenteditable="true"]`, así que el
editor sigue funcionando con normalidad.

---


---

## 6. Criterios de contenido

- Nivel: **primero** de CFGM SMX. Una idea y como máximo tres párrafos cortos
  por bloque; lo enumerable, en lista o tabla.
- Se respeta el material original: mismas actividades y prácticas (targeta
  Pokémon, landing page, zoo, portal WordPress, botigues…), actualizadas y
  atadas a los criterios de evaluación.
- La teoría explica la idea sin pasos. La guía documenta un producto. La
  actividad plantea un encargo con requisitos y comprobaciones. La
  **práctica final** es una actividad grande de instalación y configuración,
  con demostración en directo y `README.md` en GitHub.
- Pesos del RA1 (a validar con el departamento): actividades 30 %, práctica
  A1.6 50 %, práctica A1.7 20 %.

### Atadura curricular

Todo sale de `Programación_didactica/curriculum.py`; nada se escribe a mano:

| Qué | Dónde aparece |
|---|---|
| `MAP`: contenidos y criterios de cada página (incluidas las guías) | Bloque `currículum` al final de teorías y actividades; matriz `cobertura.html` |
| `PRACTIQUES`: apartados, criterios y puntos de cada práctica | Bloque `rúbrica` de la práctica; criterios de su bloque `currículum`; tabla de pruebas |
| `RA`, `CA`, `CONT` | Bloque «Al currículum» de `RA1/index.html` (entre los marcadores `CURRÍCULUM DEL RA`) |

`rubriques.py` genera la rúbrica de evaluación continuada de las actividades
(30 %): tres dimensiones que recogen los diez criterios del RA1, cada uno una
sola vez, con su trazabilidad. `--check` falla si queda un criterio fuera.
- Entorno: máquina nueva de isardVDI con Ubuntu 26.04 LTS, pila LAMP con
  MariaDB y PHP, nombres `.internal` resueltos con `/etc/hosts`. LXD y
  Vagrant quedan como alternativa en la G1.1.
- Nada de XAMPP/WAMP, 000webhost (cerrado) ni `mysql_*` de PHP: PDO y
  consultas preparadas.
- Las referencias son `T<ra>.<n>`, `G<ra>.<n>` y `A<ra>.<n>`.

---

## 7. Estado

- **RA1** revisado: 6 teorías, 4 guías, 5 actividades y 2 prácticas.
  Los pasos de las guías están contrastados con documentación, **no probados
  en VM**.
- **RA2** (Moodle) en `_pendent/`, sin revisar.
- **RA3–RA5** (gestión de archivos, ofimática web, correo y calendario web)
  sin material: el original de rusben no los cubre salvo `pt-uf2` y `uf1`.
