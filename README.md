# dev-playbook

Guía de metodología y buenas prácticas que uso de forma consistente a través de mis proyectos: apps nativas (iOS/Android), backends y APIs, automatización, scripts de datos, y desarrollo asistido por IA con Claude Code.

No es un estándar teórico. Cada práctica aquí descrita se extrajo revisando la configuración real (`CLAUDE.md`, `.claude/`, CI, linters, convenciones de git) de más de 20 proyectos activos, y se mantiene porque sigue sobreviviendo revisión tras revisión — no porque "así se debe hacer".

## Por qué existe esto

Para no reinventar las mismas decisiones en cada proyecto nuevo, y para tener algo concreto que compartir con socios y colegas cuando empezamos a trabajar juntos: qué esperar de un repo mío, cómo está organizado, y qué convenciones seguir si van a contribuir.

## Cómo usar esta guía

- Si vas a **empezar un proyecto nuevo**: copia las plantillas de [`templates/`](templates/) y ajusta lo que no aplique.
- Si vas a **contribuir a uno de mis repos**: lee [`docs/01-git-y-control-de-versiones.md`](docs/01-git-y-control-de-versiones.md) primero.
- Si vas a **trabajar con Claude Code / IA** sobre uno de estos proyectos: lee [`docs/03-trabajo-con-ia-claude-code.md`](docs/03-trabajo-con-ia-claude-code.md).
- Si quieres el **checklist de "listo para mergear"**: ve a [`checklist-definition-of-done.md`](checklist-definition-of-done.md).

## Índice

| Documento | Contenido |
|---|---|
| [`docs/01-git-y-control-de-versiones.md`](docs/01-git-y-control-de-versiones.md) | Convenciones de commits, ramas, protección de `main`, limpieza de ramas |
| [`docs/02-estructura-de-proyecto-y-documentacion.md`](docs/02-estructura-de-proyecto-y-documentacion.md) | Estructura de README, `.env.example`, honestidad sobre deuda técnica |
| [`docs/03-trabajo-con-ia-claude-code.md`](docs/03-trabajo-con-ia-claude-code.md) | Qué va en un `CLAUDE.md`, skills como dependencias versionadas, planificar antes de implementar |
| [`docs/04-ci-cd-y-automatizacion.md`](docs/04-ci-cd-y-automatizacion.md) | CI mínimo viable, secret scanning, automatización de higiene de repo |
| [`docs/05-calidad-seguridad-y-dependencias.md`](docs/05-calidad-seguridad-y-dependencias.md) | Linters por lenguaje, manejo de secretos, pin de versiones |
| [`checklist-definition-of-done.md`](checklist-definition-of-done.md) | Lista de verificación antes de abrir/mergear un PR |
| [`templates/`](templates/) | Plantillas copiables: `README.md`, `CLAUDE.md`, `CONTRIBUTING.md`, `.env.example` |

## Principios detrás de todo esto

1. **Documentar el fallo silencioso, no solo la arquitectura.** Un documento de proyecto es más útil cuando dice "esto rompe así, en este caso concreto" que cuando describe capas y componentes que ya son legibles en el código.
2. **Elegir la convención a propósito y escribir por qué.** Conventional Commits no es obligatorio; una convención propia tampoco. Lo que no es negociable es que la elección esté documentada y sea consistente dentro del repo.
3. **`main` (o la rama principal) siempre desplegable.** Si se rompe, se revierte — no se arregla a las apuradas sobre la misma rama rota.
4. **Nada de secretos en el repo, siempre con ejemplo.** Todo proyecto con configuración sensible lleva su `.env.example` actualizado; el escaneo de secretos en CI es la red, no la primera línea de defensa.
5. **Las skills y la configuración de IA se versionan, no se improvisan sesión a sesión.** Si una instrucción para el agente se repite, se documenta una vez en `CLAUDE.md` o se empaqueta como skill — no se repite en el prompt cada vez.
6. **Honestidad sobre el estado del proyecto.** Un README que dice "esto está incompleto, falta X" es más útil que uno que aparenta estar terminado.

## Licencia

MIT — úsalo, cópialo, adáptalo a tu propio flujo.
