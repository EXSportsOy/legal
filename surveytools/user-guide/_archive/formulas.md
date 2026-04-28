# Arkistoidut kaavaosiot

Poistettu julkisesta user guidesta: 2026-03-22.

## Tarkoitus

Tämä tiedosto sisältää moduuleista poistetut kaavaosiot alkuperäisessä muodossaan.
Tarkoitus on säilyttää ne helposti palautettavina myöhempää käyttöä varten ilman,
että ne näkyvät nykyisillä julkisilla ohjesivuilla.

## Rajaus

- Varsinaiset HTML-kaavalohkot on säilytetty tässä tiedostossa mahdollisimman muuttumattomina.
- Moduulisivuista poistettiin vain kaavat; muu opasteteksti jäi käyttöön.
- Tätä tiedostoa ei ole linkitetty user guiden navigaatioon.

## Palautusohje

1. Avaa tämän tiedoston haluttu moduuliosio.
2. Kopioi kyseisen moduulin alla oleva koko `<details> ... </details>` -lohko.
3. Liitä lohko takaisin oikeaan tiedostoon kohtaan, josta se poistettiin:
   - yleensä moduulin varsinaisen selitystekstin jälkeen
   - ennen `Notes`, `Notes & limitations`, `Practical notes` tai vastaavaa loppuosiota.
4. Jos moduulissa on useampi lohko, palauta ne samassa järjestyksessä kuin ne ovat tässä tiedostossa.
5. Säilytä sisennys ja sisältö sellaisenaan, jotta palautus vastaa alkuperäistä versiota.

## Yleiset periaatteet

- Jos haluat palauttaa kaavat vain yhteen moduuliin, muuta vain kyseistä moduulitiedostoa.
- Shore tank -moduulissa on kaksi erillistä palautettavaa lohkoa: `CTSh` ja `FRC`.
- Jos kaavat halutaan myöhemmin julkaista uudelleen, tämä tiedosto toimii palautuksen lähteenä.

## `user-guide/modules/interpolation.html`

Palautettava lohko lisätään kahden interpolointimenetelmän kuvauksen jälkeen ja ennen osiota `Notes & limitations`.

```html
<details>
  <summary>Formulas (optional)</summary>
  <p class="small">Shown for transparency. Not a substitute for official standards or certified tables.</p>
  <h3>Linear</h3>
  <pre><code>y = y1 + (x - x1) * (y2 - y1) / (x2 - x1)

Special case:
- if x1 == x2 and x == x1, then y = y1
- otherwise invalid (no slope)
</code></pre>

  <h3>One-way</h3>
  <pre><code>x1 = floor(ullage / interval) * interval
x2 = x1 + interval
result = linear(x1, y1, x2, y2, ullage)
</code></pre>

  <h3>Two-way</h3>
  <pre><code>x1 = floor(ullage / ullageInterval) * ullageInterval
x2 = x1 + ullageInterval

t1 = floor(actualTrim / trimInterval) * trimInterval
t2 = t1 + trimInterval

resultA = linear(x1, y1a, x2, y2a, ullage)   // at trim t1
resultB = linear(x1, y1b, x2, y2b, ullage)   // at trim t2

final = linear(t1, resultA, t2, resultB, actualTrim)
</code></pre>
</details>
```

## `user-guide/modules/wedge.html`

Palautettava lohko lisätään laskennan yleiskuvauksen jälkeen ja ennen osiota `Practical notes`.

```html
<details>
  <summary>Formulas (optional)</summary>
  <p class="small">These formulas match the app logic and are shown for transparency.</p>
  <pre><code>trim = draftAft - draftFwd
trimFactor = trim / LBP

adjustedSounding = obsSounding + (distAftBhd - (refHeight * trimFactor)) * trimFactor
innageForTable = adjustedSounding / 2

If trimFactor == 0:
  wedgeVolume = 0

wedgeLength = adjustedSounding / trimFactor
If wedgeLength &gt; tankLength:
  Not a wedge case

wedgeVolume = (volumeFromTable * adjustedSounding) / (tankLength * trimFactor)
</code></pre>
</details>
```

## `user-guide/modules/shore-tank.html`

Tässä moduulissa on kaksi erillistä palautettavaa lohkoa. Ne palautetaan omien käyttöohjeidensa jälkeen:

- ensin `CTSh formulas (optional)`
- sitten `FRC formulas (optional)`

```html
<details>
  <summary>CTSh formulas (optional)</summary>
  <pre><code>tl = (tempUpper + tempMiddle + tempLower) / 3

If insulated:
  tsh = tl
Else:
  tsh = (7 * tl + tempAmbient) / 8

deltaT = refTemp - tsh

ctsh = 1 + (2 * coefficient * deltaT) + (coefficient^2 * deltaT^2)
</code></pre>
</details>
```

```html
<details>
  <summary>FRC formulas (optional)</summary>
  <p class="small">
    The app uses an internal CTL/VCF calculator as part of this correction.
    This guide does not reproduce paid-standard content.
  </p>
  <pre><code>ctl = CTL(dens15vac, temp, group="B")
dens15air = dens15vacTo15Air(dens15vac)

frc = roofWeight / (dens15air * ctl)
</code></pre>
</details>
```

## `user-guide/modules/unit-conversions.html`

Palautettava lohko lisätään muunnoslistojen jälkeen ja ennen osiota `Notes`.

```html
<details>
  <summary>Formulas (optional)</summary>
  <p class="small">
    These are the explicit formulas used by the app’s conversion helpers.
    Any CTL/VCF behaviour is described at a high level only.
  </p>

  <h3>15°C vacuum to 15°C air</h3>
  <pre><code>dens15air = dens15vac * 1.000149926 - 1.199407795
</code></pre>

  <h3>15°C air to 15°C vacuum</h3>
  <pre><code>dens15vac = (dens15air + 1.199407795) / 1.000149926
</code></pre>

  <h3>Relative density @15°C</h3>
  <pre><code>relDens15 = dens15vac / 999.102
</code></pre>

  <h3>API @60°F to relative density @60/60°F</h3>
  <pre><code>relDens60 = 141.5 / (api + 131.5)
</code></pre>

  <h3>m³ @15°C to Long tons (in air)</h3>
  <pre><code>dens15air = dens15vacTo15Air(dens15vac)
kgToLongTons = 0.0009842065276
longTons = cbmAt15 * dens15air * kgToLongTons
</code></pre>

  <h3>m³ @15°C to bbl @60°F (high level)</h3>
  <pre><code>vcf = CTL_or_VCF(dens15vac, baseTemp=15.5556°C, group)
barrels = cbmAt15 / (vcf * 0.1589872949)
</code></pre>
</details>
```


