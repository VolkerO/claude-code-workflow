# Agentische Entwicklung mit Leitplanken

Konfiguration, mit der ich Claude Code in produktiven PHP-/Symfony-Projekten einsetze —
projektspezifische Agenten, gekapseltes Projektwissen als Skills und ein Hook, der
Formatierung erzwingt, statt sie zu erbitten.

**Das Prinzip dahinter in einem Satz:** Generierter Code wird geprüft und verantwortet
wie eigener.

## Warum das hier liegt

Ein Agent, der die Konventionen eines Projekts nicht kennt, produziert Code, der
funktioniert und trotzdem nicht dazugehört. Ein Agent ohne Grenzen produziert
irgendwann Schaden. Beides lässt sich konfigurieren — und genau das ist die Arbeit,
die zwischen „ich lasse mir Code generieren" und „ich setze KI produktiv ein" liegt.

## Was hier liegt

| Datei | Zweck |
|---|---|
| `.claude/agents/symfony-expert.md` | Symfony 8.1 / Doctrine ORM 3 — Stack als Vorgabe, Repository-Wiederverwendung vor Neuschreiben, Security über Voters statt ad hoc |
| `.claude/agents/phpunit-tester.md` | PHPUnit 12 — Unit gegen Functional abgrenzen, Transaktions-Rollback je Test, Entities über fachliche Merkmale statt IDs suchen |
| `.claude/skills/migration/SKILL.md` | Doctrine-Migration erzeugen **und reviewen** — SQL auf Datenverlust und Phantom-Diffs prüfen, bevor es angewandt wird |
| `.claude/skills/phpstan/SKILL.md` | Statische Analyse, Level 8 mit strict-rules — Findings beheben, nicht in die Baseline schieben |
| `.claude/settings.json` | `PostToolUse`-Hook: php-cs-fixer läuft nach jeder Schreiboperation auf die geänderte Datei, sofern sie im Projekt liegt und eine Konfiguration existiert |

## Die interessanten Stellen

Jede Agentendatei endet mit einem Abschnitt **„Niemals"**. Der ist wichtiger als alles
darüber:

- Kein `git commit`, kein `git push` — committet wird von Hand, nach Sichtung.
- Schemaänderungen ausschließlich über Migrationen, nie über `doctrine:schema:update`.
- Tests werden nicht an eine fehlerhafte Implementierung angepasst, damit sie grün
  werden — stattdessen wird der Bug gemeldet.
- Ergebnisse werden ehrlich berichtet: Schlägt etwas fehl, steht der Output da.

Der letzte Punkt ist der, an dem sich entscheidet, ob man den Ergebnissen trauen kann.

Ergänzt wird das durch Sperren in der Nutzerkonfiguration (`autoMode.soft_deny`), die
riskante Operationen blocken und dabei auf die dokumentierte Projektkonvention verweisen,
statt nur „verboten" zu melden. Die stehen hier nicht, weil sie Hostnamen und Pfade des
jeweiligen Projekts enthalten.

## Herkunft

Diese Dateien sind die **bereinigten Fassungen** einer Konfiguration, die in einem
produktiven Symfony-8.1-Projekt im Einsatz ist. Projektspezifische Bezeichner,
Klassennamen, Rollen, Hostnamen und interne Pfade sind durch generische Platzhalter
ersetzt. Die Struktur, die Regeln und die Qualitätskette sind unverändert.

Der Stack, auf den sie sich beziehen: PHP 8.4, Symfony 8.1, Doctrine ORM 3 / DBAL 4,
Twig 3, AssetMapper ohne Node-Buildkette, PHPUnit 12, PHPStan, Psalm, php-cs-fixer,
GrumPHP.

## Verwendung

Verzeichnis `.claude/` in ein Projekt kopieren und an dessen Konventionen anpassen —
die Dateien sind bewusst als Vorlage geschrieben, nicht als fertige Lösung. Der Wert
liegt nicht in diesen Zeilen, sondern darin, die eigenen Konventionen einmal
aufzuschreiben, statt sie in jedem Prompt zu wiederholen.

---

Volker Orgeldinger · orgeldinger.volker@gmail.com
