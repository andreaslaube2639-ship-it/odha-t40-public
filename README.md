# ODHA-T40

ODHA-T40 ist ein unabhängiges, in Rust entwickeltes Arbeitsartefakt für Sicherheits- und Autorisierungssysteme. Es untersucht eine konkrete Problemklasse: Wie lassen sich Vorschläge aus nichtdeterministischer Verarbeitung prüfen, bevor daraus eine zustandsändernde Wirkung wird?

Der Leitgedanke lautet **CAPABILITY != AUTHORITY**: Eine technische Fähigkeit oder vorliegende Evidenz erteilt für sich genommen keine Ausführungsberechtigung. Ebenso gilt **MODEL_OUTPUT != EVIDENCE**. Der betrachtete Ablauf trennt Vorschlag, Validierung, ausdrücklich begrenzte Autorisierung, Wirkung und anschließende Beobachtung.

## Stand und Grenzen

- Am 30. September 2026 wurde ein interner Quell-, Vertrags- und Teststand als Vergleichsbasis dokumentiert. Spätere Änderungen und Prüfungen sind davon getrennt erfasst.
- Die private Rust-Implementierung enthält Module für einen Authority-Daemon und zugehörige Tests. Diese Aussage beschreibt den geprüften Quellstand, keine produktive Inbetriebnahme.
- **SYSTEM_CLOSED = NO.** Eine vollständige Abdeckung aller Wirkungspfade, ein produktiver Einsatz und eine unabhängige Sicherheitszertifizierung werden nicht behauptet.
- Die Kernimplementierung und internen Evidenzdaten bleiben privat. Dieses Repository ist ausschließlich ein öffentlicher Projekteinstieg; es enthält keinen Quellcode des Kernsystems.

Projektstatus und Meilensteine beruhen auf der internen Projektprüfung vom 2. Oktober 2026 und sind keine unabhängige Bescheinigung.

## Kontakt

Fachliche Fragen, Gegenbeispiele und mögliche Zusammenarbeit: [Andreas Laube auf LinkedIn](https://www.linkedin.com/in/andreas-laube-bb54ba245/).
