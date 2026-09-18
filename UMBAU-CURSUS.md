# Umbau auf Cursus – Stand und offene Punkte

Diese Fassung ist am 18.09.2026 aus der Pontes-Arena (`Latein_Plattform`, Stand `23a03c1`)
kopiert und auf das Lehrbuch *Cursus* (Neue Ausgabe) umgestellt worden. Die Pontes-Arena
bleibt davon unberührt; beide Ordner sind eigenständige Git-Repositories.

**Gedacht ist diese Fassung derzeit für die Klassenstufe 6**, also für das erste Lernjahr.
Der Wortschatz endet deshalb bewusst bei Lektion 6 – nicht, weil der Umbau unfertig wäre.
Alles, was das Lehrbuch später einführt (Ablativ ab L7, Perfekt ab L11, AcI ab L9, Passiv
ab L18), ist im Code bereits korrekt hinterlegt und schaltet sich frei, sobald die
entsprechenden Wörter im Pool stehen.

## Erledigt

**Wortschatz** – `vocabulary.js` enthält jetzt 175 Einträge, Lektion 1–6, in der Reihenfolge
der Wortschatzseiten des Lehrbuchs (S. 222–227).

* 140 Einträge sind aus der Pontes-Fassung übernommen: Stammformen, Deklinations- und
  Konjugationsklasse, `valenz`, `subjBelebt` und `belebt` bleiben erhalten, die Lektionsnummer
  und die Bedeutung folgen dem Cursus-Wortschatz.
* 35 Einträge gab es dort nicht und sind neu angelegt – siehe „Offen" unten.
* `perfectLesson` / `pppLesson` / `compLesson` stehen pauschal auf den Cursus-Einführungs-
  lektionen (Perfekt L11, PPP L18, Steigerung L27).

**Lektions-Gating** – alle Konstanten folgen dem Cursus-Inhaltsverzeichnis (Tabelle in
`CLAUDE.md`):

| Konstante | Pontes | Cursus |
|---|---|---|
| `NounEngine.KASUS_LESSON` | nom 1, akk 1, gen 6, abl 7, dat 9 | nom 1, **akk 3**, **gen 4**, **dat 5**, abl 7 |
| `SentenceEngine.ATTRIBUT_LESSON` | 6 | 4 |
| `SentenceEngine.DATIVOBJEKT_LESSON` | 9 | 5 |
| `SentenceEngine.ACI_LESSON` | 8 | 9 |
| `SentenceEngine.PASSIV_LESSON` | 15 | 18 |
| `VerbEngine.INFINITIV_LESSON` Präs. Akt. | 2 | **1** |
| `VerbEngine.IMPERATIV_LESSON` | 4 | 6 |
| Tempora Aktiv | Perf 10, Impf 12, Fut 13, Plqpf 14 | Impf 10, Perf 11, Plqpf 13, **Fut 25** |
| Tempora Passiv | Präsensstamm 15, Perf 16 | Perf/Plqpf 18, Präsensstamm 20, Fut 25 |
| `VerbEngine.PPA_LESSON` | 20 | 21 |
| Konjunktiv | Impf/Plqpf 24, Präs 28, Perf 29 | Impf 22, Plqpf 23, Präs/Perf 26 |
| Gerundium / Gerundivum | 33 / 34 | 31 / 31 |

**Zwei neue Konstanten**, weil Cursus feiner unterteilt als Pontes:

* `SentenceEngine.AKKUSATIVOBJEKT_LESSON = 3` – in Pontes kam der Akkusativ schon in L1,
  ein Gating war deshalb nie nötig. Ohne diese Konstante baute die Satz-Engine in Cursus-L1
  Sätze wie *portam asinus exspectat*.
* `SentenceEngine.ADVERBIAL_BLOSS_LESSON = 8` – Cursus führt den Ablativ in zwei Schritten ein:
  präpositional in L7, bloß in L8. Adverbiale ohne Präposition (Zeit, Mittel) erscheinen
  deshalb erst ab L8.

