# Checklist: ¿está listo para mergear?

Antes de abrir o aprobar un PR:

- [ ] El mensaje de commit describe el efecto observable del cambio, sigue la convención del repo, y no lleva trailer de co-autoría de IA.
- [ ] Si se agregó o cambió una variable de entorno, `.env.example` quedó actualizado.
- [ ] Si se tocó una migración de base de datos, es idempotente (`IF NOT EXISTS`, `ON CONFLICT DO NOTHING` o equivalente).
- [ ] Si el proyecto es multi-tenant/multi-cliente, se verificó que el cambio respeta el aislamiento entre tenants.
- [ ] No hay secretos, tokens o credenciales en el diff (ni en código ni en archivos de configuración de ejemplo).
- [ ] CI pasa: tests, lint, y escaneo de secretos si el repo los tiene configurados.
- [ ] Si el cambio agrega un comportamiento no obvio o un fallo silencioso conocido, quedó documentado en `CLAUDE.md`/`AGENTS.md` o el README — no solo explicado en la descripción del PR.
- [ ] Si se actualizó una dependencia, va en un commit/PR separado de los cambios funcionales.
- [ ] La rama tiene nombre con prefijo semántico (`feat/`, `fix/`, `chore/`, `docs/`, `refactor/`).
- [ ] `main` (o la rama principal) sigue siendo desplegable después del merge — si algo se rompe, el plan es revertir, no parchear encima.
