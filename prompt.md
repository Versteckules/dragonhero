# Dragon Hero – Umbauplan (Ninja → Drache)

Datei: `index.html` (alles bleibt in dieser einen Datei).
Jeden Schritt einzeln ausführen und im Browser testen, bevor der nächste kommt.

## Festgelegte Entscheidungen

| Thema | Entscheidung |
|---|---|
| Drache | Per Canvas gezeichnet, Flügelschlag-Animation |
| Physik | Echte Physik (vx/vy + Schwerkraft), fester Absprungwinkel |
| Ladung | Max. nach ~1,2 s, danach Kappung bei 100 % |
| Anzeige | Ring über dem Drachenkopf, Grün→Gelb→Orange→Rot, %-Zahl |
| Punkte | Steg = +1, rote Mitte = PERFEKT +2, Überspringen ohne Bonus |
| Landung | Auf beliebigem Steg weiter vorne landen; kein Steg = Absturz |
| Vorschau | Flugbahn sichtbar Sprung 1–4, verblasst bis Sprung 10, danach weg |
| Stil | Abend-Berglandschaft, Felssäulen-Stege mit Lava-Glühen |
| Bleibt gleich | `music.mp3`, Musik-Button, Wasserzeichen/Avatar, Startmenü-Aufbau, Ziel 20 Punkte, Geocache-Popup + Koordinaten |

---

## Schritt 1 – Aufräumen & Texte

```text
In index.html:
- Titel/h1 "Ninja Hero" -> "Dragon Hero".
- Intro-Text: "Halte gedrückt, um Sprungkraft zu laden. Loslassen = Sprung." (ZIEL: 20 PUNKTE bleibt).
- Bild-src "avatar.jpg" -> "Avatar.jpg" (2x, Groß/Kleinschreibung).
- Musik, Wasserzeichen, Startmenü, Sieger-Popup, Koordinaten NICHT ändern.
```

## Schritt 2 – Hintergrund

```text
In index.html drawBackground() ersetzen (Hügel/Bäume entfernen):
- Himmel-Verlauf Violett (#2d1b4e) -> Orange (#ff8c42), kleine Sonne am Horizont.
- 2 Parallax-Bergketten (dunkel/heller), verschiedene Scrollfaktoren zu sceneOffset.
- Einige langsam ziehende Wolken.
- drawPlatforms(): Stege als Felssäulen (dunkler Stein, leichte Kanten-Textur), unten oranges Lava-Glühen (Gradient).
- Rote Perfekt-Zone in der Stegmitte beibehalten.
Spiellogik nicht anfassen.
```

## Schritt 3 – Drache zeichnen

```text
In index.html drawHero() durch drawDragon() ersetzen:
- Kleiner Drache (~40x30px) per Canvas: grüner Körper, Bauch heller, Kopf mit Auge+Hörnern, Schwanz, Flügel.
- Flügelschlag: Winkel per Math.sin(Date.now()) animiert, im Stand langsam, in der Luft schnell.
- Füße stehen exakt auf heroY (Stegoberkante), heroX = Mitte.
- Stick-Zeichnung (drawSticks) entfernen.
```

## Schritt 4 – Lade-Ring

```text
In index.html Sticks-Logik ersetzen durch Ladung:
- Neue Variable charge (0..1). Phase "stretching" -> "charging": charge += dt/1200, Kappung bei 1.
- drawChargeRing(): Ring (r=18) über Drachenkopf, füllt sich im Uhrzeigersinn ab 12 Uhr.
- Farbe nach charge: Grün(0) -> Gelb(0.33) -> Orange(0.66) -> Rot(1), interpoliert (HSL 120->0).
- Grauer Hintergrund-Ring, Prozentzahl in der Mitte, leichtes Glühen bei 100 %.
- Drache "duckt" sich beim Laden leicht (Y-Skalierung bis 0.85).
Loslassen startet vorerst noch nichts.
```

## Schritt 5 – Sprungphysik & Landung

```text
In index.html Sprung mit echter Physik:
- Loslassen: Phase "jumping", fester Winkel 55°, v = vMin + charge*(vMax-vMin), vx/vy daraus. GRAVITY konstant.
- Werte so wählen: charge=1 springt ca. 420px weit (reicht über 1 Steg hinweg).
- Pro Frame dt-basiert: vy += GRAVITY*dt; heroX += vx*dt; heroY += vy*dt.
- Landung: wenn vy>0 und Füße kreuzen Stegoberkante und heroX liegt innerhalb eines Stegs -> landen.
  - Steg weiter vorne: +1, Mitte in Perfekt-Zone: +2 ("PERFEKT! +2"). Überspringen gibt keinen Bonus.
  - Eigener Steg (zu kurz): 0 Punkte, zurück zu "waiting".
  - Danach "transitioning": Kamera schwenkt, bis gelandeter Steg links steht (wie bisher).
- Kein Steg getroffen -> Phase "falling", fällt aus dem Bild -> Neustart-Button.
- Immer genug Stege vorausgenerieren (min. 4 vor dem aktuellen).
- 20-Punkte-Check + Geocache-Popup unverändert übernehmen.
- Alten Code (sticks, thePlatformTheStickHits, turning, walking) entfernen.
```

## Schritt 6 – Flugbahn-Vorschau & Feinschliff

```text
In index.html:
- Zähler jumpCount (bei resetGame = 0, +1 je Sprung).
- Während "charging": gepunktete Vorschau-Flugbahn mit aktueller charge (gleiche Physik simulieren).
  Deckkraft: Sprung 1-4 = 1, dann linear bis Sprung 10 auf 0, danach nicht zeichnen.
- Landung: kleine Staubpartikel; Absturz: Drache dreht sich beim Fallen.
- Prüfen: Touch + Maus, Resize, Neustart, 500ms-Restart-Sperre, Musik-Button funktionieren weiter.
```
