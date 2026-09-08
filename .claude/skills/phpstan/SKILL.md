---
name: phpstan
description: Run PHPStan static analysis on this Symfony/Doctrine project (level 8 + strict-rules, with symfony + doctrine extensions) and report/triage findings. Use after PHP changes, before committing, or when asked to "check types", "run phpstan", "statische Analyse".
---

# PHPStan-Check (Beispielprojekt)

Statische Analyse für dieses Symfony 8.1 / Doctrine ORM 3 Projekt. Effektive Config (per Auto-Discovery genutzt): `phpstan.neon` (Level 8, `phpstan-strict-rules`, Extensions `phpstan-symfony` + `phpstan-doctrine`, Baseline `phpstan-baseline.neon`). Hinweis: `phpstan.dist.neon` (Level 6, ohne Baseline) existiert ebenfalls, wird aber von der Auto-Discovery **nicht** bevorzugt — `vendor/bin/phpstan analyse` lädt `phpstan.neon`.

## Ablauf

1. **Container-Cache sicherstellen.** Die Symfony-Extension braucht `var/cache/dev/App_KernelDevDebugContainer.xml`. Falls die Analyse mit „container XML … not found" abbricht, zuerst:
   ```bash
   php bin/console cache:warmup --env=dev
   ```

2. **Analyse laufen lassen** (volles Projekt):
   ```bash
   vendor/bin/phpstan analyse --no-progress
   ```
   Nur geänderte Dateien (schneller, für gezielte Checks):
   ```bash
   vendor/bin/phpstan analyse --no-progress <pfad/zur/datei.php> ...
   ```

3. **Findings triagieren.** Echte Fehler beheben. Die Baseline (`phpstan-baseline.neon`) enthält bekannte Altlasten — neue Fehler **nicht** einfach in die Baseline schieben, sondern beheben. Baseline nur bewusst und auf ausdrückliche Anweisung neu generieren:
   ```bash
   vendor/bin/phpstan analyse --generate-baseline
   ```

4. **Bericht.** Kurz zusammenfassen: Anzahl Fehler, betroffene Dateien, was behoben wurde, was offen bleibt (mit Begründung).

## Hinweise

- `treatPhpDocTypesAsCertain: false` ist gesetzt — PHPDoc-Typen gelten nicht als garantiert.
- Doctrine-Repositorys/Entities werden über die Doctrine-Extension typisiert (`objectManagerLoader: tests/object-manager.php`).
- Code-Style ist **nicht** Aufgabe von PHPStan — das erledigt der php-cs-fixer-PostToolUse-Hook automatisch.
- Beim Level-8-Sprung eingefrorene Altlasten (v. a. Null-Safety) sind in einer eigenen Punch-Liste dokumentiert.
