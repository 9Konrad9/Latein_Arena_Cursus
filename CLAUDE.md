# Lūdī Rōmānī / Latein-Arena (Cursus) – Projektkontext für Claude Code

Latein-Lernplattform zum Lehrbuch *Cursus* (Neue Ausgabe), Gymnasium Baden-Württemberg.
Browserbasiert, kein Server/Backend, kein Login – reines HTML/CSS/JS, Fortschritt liegt lokal im
`localStorage` des jeweiligen Browsers.

> **Status: Umbau bis Lektion 6.** Dieser Ordner ist aus der Pontes-Arena (Stand `23a03c1`)
> hervorgegangen. Umgestellt sind der Wortschatz (Cursus L1–6), sämtliche Lektionszahlen in den
> Engines und die Lektionsangaben in den Spieltexten. **Nicht** umgestellt sind das Kultur-Quiz
> und die handkuratierten Sätze in `Pronomina.html` – beide tragen noch Pontes-Inhalte und sind
> im Spiel entsprechend gekennzeichnet. Stand und offene Punkte: `UMBAU-CURSUS.md`.

## Grundprinzip

Alle Spiele arbeiten mit echtem, lektionsgefiltertem Wortschatz aus `vocabulary.js` (derzeit 175
Einträge, Lektion 1–6 nach den Wortschatzseiten des Cursus).
Schüler:innen wählen vor jeder Runde, bis zu welcher Lektion geübt werden soll (`lessonFilter.js`).
Formen werden **live generiert**, nicht aus festen Beispielsätzen – deshalb wiederholen sich Sätze
praktisch nie, und der Schwierigkeitsgrad wächst automatisch mit der Lektionsauswahl mit.

## Zentrale Dateien (von (fast) jedem Spiel eingebunden)

