# Trabajo con IA / Claude Code

Esta es la parte que más cambia con el tiempo, pero los patrones que han sobrevivido son estos.

## Qué va en un `CLAUDE.md` (o `AGENTS.md`)

Un buen `CLAUDE.md` no describe arquitectura que ya es legible en el código — describe lo que **no es obvio leyendo los archivos**:

1. **Mapa del repo al principio**, en bloque de código, antes de cualquier instrucción — para que el agente no tenga que explorar todo el árbol de archivos solo para orientarse.
2. **Comandos de arranque copiables y probados**, no descripciones en prosa ("corre `docker-compose up --build -d`", no "levanta los contenedores de Docker").
3. **Fallos silenciosos conocidos, numerados, con el caso concreto que los motivó.** Ejemplo real de este tipo de regla: *"`Number(v).toFixed(1)` falla en 20 de 50 casos de frontera — usar la función X en su lugar"*. Una regla con el caso de fallo adjunto se recuerda y se aplica; una regla sin motivo se ignora la primera vez que estorba.
4. **Reglas "no negociables" numeradas**, cada una con el porqué, no solo el qué.
5. **Convenciones de idioma, nombres y estilo** si el proyecto las tiene, explícitas (ej. "todo en español: módulos, funciones, tests y comentarios").
6. **Qué repo es la autoridad**, si hay más de uno relacionado (ver PoC vs definitivo en [`02-estructura-de-proyecto-y-documentacion.md`](02-estructura-de-proyecto-y-documentacion.md)).
7. **Prohibiciones explícitas** que de otro modo un agente asumiría por defecto (ej. "los commits van sin trailer de co-autoría").

Si una instrucción se repite en el prompt de cada sesión, esa es la señal de que debería estar en `CLAUDE.md` en vez de repetirse.

## Skills como dependencias versionadas, no como prompts sueltos

Cuando una instrucción o flujo de trabajo se usa en más de un proyecto, conviene empaquetarla como **skill** en vez de copiar y pegar el mismo texto de instrucciones repo a repo. Si la skill viene de un repo externo, se puede fijar por hash/versión en un lock file — igual que cualquier otra dependencia de código — para que una actualización de la skill no cambie el comportamiento del agente sin que alguien lo note.

Si una skill custom documenta un flujo propio (ej. una metodología de diseño con fases: pensar → dirección visual → implementar → checklist de accesibilidad → formato de salida), que esa estructura de fases quede explícita en el `SKILL.md` — no implícita en cómo "sueles pedirlo".

## Planificar antes de implementar

Para cambios no triviales, un documento de plan (`plan.md` o equivalente) separado de la implementación, escrito **antes** de tocar código. Esto aplica igual si quien planifica es un agente: la planificación y la ejecución son pasos distintos, y el plan sobrevive como documentación de la decisión aunque el código cambie después.

## Permisos y modo automático

Si usas un modo de ejecución automática/autónoma para el agente, documenta explícitamente (no confíes en que el agente lo infiera):

- Qué repos/ramas son de confianza vs. cuáles requieren confirmación.
- Qué se considera "producción" o "sensible" (nombres de host, namespaces, convenciones de nombre como `prod-*`).
- Dónde viven los secretos y quién debería tener acceso a ellos.
- Qué operaciones necesitan confirmación humana explícita incluso en modo automático (deploys, cambios de esquema, force-push).

Esto no es specific a Claude Code — es la misma disciplina que aplicarías dándole permisos amplios a cualquier colaborador nuevo, humano o no.

## Revisión de código generado por IA

El mismo estándar de revisión que para código humano: ¿rompe el aislamiento multi-tenant?, ¿la migración es idempotente?, ¿hay secretos filtrados? — nada de esto se relaja porque el autor del diff sea un agente.