**Ein Fehler nebenbei behoben:** Die Satz-Engine würfelte den Numerus unabhängig von
`PLURAL_LESSON`, erzeugte also schon in Lektion 1 Pluralformen. Das betraf Subjekt, Objekt,
Attribut, adverbiale Bestimmung, den Handlungsträger im Passivsatz und den AcI. Alle Stellen
gehen jetzt über `wuerfelPlural(maxLesson)`. **Der Fehler steckt auch in der Pontes-Fassung**
(dort PLURAL_LESSON ebenfalls 2) und ist dort noch nicht behoben.

**Sichtbare Lektionsangaben** in `Arcus.html`, `Kastell.html`, `Villa.html` und `index.html`
sind auf die Cursus-Zahlen umgeschrieben (AcI ab L9, Zeitverhältnis ab L14, Tempora-Reihenfolge,
Adjektive ab L6, konsonantische Adjektive ab L15).

**Tests** – `latein-tests/test-gating-cursus.js` prüft 28 Konstanten, das Rollen-Gating über
elf Lektionsgrenzen, das Numerus-Gating, die Kasusreihenfolge und den Wortschatz. Der Harness
dort nimmt jetzt `LATEIN_PROJ` entgegen; `smoke.js` und `test-aci.js` sind lehrbuchunabhängig
gemacht (sie hatten Pontes-Wörter bzw. die Zahl 8 fest verdrahtet).

## Offen

1. **Die 35 neu angelegten Einträge fachlich gegenlesen.** `valenz`, `subjBelebt` und `belebt`
   stammen nicht aus dem gegengelesenen Pontes-Bestand, sondern sind neu gesetzt. Betroffen:
   asinus, stāre, (nōn) placet, sōlum, mātrōna, item, citō, appropinquāre, cessāre, apportāre,
   mālum, unde?, licēre/licet, spectāculum, cōnsul, avē!, simulācrum, initium, ārdor, invādere,
   sūmere, dēcēdere, accurrere, reus, fuga, fugam petere, plaudere, aliēnus, maestus, ventus,
   secundus, nauta, multum, necāre, praecipitāre.
   Besonders prüfenswert: `appropinquāre` steht auf `valenz: "dat"`, `plaudere` auf
   `["intrans","dat"]`, `praecipitāre` auf `["akk","intrans"]`.

2. **Kultur-Quiz.** `quizData.js` trägt unverändert die 26 Pontes-Fragen. Für „Das Römische
   Reich" (L3) und „Reisen zur See" (L6) fehlen Fragen ganz. Der Dateikopf sagt es noch einmal.

3. **`Pronomina.html`.** Die Sätze sind handkuratiert und benutzen Pontes-Wortschatz; die
   Pronomina selbst kommen im Cursus erst ab L15. Das Spiel ist im Startbildschirm und auf
   der Index-Karte als „noch nicht umgestellt" gekennzeichnet, läuft aber weiter.

4. **Lektion 7 aufwärts.** Sobald die nächsten Wortschatzseiten vorliegen, hinten an
   `vocabulary.js` anhängen. Die Gating-Konstanten stehen schon für den ganzen Lehrgang bereit.
   Bei der Gelegenheit `perfectLesson` pro Verb prüfen: Cursus verteilt die Perfektbildungen
   auf L11 (-v-, -u-, -s-) und L12 (Reduplikation, Dehnung, ohne Veränderung).

5. **Veröffentlichung.** Das Repository liegt öffentlich auf
   `github.com/9Konrad9/Latein_Arena_Cursus`, GitHub Pages läuft von `main` aus dem
   Wurzelverzeichnis. Adresse für die Klasse:
   **https://9konrad9.github.io/Latein_Arena_Cursus/**
   (Die Pontes-Arena liegt unverändert auf `9Konrad9/Latein_Plattform`.)

## Testlauf

```
cd C:\Users\konra\latein-tests
node test-gating-cursus.js
```

Für die übrigen Tests die Umgebungsvariable setzen, sonst prüfen sie die Pontes-Arena:

```
set LATEIN_PROJ=C:/Users/konra/OneDrive - Privatgymnasium Schwetzingen gGmbH/Latein_Plattform_Cursus
node smoke.js & node test-engine.js & node test-aci.js & node test-circus.js
```
