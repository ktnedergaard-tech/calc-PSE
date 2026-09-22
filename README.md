# Projektkalkulator

En selvstændig HTML-side, der beregner fakturerbare timer, omkostning pr. time og
dækningsgrad for et konsulentprojekt. Bygget som en moderne, mobilvenlig
erstatning for `Calc_consultancy.xlsx`, så salgsteamet hurtigt kan lave beregninger
uden at åbne Excel.

Live: https://ktnedergaard-tech.github.io/calc-PSE/ (deployes automatisk via
GitHub Actions ved push til `claude/fervent-hypatia-m3udwf`, se
`.github/workflows/deploy-pages.yml`).

Åbn `index.html` direkte i en browser — ingen build-trin eller server er nødvendig.

## Beregningslogik

Inputs (venstre panel): kunde, konsulent, projektperiode, månedsløn (inkl. 8 %
pension), øvrige omkostninger og salgspris pr. time.

Beregnet (højre panel), i samme rækkefølge som i den oprindelige regnearksmodel:

1. **Arbejdsdage** = hverdage i perioden, ekskl. danske helligdage
2. **Arbejdstimer** = arbejdsdage × 7,4 t
3. **Antal måneder** = arbejdsdage ÷ (gennemsnitligt antal arbejdsdage pr. måned i startåret)
4. **Optjent ferie** = måneder × 2,08 dage × 7,04 t
5. **Fakturerbare timer** = arbejdstimer − optjent ferie
6. **Lønomkostning** = månedsløn × måneder
7. **Omkostning pr. fakturerbare time** = (lønomkostning + øvrige omkostninger) ÷ fakturerbare timer
8. **Dækning pr. time** = salgspris − omkostning pr. time
9. **Dækningsgrad** = dækning pr. time ÷ salgspris

Danske helligdage (nytårsdag, skærtorsdag, langfredag, påskedag, 2. påskedag,
Kristi himmelfartsdag, pinsedag, 2. pinsedag, juledag, 2. juledag) beregnes
dynamisk for et hvilket som helst år ud fra påskedatoen (Gauss' algoritme) —
værktøjet er derfor ikke bundet til en fast liste af år, som i den oprindelige
`Helligdage`-fane i regnearket. Store Bededag indgår ikke, da den er afskaffet
fra 2024.

Seneste indtastninger gemmes lokalt i browseren (`localStorage`), så feltværdier
huskes ved genbesøg — data deles ikke og sendes ikke nogen steder.
