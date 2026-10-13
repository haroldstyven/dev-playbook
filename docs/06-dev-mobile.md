# Desarrollo mobile (iOS / Android)

## El archivo de proyecto no se versiona, su generador sí

En iOS, el `.xcodeproj` es un XML gigante propenso a conflictos de merge y a ensuciarse con configuración específica de una máquina. La fuente de verdad es `project.yml` (generado con **XcodeGen**), y el `.xcodeproj` resultante va en `.gitignore`. Cualquiera que clone el repo corre `xcodegen generate` (o el script equivalente) antes de abrir el proyecto — eso se documenta en el README como primer paso del quickstart, no como nota al margen.

En Android el equivalente es que `build.gradle.kts` **sí** es la fuente de verdad y se versiona directamente — Gradle no sufre el mismo problema de archivos binarios/XML frágiles que Xcode, así que no hace falta una capa de generación encima.

## Stack por defecto

- **iOS**: SwiftUI + SwiftData para proyectos nuevos, salvo que haya una razón concreta para UIKit o Core Data (interoperar con código legado, necesitar algo que SwiftData todavía no soporta bien). No se arranca un proyecto nuevo en UIKit "por si acaso".
- **Android**: Kotlin + Gradle KTS.
- Targets adicionales (widgets, extensiones) viven como target separado dentro del mismo proyecto, no como repo aparte, para que compartan modelos y lógica de dominio sin duplicar código.

## Secretos y configuración local

- Android: `secrets.properties` (valores reales, gitignored) junto a `secrets.defaults.properties` (plantilla con claves vacías, sí versionada) — mismo patrón que `.env`/`.env.example` del resto de proyectos, aplicado a Gradle.
- iOS: el equivalente son archivos `.xcconfig` con claves/endpoints — el real gitignored, uno `.example` versionado.
- **Firma y provisioning son la fricción más común al clonar en una máquina nueva.** El Team ID de firma y los UDIDs de los dispositivos de prueba registrados se documentan en un lugar no versionado (nota local, gestor de secretos) — nunca hardcodeados en el repo, pero tampoco solo "en la cabeza de quien configuró el proyecto la primera vez". Si un build falla por firma en una máquina nueva, la causa casi siempre es esa información faltante, no un bug de código.

## Estado actual honesto sobre calidad de código mobile

Ningún proyecto iOS de este playbook usa SwiftLint todavía — XcodeGen cumple el rol de herramienta de control del proyecto (estructura, targets, configuración), no de estilo de código. Si vas a añadir lint a un proyecto iOS nuevo, es una decisión consciente de agregar, no algo que ya viene resuelto por copiar la estructura de otro repo de este grupo.

## README también aplica aquí

La sección "Pendientes conocidos" (ver [`02-estructura-de-proyecto-y-documentacion.md`](02-estructura-de-proyecto-y-documentacion.md)) es especialmente importante en proyectos mobile: features a medio implementar, pantallas sin conectar a datos reales, o soporte de dispositivo/OS incompleto son fáciles de olvidar documentar porque "se ven" en el simulador aunque no estén terminadas.

## Checklist adicional para PRs mobile

- [ ] `project.yml` (iOS) o `build.gradle.kts` (Android) refleja cualquier target/dependencia nueva — no se edita el `.xcodeproj` a mano.
- [ ] No se commiteó ningún archivo de firma, perfil de provisioning, o `secrets.properties`/`.xcconfig` real.
- [ ] Si el cambio agrega una dependencia nueva, se evaluó el impacto en tamaño de binario antes de mergear.
