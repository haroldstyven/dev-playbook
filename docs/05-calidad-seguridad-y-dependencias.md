# Calidad, seguridad y dependencias

## Linters: selección consciente, no "todo activado"

En cada lenguaje, la configuración de lint es una **selección deliberada de reglas con un propósito escrito**, no la config por defecto más estricta posible:

- **Python**: un set de reglas enfocado en bugs reales (errores de lógica, imports rotos) en vez de estilo — para no generar ruido que nadie atiende. Documentar por qué se excluyó algo, no solo qué se incluyó.
- **Go**: linters habilitados explícitamente uno por uno (`gosec`, `errcheck`, `bodyclose`, etc.), no `enable-all`. Cada linter activado tiene una razón de seguridad o correctness detrás.
- **TypeScript/JS**: la herramienta se elige según el tamaño del proyecto — ESLint clásico para proyectos con reglas de framework específicas (Next.js), un linter más rápido (oxlint) para proyectos pequeños donde la velocidad de feedback importa más que la cobertura de reglas.

Regla de fondo: un linter con 40 reglas que el equipo realmente corrige vale más que uno con 200 reglas que todos ignoran.

## Pin de versiones de runtime

Cada proyecto fija la versión del lenguaje que usa (`.nvmrc`, `.python-version`, o equivalente) en vez de depender de "lo que haya instalado localmente". Esto evita el clásico "en mi máquina funciona" causado por una versión distinta de Node/Python/Go.

## Gestión de dependencias

- Preferir gestores modernos con lockfile reproducible (`uv` + `uv.lock` para Python, `package-lock.json`/`pnpm-lock.yaml` para Node) sobre `requirements.txt` plano cuando el proyecto es nuevo.
- Las actualizaciones de dependencias van en su propio commit/PR (`chore(deps):` o equivalente), separadas de cambios funcionales — para que un bug nuevo no se confunda entre "cambié la lógica" y "subí una versión".

## Secretos

- **Nunca en el repo**, ni en commits viejos del historial, ni en archivos de configuración de ejemplo con valores reales.
- `.env.example` siempre actualizado (ver [`02-estructura-de-proyecto-y-documentacion.md`](02-estructura-de-proyecto-y-documentacion.md)).
- Escaneo de secretos en CI como red de seguridad (ver [`04-ci-cd-y-automatizacion.md`](04-ci-cd-y-automatizacion.md)), no como única defensa.
- Si el proyecto maneja datos de más de un cliente/tenant, el aislamiento entre tenants se trata con el mismo rigor que un secreto: un fallo de aislamiento es una fuga de datos, no un bug de UI.

## Testing: foco antes que cobertura total

No todo proyecto necesita 100% de cobertura desde el día uno. Es más valioso tener una suite de tests enfocada en la lógica crítica y decir explícitamente en la documentación "esto tiene foco, no cobertura general" que aparentar una cobertura completa que no existe. La honestidad sobre qué está probado y qué no es parte de la documentación del proyecto, no un detalle de implementación.

Para migraciones entre lenguajes o reescrituras, ver el patrón de "prueba diferencial" en [`02-estructura-de-proyecto-y-documentacion.md`](02-estructura-de-proyecto-y-documentacion.md).
