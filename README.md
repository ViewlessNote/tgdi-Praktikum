# TGdI-Praktikum

LTspice-Schaltungen und Simulationsergebnisse zum Praktikum *Technische Grundlagen der Informatik*: vom einzelnen MOSFET als Schalter über CMOS-Grundgatter bis zu einem Schaltnetz aus 74HCT-Bausteinen.

## Inhalt

| Ordner | Schaltung | Beschreibung |
| --- | --- | --- |
| `P2/mosfet/nmos` | `mosfet_lampe.asc` | N-MOSFET (Si7336ADP) als Schalter für eine Lampe (1 Ω, Versorgung 12 V) |
| `P2/mosfet/pmos` | `mosfet_lampe_pmos.asc` | P-MOSFET (Si1013) als High-Side-Schalter für eine Lampe (100 Ω an 5 V) |
| `P2/inverter` | `Draft1.asc` | CMOS-Inverter aus einem PMOS und einem NMOS |
| `P2/nand` | `nand.asc` | CMOS-NAND mit zwei Eingängen (2 PMOS parallel, 2 NMOS in Reihe) |
| `P3` | `P3.asc` | Schaltnetz mit 4 Eingängen und 4 Ausgängen aus 74HCT-Gattern |
| `P3/74HCT`, `sym/EXTRA/74HCT` | `74hct.lib`, `*.asy` | 74HCT-Modellbibliothek und Symbole für LTspice |

## Praktikum 2 – MOSFETs und CMOS-Gatter

### MOSFET als Schalter

- **NMOS:** Drain an `UD` (+12 V), Lampe `L1` zwischen Source und Masse; das Gate wird über `UG` mit einem 0–5-V-Puls angesteuert. Simulation: `.tran 10`.
- **PMOS (High-Side):** Source an `UD` (+5 V), Lampe `L1` zwischen Drain und Masse. Gate auf 5 V = aus, Gate auf 0 V = an. Simulation: `.tran 10`.

### CMOS-Inverter

Ein PMOS zieht den Ausgang nach +5 V, ein NMOS nach Masse. Der Eingang wird mit einem 0–5-V-Puls (10 ns Pulsbreite, 100 ns Periode) angesteuert. Simulation: `.tran 0 300n 0 0.01n`.

### CMOS-NAND

| a | b | out |
| --- | --- | --- |
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Die Eingänge `a` (Periode 20 µs) und `b` (Periode 40 µs) durchlaufen alle vier Kombinationen. Simulation: `.tran 40u`.

## Praktikum 3 – Schaltnetz mit 74HCT-Bausteinen

Die vier Eingänge `x0`…`x3` (im Schaltplan auch A…D) werden als Binärzähler mit Pulsquellen angesteuert (Perioden 2 / 4 / 8 / 16 ms), sodass in `.tran 0.02` alle 16 Eingangskombinationen durchlaufen werden.

Die Ausgänge sind in NAND-NAND-Form realisiert:

```
y0 = x0
y1 = x1·¬x3 + x1·¬x0 + x0·¬x1·x3
y2 = x2·¬x3 + x0·¬x2·x3 + x1·¬x2·x3 + ¬x0·¬x1·x2
y3 = x3
```

Für `x3 = 0` werden die unteren drei Bits unverändert durchgereicht, für `x3 = 1` werden sie durch ihr Zweierkomplement ersetzt – das Schaltnetz wandelt also zwischen Vorzeichen-Betrag-Darstellung und Zweierkomplement um (`x3` ist das Vorzeichenbit).

Verwendete Bausteine:

| Baustein | Funktion | Instanzen |
| --- | --- | --- |
| 74HCT04 | Inverter | U1–U4 |
| 74HCT00 | NAND, 2 Eingänge | U5, U6, U9 |
| 74HCT10 | NAND, 3 Eingänge | U7, U8, U10–U12 |
| 74HCT20 | NAND, 4 Eingänge | U13 |

## Verwendung

1. [LTspice](https://www.analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html) installieren (erstellt mit LTspice 26.0.2 für macOS).
2. Die gewünschte `.asc`-Datei öffnen und die Simulation starten (*Run*).
3. Signale im Plot-Fenster anklicken; für Inverter und NAND liegen gespeicherte Plot-Einstellungen (`.plt`) bei.

### Hinweis zur 74HCT-Bibliothek

`P3.asc` bindet die Symbole relativ zum eigenen Ordner ein (`P3/74HCT/*.asy`). Die Symbole verweisen auf die Modelldatei unter `sym/EXTRA/74HCT/74hct.lib` (relativ zum Schaltplan: `../sym/EXTRA/74HCT/74hct.lib`). Die Ordnerstruktur des Repositories muss daher erhalten bleiben, damit P3 ohne globale Installation der Bibliothek simuliert.

## Dateitypen

| Endung | Bedeutung |
| --- | --- |
| `.asc` | LTspice-Schaltplan |
| `.asy` | LTspice-Symbol |
| `.lib` | SPICE-Modellbibliothek |
| `.net` | Erzeugte Netzliste |
| `.raw`, `.op.raw` | Simulationsergebnisse (Transienten- bzw. Arbeitspunktanalyse) |
| `.log` | Simulationsprotokoll |
| `.plt` | Plot-Einstellungen |

`.net`, `.raw` und `.log` werden von LTspice bei jedem Simulationslauf neu erzeugt.
