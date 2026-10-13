# CI/CD y automatización

## CI mínimo viable

El patrón que se repite y funciona: checkout → setup del lenguaje → instalar dependencias → correr tests. Sin matrices complejas de versiones salvo que el proyecto realmente las necesite (una librería pública con múltiples consumidores, por ejemplo). Un workflow de ~20-30 líneas que corre en cada push/PR es preferible a una pipeline elaborada que nadie mantiene.

```yaml
# Esqueleto representativo
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5   # o setup-node, setup-go, etc.
        with:
          python-version: "3.11"
      - run: pip install -r requirements.txt
      - run: pytest -q
```

## Secret scanning en CI, no solo `.gitignore`

`.gitignore` evita que un secreto se añada por primera vez; no detecta que alguien ya lo commiteó. Un job de escaneo de secretos (gitleaks o equivalente) corriendo en cada PR es la segunda línea de defensa. Si hay falsos positivos legítimos (ej. claves de ejemplo en código de terceros), se documentan en una allowlist **con el motivo escrito al lado de cada excepción** — nunca se silencia sin explicación.

## Limpieza automática de ramas

Un workflow que borra ramas ya mergeadas (al cerrarse el PR, o en un cron semanal) evita que el repo acumule docenas de ramas muertas. Es higiene de bajo costo que vale la pena automatizar desde el principio de un proyecto en vez de hacerla manual "cuando se note sucio".

## Despliegue

- Si el despliegue es automático en push a una rama, eso debe estar documentado explícitamente en el README o `CLAUDE.md` — incluyendo qué rama dispara qué entorno.
- Advertencias de "esto falla en silencio" relacionadas al despliegue se numeran explícitamente en la documentación (ver [`03-trabajo-con-ia-claude-code.md`](03-trabajo-con-ia-claude-code.md)) — un deploy que falla sin avisar es peor que uno que nunca se automatizó.

## Releases (para librerías / CLIs)

Para proyectos que se distribuyen como binario o paquete (un CLI, por ejemplo), un workflow de release basado en tags con generación automática de changelog/binarios (ej. goreleaser para Go) en vez de empaquetar manualmente cada versión. El `CHANGELOG.md` se mantiene a mano cuando el volumen de cambios lo permite — no todo necesita generación automática.