- `vocabulary.js` – die Wortschatz-Datenbank. Jeder Eintrag: `{ latin, lesson, type, middle,
  gram_clue, gram_class, meaning, ... }`. Bei Verben zusätzlich `perfect`, `perfectLesson`, `ppp`,
  `pppLesson` (nur gesetzt, wenn die Form SPÄTER gelehrt wird als das Wort selbst).
  Für den Satzbau tragen Verben außerdem `valenz` (`"akk" | "dat" | "dat+akk" | "intrans" |
  `"abl"` | `"gen"`, oder ein Array, wenn beide Verwendungen gelten) und `subjBelebt`
  (verlangt das Verb ein Lebewesen als Subjekt?); Nomen tragen `belebt`
  (`"person" | "tier" | "sache"`). Mehrteilige Lemmata haben `satzbau: false`.
  Diese vier Felder sind fachlich gegengelesen – NICHT im Vorbeigehen ändern.
- `nounEngine.js` – zentrale Deklinations-Engine für Substantive. API: `NounEngine.decline(nounObj)`
  gibt `{ sg: {nom,gen,dat,akk,abl}, pl: {...}, gender }` zurück (Werte können `null` sein bei
  defektiven Nomen wie *vīs*!). `NounEngine.getForm(nounObj, caseKey, numerus)` für Einzelformen.
- `verbEngine.js` – zentrale Konjugations-Engine. Deckt ab: Aktiv/Passiv (5 Tempora), Imperativ,
  Infinitive (4 Kombinationen), PPA, Konjunktiv (4 Tempora × 2 Genera), Gerundium/Gerundivum.
  Kernfunktion: `VerbEngine.getFormsForTempus(verbObj, tempus, genus)` → Array mit 6 Personalformen.
  Kennt alle unregelmäßigen Verbfamilien, Deponentien, *fierī*, sowie die Sonderfälle *dare*
  (kurze Vokale) und die 3 lexikalisierten Imperativ-Kurzformen (*dūc, dīc, fac*).
- `progress.js` – Leitner-System (`LudiProgress.recordVocabAttempt`) und Achievement-Logik
  (`LudiProgress.recordCategoryAttempt`, `getAchievementOverview`). Speichert in `localStorage`.
- `achievementToast.js` – Popup-Benachrichtigung bei neu freigeschalteten Achievements.
- `lessonFilter.js` – die Lektionsauswahl-Overlay-Komponente, von praktisch jedem Spiel genutzt.

## Aktive Spiele (Stand zuletzt)

- **CircusV.html** – reine Bedeutungsabfrage (Circus Maximus)
- **Duell.html** – Wortschatz-Duell, mehrere Fragetypen
- **Principia.html** – Grundformen/Stammformen progressiv
- **IssaJump.html** – Jump-and-Run, Bedeutungsabfrage
- **Kastell.html** – Formen-Kastell: Substantiv- UND Verbformen, nutzt die komplette VerbEngine
  (Aktiv/Passiv, Imperativ, Infinitive, PPA, Konjunktiv, Gerundium/Gerundivum), gewichtet nach
  Häufigkeit. Bild-Assets in `assets/kastell/`.
- **Villa.html** (Aedificium Rōmānum) – KNG-Kongruenz. Drei Adjektiv-Modi (regulär -us/-a/-um,
  i-Deklination, gemischt). Sechs Bild-Stufen für den Baufortschritt in `assets/villa/`.
- **ViaRomana.html** – Satzglieder bestimmen: Subjekt, Prädikat, Akk-/Dativobjekt, Genitiv-Attribut,
  Ablativ-Handlungsträger, adverbiale Bestimmungen (Zeit/Ort/Herkunft/Mittel/Begleitung ab L7).
  Volle Tempus-/Genus-Vielfalt.
- **Pendel.html** – klassische Pendelmethode (Subjekt → Prädikat → Objekt schrittweise), generiert
  aus echtem Wortschatz, mit Erklärung statt Übersetzungsauswahl am Ende.
- **Arcus.html** – AcI (ab L8). Zwei Schritte: Subjektsakkusativ und Infinitiv im Satz anklicken,
  ab L10 zusätzlich das Zeitverhältnis (gleichzeitig/vorzeitig). Bildmetapher ist der römische
  Bogen: zwei Pfeiler und ein Schlussstein. Nutzt `SentenceEngine.buildAcI()`.
- **Pronomina.html** – vier Modi: Possessiv (handkuratierte Sätze), Personal, Demonstrativ
  (hic/ille/is, KNG-Kongruenz-generiert), Relativ (quī/quae/quod – Kasus kommt von der Funktion
  im Nebensatz, NICHT vom Bezugswort!).
- **Quiz.html** – statisches Kultur-Quiz (`quizData.js`), nicht wortschatz-basiert.
  **Noch Pontes-Fragen**; zwei Cursus-Kulturthemen (Das Römische Reich L3, Reisen zur See L6)
  haben bisher gar keine Fragen.
- **Achievements.html** – Trophäensammlung-Übersichtsseite.
- **index.html** – Hauptmenü.

Gelöscht/nicht mehr vorhanden (bewusst entfernt, falls in altem Stand noch auftauchend):
Bollwerk.html, Kastell_backup.html, Adventura.html, Possessiv.html, Tabularium.html,
LektionsCheck.html, Genitiv.html (Compone!) – alle redundant zu neueren/reichhaltigeren Spielen.

## Wichtige Konventionen & bekannte Fallstricke

- **Makronfehler kommen vor**: In der Vokabeldatenbank fanden sich mehrfach fehlende Längen
  (*instruere*→*īnstruere*, *multi*→*multī*, *amicus*→*amīcus*). Bei Unsicherheit lieber
  nachschlagen/prüfen als blind übernehmen.
- **`gram_clue`-Format für Nomen-Ausnahmen**: i-Stamm-Gen.-Pl.-Ausnahmen stehen als
  `"(Gen. Pl. XXX)"` im `gram_clue`-Text und werden von `NounEngine` automatisch geparst – NICHT
  hartkodieren, sondern im Datensatz ergänzen (z.B. bei *mors*, *bōs* nachträglich gefunden).
- **Defektive Nomen** (*vīs* u.a.) liefern `null` für fehlende Kasus – IMMER prüfen, bevor eine
  Form als Distraktor/Zielwort verwendet wird, sonst tauchen `null`/`undefined` im UI auf.
- **Testverfahren**: Jede Änderung wird vor Auslieferung mit jsdom getestet (Syntax-Check per
  `node --check`, dann funktionaler Test durch Laden der HTML-Datei mit `runScripts: 'dangerously'`).
  localStorage funktioniert unter `file://`-URLs in jsdom nicht zuverlässig – bei Bedarf mit
  In-Memory-Ersatz oder `http://`-Basis-URL umgehen.
  Testumgebung liegt in `C:\Users\konra\latein-tests` (Node + jsdom), bewusst AUSSERHALB des
  OneDrive-Ordners – `node_modules` gehört nicht in die Synchronisierung und nicht ins Repo.
  Wiederverwendbare Testbasis dort: `harness.js` (`loadScripts([...])` → `{ ev, errors }`).
  **Der Harness zeigt per Standard auf die Pontes-Arena.** Für diese Fassung die Umgebungsvariable
  setzen, sonst testet man das falsche Projekt:
  `LATEIN_PROJ=".../Latein_Plattform_Cursus" node test-gating-cursus.js`
  Zwei Fallstricke, die dabei jedes Mal zuschlagen:
  - **`window.eval(quelltext)` funktioniert NICHT** zum Laden der Projektdateien. Deren Globals
    (`globalVocabularyPool`, `NounEngine`, `VerbEngine` ...) sind top-level `const` – solche
    Bindings landen weder auf `window`, noch überleben sie den eval-Scope. Die Dateien müssen als
    echte `<script>`-Tags in die Seite; der Zugriff läuft danach über `window.eval('name')`.
  - **Beim Inlinen die Sequenz `</script` escapen.** `nounEngine.js`, `verbEngine.js`, `progress.js`
    und `achievementToast.js` enthalten sie im Einbinde-Hinweis-Kommentar. Im Browser harmlos (die
    Dateien werden per `src` geladen), beim Inlinen bricht der HTML-Parser dort das Script-Tag ab –
    die Datei lädt dann still nur zur Hälfte.
  - **jsdom kennt `innerText` nicht.** Die Spiele setzen Texte fast überall per `.innerText`; in
    jsdom legt das nur eine gewöhnliche JS-Eigenschaft an, statt den DOM-Text zu ändern.
    `.textContent` liefert deshalb weiter den HTML-Platzhalter – ein Test, der `textContent` prüft,
    prüft nichts. Stattdessen die Eigenschaft selbst lesen (`el.innerText`).
- **Bild-Assets**: Cartoon-Stil, "children's book style, simple clean lines", auf Magenta-Hintergrund
  generiert und dann per HSV-basiertem Colorkey freigestellt, skaliert und als JPEG (Hintergründe)
  oder PNG (transparente Elemente) gespeichert.
  Das Freistellen lief früher über Python/PIL – Python ist auf dieser Maschine nicht mehr
  installiert. Ersatz ist `latein-tests/freistellen.js` (sharp):
  `node freistellen.js quelle.jpg ziel.png --breite 900 --tolerance 30`, mit `--info` nur messen.
  Es leitet den Hintergrundfarbton aus den vier Bildecken ab, statt #FF00FF anzunehmen – die
  Generatoren liefern oft ein abweichendes Magenta (gemessen 322° und 333°). Ausgabe ist
  palettiertes PNG mit 128 Farben; bei diesen flachen Flächen spart das rund 85 % ohne
  sichtbaren Verlust.
- **UI-Assets in `assets/ui/`** werden per `border-image` eingebunden, nicht per
  `background-size` – sonst verzerren Rahmen und Griffe beim Dehnen. **Die Schnittwerte in
  `border-image-slice` sind Bildpixel und skalieren NICHT mit**: Wird ein Asset ausgetauscht oder
  neu skaliert, müssen sie neu gemessen werden (inneres Feld suchen, Randbreiten ablesen).
  Vor dem Einbau den **Kontrast des inneren Feldes** gegen die Schriftfarbe messen. Gemessen:

  | Asset | inneres Feld | bester Kontrast | Verwendung |
  |---|---|---|---|
  | `pergament.png` + `rolle-links/rechts.png` | `#f6e8cb` | 11,3 mit `#3b2a18` | Satzfeld (Arcus) |
  | `tabula.png` | `#66543b` | 5,85 mit Creme | frei – gut für Überschriften |
  | `plaque.png` | `#aa754a` | 3,66 mit Dunkel | Knopf, Ruhezustand |
  | `plaque-richtig.png` | `#a6904f` | 4,60 | Knopf, richtig |
  | `plaque-falsch.png` | `#a39375` | 4,78 | Knopf, falsch |

  Die Plaques liegen in einem Mittelton, auf dem weder helle noch dunkle Schrift wirklich gut
  steht – für kurze fette Knopfbeschriftung reicht es, für Fließtext nicht. Das Pergament ist
  der klar beste Lesegrund.
  Die Schriftrolle ist dreiteilig: Das Pergamentband dehnt sich per `border-image`, die beiden
  Walzen sitzen als `::before`/`::after` an den Enden. Der Rand darf schmaler sein als der
  Schnitt – er muss nur die wellige Kante tragen, das schafft Platz für den Satz.
- **Lektions-Gating**: Jede Engine-Fähigkeit hat eine feste Einführungs-Lektion. Die
  verbindlichen Nummern stehen im Abschnitt „Lehrgang: was wann drankommt“ weiter unten –
  dort nachschlagen, nicht schätzen. Beim Ergänzen einer neuen Fähigkeit gehört die Nummer
  als benannte Konstante in die Engine und in `test-gating.js`.

## Lehrgang: was wann drankommt

Aus dem Inhaltsverzeichnis von *Cursus* (Neue Ausgabe) übernommen – **die verbindliche Quelle für
jedes Lektions-Gating**. Nicht aus dem Gedächtnis ergänzen, sondern hier nachschlagen.

Cursus hat 36 Lektionen, gruppiert in neun Blöcke mit je einer "Insel" als Wiederholung. Die
Lektionen 31–36 sind im Buch mit ★ markiert (Übergang zur Lektüre).

| L | Satzlehre | Formenlehre |
|---|---|---|
| 1 | Satzglieder: Subjekt und Prädikat; Darstellung im Satzmodell | **Infinitiv Präsens**, 3. P. Sg. Präsens, Nom. Sg., Genus des Substantivs |
| 2 | Kongruenz von Prädikat und Subjekt; Satzarten beim Hauptsatz | Konjugationsklassen, 3. P. Pl. Präsens, **Nom. Pl.**, Vokativ |
| 3 | **Akkusativ-Objekt**; Adverbiale | 1./2. P. Sg.+Pl. Präsens, Schema ā-/ē-/i-Konj. und *esse*, Deklinationsklassen, **Akkusativ** |
| 4 | **Genitiv-Attribut** | konsonantische Deklination, **Genitiv** |
| 5 | **Dativ-Objekt** | konsonantische Konjugation, **Dativ** |
| 6 | Adjektiv-Attribut; Prädikatsnomen | Modus einer Verbform, **Imperativ**, Adjektive der ā-/o-Deklination |
| 7 | Adverbiale: **Ablativ in präpositionaler Verbindung** | **Ablativ** |
| 8 | Adverbiale: **Ablativ ohne Präposition**; Ablativfunktionen im Überblick | |
| 9 | **AcI** | Endung -(e)r bei Substantiven und Adjektiven der o-Deklination |
| 10 | Verwendung des Imperfekts; Satzreihe, Satzgefüge | **Imperfekt** |
| 11 | Verwendung des Perfekts | **Perfekt** (-v-, -u-, -s-), Personzeichen des Perfekts |
| 12 | | Perfekt mit Reduplikation/Dehnung/ohne Veränderung, Neutra kons. Dekl. |
| 13 | Dativ des Besitzers | Personalpronomen 1.+2. Person, **Plusquamperfekt** |
| 14 | **AcI: Zeitverhältnis** (gleichzeitig/vorzeitig); Zeitangaben | **Infinitiv Perfekt** |
| 15 | | Adjektive der konsonantischen Deklination, Possessivpronomen, reflexives Personalpronomen |
| 16 | Verwendung des reflexiven Personalpronomens | *is, ea, id* |
| 17 | Relativsatz; Demonstrativpronomina | *quī, quae, quod*; *hic* – *ille* |
| 18 | Verwendung Perfekt/Plusquamperfekt Passiv | **Genus verbi**, **PPP**, Perfekt + Plusquamperfekt Passiv |
| 19 | **PPP als Participium coniunctum**, als Attribut, als Adverbiale | |
| 20 | Verwendung der Adverbien | **Passiv im Präsensstamm** (Präsens/Imperfekt), Adverbbildung |
| 21 | **PPA als Participium coniunctum**, als Attribut, als Adverbiale | **PPA** |
| 22 | Konj. Imperfekt in Gliedsätzen mit *ut* und *nē* | **Konjunktiv**, Konj. Imperfekt Aktiv/Passiv |
| 23 | Konj. Plusquamperfekt in Gliedsätzen mit *cum*; Zeitverhältnisse | **Konj. Plusquamperfekt** Aktiv/Passiv |
| 24 | | Interrogativpronomen *quis? quid?*, *quī, quae, quod*; Komposita von *esse* |
| 25 | Verwendung des Futurs | **Futur** Aktiv/Passiv |
| 26 | Konj. Präsens und Perfekt mit *ut*, *nē*, *cum*; indirekte Fragesätze | **Konj. Präsens** Aktiv/Passiv, **Konj. Perfekt** Aktiv/Passiv |
| 27 | Verwendung des Komparativs und Superlativs | e-Deklination, **Steigerung** von Adjektiven und Adverbien |
| 28 | **Ablativus absolutus mit PPP** (Vorzeitigkeit) | *īre* und Komposita |
| 29 | **Ablativus absolutus mit PPA** (Gleichzeitigkeit) | u-Deklination |
| 30 | Konditionale Satzgefüge: Realis, Irrealis | |
| 31 ★ | Verwendung des Gerundiums und des Gerundivums (Gerundivum-V) | **nd-Form I: Gerundium**, **nd-Form II: Gerundivum** |
| 32 ★ | Verwendung des Gerundivums als Gerundivum-N | |
| 33 ★ | | *velle – nōlle – mālle* |
| 34 ★ | | **Deponentien** |
| 35 ★ | **NcI** | *ferre* und Komposita, Semi-Deponentien |
| 36 ★ | Konjunktiv in Hauptsätzen | *fierī* |

Wo diese Zahlen im Code stehen: `VerbEngine.TEMPUS_LESSON_AKTIV/_PASSIV`, `INFINITIV_LESSON`,
`IMPERATIV_LESSON`, `PPA_LESSON`, `KONJUNKTIV_LESSON`, `GERUNDIUM_LESSON`, `GERUNDIVUM_LESSON`,
`NounEngine.KASUS_LESSON/PLURAL_LESSON` sowie `SentenceEngine.ATTRIBUT_LESSON/ADVERBIAL_LESSON/
ADVERBIAL_BLOSS_LESSON/DATIVOBJEKT_LESSON/PASSIV_LESSON/ACI_LESSON`.
`latein-tests/test-gating-cursus.js` prüft diese Konstanten gegen die Tabelle und dazu, dass die
erzeugten Sätze pro Lektionsgrenze nichts Verfrühtes enthalten.

Zwei Cursus-Eigenheiten, die beim Gating auffallen:
- Der **Infinitiv steht schon in L1** – Cursus lernt das Verb über den Infinitiv, nicht über die
  3. Person. Kastell und Principia dürfen also von Anfang an danach fragen.
- Der **Ablativ zerfällt in zwei Stufen**: präpositional ab L7, bloßer Ablativ erst ab L8.
  Dafür gibt es die zusätzliche Konstante `ADVERBIAL_BLOSS_LESSON`, die es in der Pontes-Fassung
  nicht gibt.

## Offene Punkte / auf der Liste

- **Semantik Stufe 2: die Objektseite.** Stufe 1 (Belebtheit des Subjekts) ist umgesetzt und hat
  die Quote semantisch unmöglicher Sätze von 41,5 % auf 0 gedrückt. Ungeprüft bleibt das Objekt:
  rund 49 % der Aktivsätze haben eines, und *poēta vulnus legit* ist weiterhin möglich. Dafür
  bräuchte es grobe Sachfelder an den Nomen (Flüssigkeit, Ort, Text, Abstraktum …) und die
  passende Erwartung am Verb. Deutlich mehr Aufwand als Stufe 1 – erst angehen, wenn im Spiel
  auffällt, dass es stört.

- Satzwertige Konstruktionen: Der **AcI** ist in `Arcus.html` umgesetzt. Offen sind
  **Participium coniunctum** (PPP ab L18, PPA ab L20) und **Ablativus absolutus** (ab L22) –
  gedacht als weitere Modi desselben Spiels, mit einem gemischten Modus als eigentlichem Ziel:
  Der Unterschied zwischen PC und Abl. abs. ist genau die Frage, ob das Bezugswort des Partizips
  im Hauptsatz steht, also ob `head` dorthin zeigt oder nicht.
  Dafür fehlt in `verbEngine.js` noch eine PPP-Deklination (das us/a/um-Muster gibt es bereits
  in `getGerundivumDeclension` und ließe sich nachnutzen).
- Achievement-Kategorie-Tracking (`recordCategoryAttempt`) ruft inzwischen jedes Spiel auf, das
  eine sinnvolle Kategorie hat. Ohne bleiben nur Quiz.html (Kulturwissen, kein Wortschatz) und
  IssaJump.html (reine Bedeutungsabfrage – könnte die Wortart-Zähler mitbedienen, tut es aber
  noch nicht).

## Arbeitsweise, die sich bewährt hat

- Vor größeren Änderungen kurz den Plan/Ansatz erklären, dann erst umsetzen.
- Nach jeder Code-Änderung: Syntax prüfen, dann mit jsdom funktional testen, bevor etwas als
  "fertig" gemeldet wird.
- Bei Unklarheiten über Lehrplan-Reihenfolge oder didaktische Gewichtung lieber nachfragen als
  eine Annahme zu treffen – der Nutzer ist Fachlehrer und hat klare Vorstellungen dazu.
