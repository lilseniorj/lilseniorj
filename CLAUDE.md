# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repositorio

Dos cosas que conviven pero no se mezclan:

1. **El README de perfil de GitHub de Jesús Vargas.** Este repo se llama igual que
   el usuario (`lilseniorj/lilseniorj`), así que GitHub muestra su `README.md` en
   github.com/lilseniorj. Por eso el repo debe seguir siendo público y el README
   debe quedarse en la raíz. Es lo único que se versiona aquí.

2. **El CV, que vive solo en local.** Los archivos `cv-jesus-vargas*.html`,
   `CV_*.pdf` y `CV_contenido_para_copiar.md` están en `.gitignore`
   deliberadamente: el HTML lleva el teléfono en texto plano y este repo es
   público. **No los quites del `.gitignore`, y no los commitees** — si entran una
   vez, el teléfono queda recuperable desde el historial para siempre.

No hay sistema de build, dependencias ni pruebas.

## Regenerar el PDF del CV

Se compila desde el HTML con Chrome headless. No hace falta instalar nada.

```bash
CH="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
"$CH" --headless --disable-gpu --no-pdf-header-footer --virtual-time-budget=6000 \
  --print-to-pdf="CV_Jesus_Vargas_FullStack.pdf" "file://$PWD/cv-jesus-vargas.html"
```

Hay dos variantes con contenido idéntico y encabezados distintos:
`cv-jesus-vargas.html` (a la izquierda, técnico) y `cv-jesus-vargas-centrado.html`
(centrados en serif, editorial) → `CV_Jesus_Vargas_Editorial.pdf`.

Verificar siempre el resultado:

```bash
python3 -c "
import re
d=open('CV_Jesus_Vargas_FullStack.pdf','rb').read()
print('Páginas:', len(re.findall(rb'/Type\s*/Page[^s]', d)))
print('Enlaces:', len(re.findall(rb'/Subtype\s*/Link', d)))
print('Imágenes:', len(re.findall(rb'/Subtype\s*/Image', d)))"
```

**El objetivo son exactamente 2 páginas.** Si salen 3, recorta contenido —quita el
proyecto menos relevante, funde dos viñetas, sintetiza certificaciones por
competencia— en lugar de reducir el tamaño de letra. Después, lee el PDF con la
herramienta Read para revisarlo visualmente: viñetas huérfanas al pie, líneas
desbordadas, secciones partidas.

`Imágenes: 0` confirma que el PDF es texto real y no un escaneo, que es lo que
permite a los filtros ATS leerlo.

## Trampas del CSS de impresión

- **El margen va en `@page`, no como `padding` de `.sheet`.** El padding de un
  bloque que se parte en varias páginas se aplica una sola vez al bloque entero,
  así que la segunda página arrancaba pegada al borde del papel. Ya está corregido
  a `@page { size: A4; margin: 12mm 15mm; }` con `.sheet { padding: 0 }` en print.
- **El texto justificado necesita `hyphens: auto` y `lang="es"`** en `.sheet`, o se
  abren ríos de espacio entre palabras. Las clases monoespaciadas (`.contact`,
  `.when`, `.stack`, `.skills dd`, `footer`) están excluidas a propósito.
- **Los colores están tematizados** con tokens para claro, oscuro y `data-theme`.
  El acento es distinto en cada tema (`#0e7490` claro / `#22d3ee` oscuro) para que
  se vea igual de vivo en ambos. En impresión se fuerza el tema claro.

## De dónde sale el contenido del CV

**No lo escribas de memoria ni lo infieras.** La base de hechos verificados vive
fuera del repo, en `~/.claude/skills/cv/datos-verificados.md`, con la fuente de
cada dato y una sección "Prohibido afirmar". La skill `/cv` la usa para generar
versiones adaptadas a una oferta concreta.

Reglas que no se rompen, y que salieron de errores reales:

- **El trabajo con clientes empieza en 2026, no en 2024.** Una versión anterior
  copió "2024" de un CV viejo; el historial de git demostró que el primer proyecto
  de cliente fue abril de 2026. Verificar fechas contra `git log`, no contra
  documentos previos.
- **No inventar métricas de negocio** — ingresos, usuarios, porcentajes de mejora,
  tiempo ahorrado. No las tiene. Si una oferta las pide, preguntarle a él.
- **Inglés B1.** No declarar un nivel superior.
- **Toda la experiencia es freelance individual.** Nunca ha tenido empleo formal ni
  ha liderado equipo.
- **No adaptar el CV a ofertas cuyo stack no ha tocado.** Reordenar lo que existe es
  válido; inventar no.

## Enlaces: verificar antes de añadir

Varios sitios de cliente se cayeron cuando el hosting de Firebase dejó de pagarse,
y algunos repos de cliente son privados. Un enlace muerto en un CV o en el README
es peor que no tener enlace. Comprobar siempre antes de publicar uno:

```bash
curl -s -o /dev/null -w "%{http_code}" -L --max-time 12 "<url>"
```

Además, hay sitios que responden 200 pero **no deben enlazarse**: `soidentist.web.app`
es el sistema clínico interno de una clienta, y `chuky-tattoo.web.app` está publicado
pero sin lanzar, con `noindex` y datos pendientes del cliente.

LinkedIn devuelve `999` por su bloqueo anti-bots; no es un enlace roto.

## Convenciones de commit

Los mensajes van **en español, en imperativo**, siguiendo el historial existente
("Quita la tarjeta de estadísticas de GitHub", "Agrega badge de Platzi al contacto").
El cuerpo explica *por qué*, no *qué* — el diff ya dice qué cambió.
