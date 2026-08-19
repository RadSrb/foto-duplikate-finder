# Foto-Duplikate finden

Zwei Werkzeuge gegen doppelte Fotos. Beide rechnen ausschließlich lokal im Browser — es wird nichts hochgeladen.

| | [`index.html`](index.html) — iPhone | [`pc.html`](pc.html) — Computer |
|---|---|---|
| Läuft in | Safari, jedem Browser | nur Chrome / Edge am PC |
| Findet | bit-identische **und** ähnliche Bilder | bit-identische **und** ähnliche Bilder |
| Löscht | nichts, erzeugt eine Checkliste | löscht die Dateien wirklich |
| Rechnet | im Haupt-Thread (Safari kennt kein OffscreenCanvas) | parallel in Web Workern |
| HEIC | dekodiert Safari nativ | nur nach Umstellung auf JPEG |

## Beide Seiten nutzen dasselbe Verfahren

Der Fingerabdruck-Vergleich ist in beiden Dateien wortgleich (ein Testskript prüft das),
damit Handy und PC über dieselben Fotos nicht verschieden urteilen.

## `pc.html` — die PC-Version

Arbeitet auf dem iCloud-Fotos-Ordner von *iCloud für Windows*. Was dort gelöscht wird,
verschwindet auch aus der iCloud-Mediathek und damit vom iPhone.

**Voraussetzung:** In den Foto-Einstellungen von iCloud für Windows
*„Hocheffizientes Original beibehalten"* abschalten, damit JPEG statt HEIC ankommt —
Chrome kann HEIC nicht dekodieren. Kommt trotzdem HEIC an, sagt die App es im Klartext
und überspringt die Dateien, statt still falsche Ergebnisse zu liefern.

### So erkennt sie ähnliche Bilder

Jedes Foto wird auf ein 9×8-Graustufenraster eingedampft; der Vergleich benachbarter
Helligkeiten ergibt einen 64-Bit-Fingerabdruck (dHash). Zwei Bilder gelten als ähnlich,
wenn sich wenige dieser Bits unterscheiden.

An echten Bilddaten gemessen:

| Kopier-Art | Abstand | entspricht |
|---|---|---|
| nur neu komprimiert | 1 Bit | 98 % |
| auf 90 % verkleinert | 1 Bit | 98 % |
| auf 50 % verkleinert | 3 Bit | 95 % |
| Serienbild vom selben Motiv | 14 Bit | 78 % |
| völlig anderes Bild | 33 Bit | 48 % |

Zwischen Kopien (95–98 %) und Serienbildern (~78 %) liegt eine Lücke. Der Regler reicht
deshalb von 100 % bis 70 % und startet bei 90 %.

### Sicherheitsnetze

- **Strenge Gruppen:** Jedes Bild einer Gruppe muss mit *jedem* anderen über der Schwelle
  liegen. Sonst entstehen Ketten (A≈B, B≈C, aber A≉C), die ganze Alben zusammenziehen.
- **Screenshots werden ausgeschlossen.** Zwei Screenshots derselben App sind rechnerisch
  sehr ähnlich, zeigen aber verschiedenen Inhalt — die häufigste Fehlerquelle.
- **Behalten wird die größte Datei**, bei Gleichstand die ältere.
- **Trockenlauf zuerst:** Die App listet jede Datei auf und legt sie als Textdatei ab.
  Gelöscht wird erst mit einem eigenen Knopf.
- **Eigener Knopf für ein einzelnes Foto** — zum Prüfen, ob die Löschung wirklich aufs
  iPhone durchschlägt, bevor man Tausende löscht.
- Gelöschte Dateien landen **nicht** im Windows-Papierkorb. Das Netz ist
  „Zuletzt gelöscht" in iCloud: 30 Tage.

### Sonstiges

- Live Photos: die zugehörige `.MOV` wird mitgelöscht.
- Fingerabdrücke werden lokal zwischengespeichert; ein zweiter Durchlauf ist fast sofort fertig.
- Dekodiert wird in mehreren Hintergrund-Prozessen parallel.

## Entwicklung

Statische HTML-Dateien, kein Build, keine Abhängigkeiten. Deploy über Push nach `main`.
