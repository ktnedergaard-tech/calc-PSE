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

Inputs (venstre panel): kunde, konsulent, projektperiode, øvrige omkostninger,
salgspris pr. time og risiko timer pr. måned — plus enten en månedsløn eller en
freelancer-timepris, afhængig af fanen valgt under Økonomi:

- **Projektansat**: indtast månedsløn (inkl. 8 % pension)
- **Freelance**: indtast den timepris freelanceren fakturerer

Beregnet (højre panel), i samme rækkefølge som i den opdaterede regnearksmodel:

1. **Arbejdsdage** = hverdage i perioden, ekskl. danske helligdage
2. **Arbejdstimer** = arbejdsdage × 7,4 t
3. **Antal måneder** = arbejdsdage ÷ (gennemsnitligt antal arbejdsdage pr. måned i startåret)
4. **Optjent ferie** = måneder × 2,08 dage × 7,04 t
5. **Fakturerbare timer** = arbejdstimer − optjent ferie
6. **Risiko timer** = måneder × risiko timer pr. måned (standard 7,4 t/md., ≈ 1 sygedag)
7. **Effektive timer** = fakturerbare timer − risiko timer
8. **Lønomkostning**
   - Projektansat: månedsløn × måneder
   - Freelance: freelancer-timepris × effektive timer
9. **Omkostning pr. effektiv time** = (lønomkostning + øvrige omkostninger) ÷ effektive timer
10. **Dækning pr. time** = salgspris − omkostning pr. effektiv time
11. **Dækningsgrad** = dækning pr. time ÷ salgspris
12. **Projekt omsætning** = effektive timer × salgspris
13. **Projekt dækningsbidrag** = projekt omsætning − lønomkostning − øvrige omkostninger

Al tidsberegning (arbejdsdage, ferie, risiko, effektive timer) er den samme
uanset fane — kun selve lønomkostningen beregnes forskelligt.

Danske helligdage (nytårsdag, skærtorsdag, langfredag, påskedag, 2. påskedag,
Kristi himmelfartsdag, pinsedag, 2. pinsedag, juledag, 2. juledag) beregnes
dynamisk for et hvilket som helst år ud fra påskedatoen (Gauss' algoritme) —
værktøjet er derfor ikke bundet til en fast liste af år, som i den oprindelige
`Helligdage`-fane i regnearket. Store Bededag indgår ikke, da den er afskaffet
fra 2024.

Seneste indtastninger gemmes lokalt i browseren (`localStorage`), så feltværdier
huskes ved genbesøg — data deles ikke og sendes ikke nogen steder.

## Mobilvisning

Under 860 px bredde (telefon) stables input- og resultatpanelerne, dato­felterne
lægger sig oven på hinanden under 420 px, og en fast bjælke i bunden viser
dækningsgrad og dækning pr. time konstant, mens man udfylder felterne — tryk på
den for at hoppe ned til det fulde resultat. Inputfelter bruger 16 px skrift på
mobil, så Safari på iPhone ikke zoomer ind ved fokus.
