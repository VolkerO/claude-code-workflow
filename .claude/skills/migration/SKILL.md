---
name: migration
description: Generate and review a Doctrine migration after entity/mapping changes on this Symfony project, checking the generated SQL for data loss and unintended changes before applying. Use when entities changed and a schema migration is needed, or when asked to "create/review a migration", "Migration erstellen", "Schema-Diff".
---

# Doctrine-Migration erstellen & reviewen (Beispielprojekt)

Stack: Doctrine ORM 3 / DBAL 4 / `doctrine/doctrine-migrations-bundle`. Migrations liegen unter `migrations/`.

## Ablauf

1. **Diff generieren** (vergleicht Mapping ↔ aktuelles DB-Schema):
   ```bash
   php bin/console doctrine:migrations:diff
   ```
   Erzeugt eine neue `migrations/VersionYYYYMMDDHHMMSS.php`.

2. **Generiertes SQL reviewen — kritisch.** Die neue Migrationsdatei öffnen und `up()`/`down()` prüfen auf:
   - **Datenverlust**: `DROP TABLE`, `DROP COLUMN`, Typ-Verengung, `NOT NULL` auf bestehender Spalte ohne Default → braucht ggf. Datenmigration/Default.
   - **Unbeabsichtigte Änderungen**: Diffs an Tabellen, die gar nicht geändert wurden (oft fehlende/abweichende Mapping-Annotationen oder Plattform-Defaults). Solche „Phantom-Diffs" deuten auf ein Mapping-Problem, nicht auf eine nötige Migration.
   - **`down()` vollständig**: Macht die Reverse-Migration die Änderung sauber rückgängig?
   - Index-/FK-Namen und -Reihenfolge (FKs vor referenzierten Tabellen droppen).

3. **Trockenlauf / Anwenden** (dev):
   ```bash
   php bin/console doctrine:migrations:migrate --dry-run
   php bin/console doctrine:migrations:migrate          # nach Review
   ```

4. **Gegenprobe**: Nach dem Migrieren erneut `doctrine:migrations:diff` — es sollte **keine** neue Migration mehr nötig sein („No changes detected"). Andernfalls fehlt im Mapping etwas.

## Hinweise

- Bei „Phantom-Diffs" zuerst das Entity-Mapping korrigieren statt die Migration zu committen.
- Generierte Migration anschließend wie normaler Code: php-cs-fixer-Hook formatiert sie automatisch.
- Schema **nie** per `doctrine:schema:update --force` in Richtung Produktion ändern — immer über Migrationen.
