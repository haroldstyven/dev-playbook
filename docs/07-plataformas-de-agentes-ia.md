# Plataformas de agentes de IA (Platform-Agent)

Patrones específicos para proyectos cuyo producto es un sistema de agentes — no "IA asistiendo al desarrollo" (eso está en [`03-trabajo-con-ia-claude-code.md`](03-trabajo-con-ia-claude-code.md)), sino aplicaciones que corren agentes/grafos/colas en producción.

## Separar grafos/agentes por responsabilidad, no uno monolítico

Cuando hay más de un flujo de razonamiento claramente distinto (ej. uno que atiende conversación y otro que ejecuta tareas en segundo plano), se modelan como **grafos independientes**, cada uno con su propio punto de entrada — no como ramas condicionales dentro de un único grafo gigante. Un grafo por responsabilidad es más fácil de versionar, probar y depurar por separado.

## Colas separadas por tipo de trabajo

Si el sistema usa un worker de tareas en segundo plano (Celery o equivalente), las colas se separan por tipo de carga (ej. una cola para trabajo ligero/interactivo, otra para trabajo pesado/batch) en vez de una cola compartida. Esto evita que un job lento bloquee la latencia de algo que necesita responder rápido.

## Infraestructura mínima de un agente en producción

El set que se repite, orquestado con Docker Compose en desarrollo:

- **Postgres** — estado durable (conversaciones, resultados, metadata de ejecución).
- **Redis** — broker de colas y/o caché.
- **Almacenamiento de objetos** (MinIO u equivalente S3-compatible) — artefactos generados, archivos subidos.
- **Base de datos vectorial** (Chroma u equivalente) — embeddings para RAG, cuando el agente necesita recuperar contexto propio.
- **Migraciones versionadas** (Alembic o equivalente) con la misma disciplina de idempotencia que cualquier otra migración (ver [`01-git-y-control-de-versiones.md`](01-git-y-control-de-versiones.md)).

No todo proyecto de agentes necesita las cinco piezas desde el día uno — pero si el proyecto va a crecer, vale más elegir esta forma desde el principio que migrar después de que el estado durable y la caché compartan la misma base de datos.

## Observabilidad desde el día uno, no después del primer incidente

El comportamiento de un agente no es determinista de la misma forma que un endpoint CRUD: la misma entrada puede tomar rutas distintas. Eso hace que loggear solo errores no sea suficiente para depurar "por qué decidió hacer esto". Un stack mínimo de logs + métricas (ej. Grafana/Loki/Promtail o equivalente) corriendo desde el principio del proyecto es lo que permite reconstruir una ejecución después del hecho, en vez de pedirle al usuario que reproduzca el bug.

## El proveedor de modelo es una variable de configuración, no una decisión de código

Cuando el proyecto depende de un LLM, la integración se hace detrás de una capa de abstracción intercambiable por variable de entorno (ej. `MODEL_PROVIDER=anthropic|openai|gemini`), no con llamadas directas al SDK de un proveedor esparcidas por el código. Esto no es sobre-ingeniería prematura: es lo que te permite comparar costo/latencia/calidad entre proveedores sin reescribir lógica de negocio, y te protege de quedar atado a un solo proveedor si cambia el pricing o la disponibilidad.

## Plan antes de grafo

Igual que cualquier cambio no trivial (ver [`03-trabajo-con-ia-claude-code.md`](03-trabajo-con-ia-claude-code.md)), el diseño de un flujo de agente nuevo —qué nodos tiene, qué herramientas puede llamar, dónde puede fallar— se documenta en un `plan.md` **antes** de escribir el grafo. Un grafo de agente es más difícil de leer que código imperativo equivalente; el plan es lo que le da trazabilidad a por qué quedó con esa forma.

## Documentar los modos de fallo propios de agentes

Además de los fallos silenciosos genéricos de cualquier proyecto (ver [`03-trabajo-con-ia-claude-code.md`](03-trabajo-con-ia-claude-code.md)), un sistema de agentes tiene su propia categoría de fallo que vale la pena numerar explícitamente en la documentación:

- Una herramienta que falla y el agente continúa como si hubiera tenido éxito.
- Un bucle de reintento silencioso que dispara múltiples llamadas al modelo sin que nadie lo note (impacto directo en costo).
- Contexto que se trunca o se desborda sin error visible, produciendo una respuesta "razonable" pero basada en información incompleta.

## Testing: foco en las herramientas y los bordes del grafo, no en "probar el modelo"

No se prueba que el LLM responda bien — eso no es determinista y no es responsabilidad del repo. Se prueban las partes que sí son deterministas: que cada herramienta/nodo haga lo que dice que hace, que los errores de una herramienta se propaguen (no se traguen), y que el enrutamiento entre nodos tome la rama correcta dado un estado de entrada conocido. Igual que en el resto del playbook, es preferible admitir explícitamente "esto tiene foco en X, no cobertura general" que aparentar una suite completa (ver [`05-calidad-seguridad-y-dependencias.md`](05-calidad-seguridad-y-dependencias.md)).
