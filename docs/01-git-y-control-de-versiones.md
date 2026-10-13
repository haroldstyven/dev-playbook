# Git y control de versiones

## Commits

No hay una única convención impuesta en todos los proyectos — hay **dos** convenciones reales en uso, y ambas comparten la misma regla de fondo: **se elige una a propósito, se documenta en `CONTRIBUTING.md`, y se respeta de forma consistente dentro de ese repo.**

### Opción A — Conventional Commits

`feat(scope): descripción`, `fix: descripción`, `docs:`, `chore(deps):`, `test:`, `build:`, con referencia a issue/PR cuando aplica (`(#21)`). Es la opción por defecto para proyectos nuevos, especialmente si vas a usar herramientas que dependen de ese formato (changelogs automáticos, semantic-release, etc.).

### Opción B — Convención propia en lenguaje natural

`Área: efecto observable del cambio`, en el idioma del equipo. Válida cuando:
- El equipo ya tiene cientos de commits con ese formato y migrar rompería la legibilidad del historial.
- Conventional Commits no te está dando nada que la protección de rama / revisión de PR no te dé ya.

Si eliges la opción B, **dilo explícitamente** en `CONTRIBUTING.md` — de lo contrario cualquier colaborador nuevo (humano o agente) asumirá Conventional Commits por default y vas a terminar con un historial mezclado.

### Regla común a ambas

- El mensaje describe el **efecto observable**, no la lista de archivos tocados.
- **Nunca se añade un trailer de co-autoría de IA** (`Co-Authored-By: Claude...`) salvo que el autor del repo lo pida explícitamente. Los commits van a nombre de quien los autoriza.
- Commits atómicos: un commit, un cambio que tiene sentido revertir solo.

## Ramas

Prefijo semántico corto, mismo vocabulario entre proyectos: `feat/`, `fix/`, `chore/`, `docs/`, `refactor/`. El nombre después del prefijo es descriptivo y corto (`fix/overlap-boton-modal`, no `fix/bug-2`).

## `main` siempre desplegable

La rama principal (`main` o la que despliegue a producción) se trata como protegida incluso sin reglas de GitHub activas:

- Si algo rompe `main` después de un merge, la respuesta es **revertir el merge**, no parchear encima con un commit apurado.
- Si un proyecto tiene una rama de "producción" distinta de una rama de "desarrollo" (ej. `main` congelada, `dev` activa), eso se documenta explícitamente en el README o `CLAUDE.md` — un agente o colaborador nuevo no debería tener que inferirlo del historial.

## Higiene de repositorio

- **Limpieza automática de ramas mergeadas** vía GitHub Action (ver [`04-ci-cd-y-automatizacion.md`](04-ci-cd-y-automatizacion.md)) en vez de limpieza manual ocasional.
- Antes de cualquier operación destructiva de git (`reset --hard`, `clean -f`, `checkout` que descarta cambios) se revisa `git status` primero. Esto aplica igual si quien está operando es un agente de IA.

## Revisión de PR — primeras preguntas

Para proyectos multi-tenant o con datos de más de un cliente/usuario, la primera pregunta de cualquier revisión es si el cambio respeta el aislamiento entre tenants — antes de mirar estilo o nombres de variables. Para migraciones de base de datos, la pregunta previa al merge es si son **idempotentes** (`IF NOT EXISTS`, `ON CONFLICT DO NOTHING`), nunca después.
