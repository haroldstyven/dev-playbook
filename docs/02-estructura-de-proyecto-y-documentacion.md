# Estructura de proyecto y documentación

## Estructura del README

Patrón consistente, con profundidad proporcional a la madurez del proyecto:

```markdown
# Nombre del proyecto

## Arquitectura / Stack
## Requisitos
## Arrancar / Quickstart
## Pendientes conocidos
```

Dos detalles que se repiten y valen más que la plantilla en sí:

- **"Pendientes conocidos" es una sección real, no decorativa.** Un README que admite qué falta o qué es deuda técnica es más confiable que uno que aparenta estar terminado. Si el proyecto tiene limitaciones conocidas, van ahí — no se descubren leyendo el código tres semanas después.
- **Audiencia dual cuando el proyecto la tiene.** Si el repo lo va a leer gente no técnica (un socio, un cliente) además de desarrolladores, el README se separa explícitamente: una sección en lenguaje llano ("la historia", "qué hace esto") y otra técnica ("para devs"). No se mezcla registro dentro del mismo párrafo.
- Proyectos con contratos de API o decisiones de arquitectura no triviales los documentan **directamente en el README**, no solo en un doc separado que nadie abre.

## `.env.example`

Todo proyecto con configuración sensible (API keys, credenciales de BD, tokens) lleva un `.env.example` actualizado con cada variable que el código realmente lee — sin valores reales, con un comentario corto de qué es cada una si el nombre no es autoexplicativo. Mantenerlo actualizado es parte de la definición de "terminado" de cualquier cambio que agregue una variable de entorno nueva.

## Separación entre PoC y versión definitiva

Cuando un proyecto empieza como prueba de concepto y luego se reescribe en serio (otro lenguaje, otra arquitectura), **no se borra la PoC** — se congela como referencia y el repo definitivo documenta explícitamente cuál de los dos manda. Esto evita la ambigüedad de "¿cuál es la versión real?" cuando alguien (o un agente) encuentra ambos repos.

## Reutilización de estructura entre proyectos hermanos

Si vas a tener varios microservicios con la misma forma (ej. varias APIs de ingesta/scraping con distinto origen de datos), usa la **misma estructura de carpetas** (`models/`, `routes/`, `services/`, o la que corresponda a tu stack) en todos. El costo de mantenerlos es menor cuando cualquiera de ellos se navega igual que los demás.

## Arquitectura hexagonal / dependencia explícita (cuando aplica)

Para migraciones entre lenguajes o reescrituras críticas, vale la pena hacer explícita la regla de dependencia (el dominio no hace I/O, no importa adaptadores externos) y dejarla verificable — no solo como diagrama, sino como regla que un lint o un test puede comprobar.

### Patrón de "prueba diferencial" para migraciones

Al portar una implementación de un lenguaje/stack a otro, generar casos de prueba dorados a partir de la implementación de referencia y comparar automáticamente contra la nueva implementación. Cuando los resultados difieren, la pregunta correcta es **cuál de las dos tiene razón**, no asumir que la referencia es la fuente de verdad por defecto — a veces el bug estaba en la original.
