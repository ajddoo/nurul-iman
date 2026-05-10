# 13 Countdown Faza — Test Plan
**Verzija:** v2.26.61
**Datum:** 10. maj 2026.
**Cilj:** Provjera da svaka faza pravilno prikazuje countdown za 5 različitih lokacija.

---

## Test lokacije

| ID | Grad | Lat / Lng | Region | Posebnost |
|----|------|-----------|--------|-----------|
| T | **Tuzla** | 44.54, 18.67 | BiH/Sandžak | vaktija.ba radi |
| M | **Mekka** | 21.39, 39.86 | Hejaz | UK + ISNA dual |
| B | **Berlin** | 52.52, 13.41 | EU dijaspora | ISNA standardno |
| I | **Istanbul** | 41.01, 28.98 | Turska | ISNA (ne vaktija.ba) |
| S | **Stockholm** | 59.33, 18.07 | Skandinavija | Polarni warning ljeti |

---

## Test URL (svih 5 lokacija)

```
https://ajddoo.github.io/nurul-iman-by-divan/?lat=44.54&lng=18.67&city=Tuzla&time=03:30
https://ajddoo.github.io/nurul-iman-by-divan/?lat=21.39&lng=39.86&city=Mekka&time=03:30
https://ajddoo.github.io/nurul-iman-by-divan/?lat=52.52&lng=13.41&city=Berlin&time=03:30
https://ajddoo.github.io/nurul-iman-by-divan/?lat=41.01&lng=28.98&city=Istanbul&time=03:30
https://ajddoo.github.io/nurul-iman-by-divan/?lat=59.33&lng=18.07&city=Stockholm&time=03:30
```

URL parametar `?time=HH:MM` simulira sat dana — može se mijenjati za testiranje različitih faza.

---

## Faze (13 faza × 5 lokacija = 65 testova)

### Faza 1 — Polovina noći → kraj sehura

**Period:** sredina noći do kraja sehura.

| Lokacija | Lijevi countdown | Desni countdown | Napomena |
|----------|------------------|-----------------|----------|
| Tuzla (T) | "do kraja sehura · **lokalno**" do **zoraBA** | "do sabaha · ISNA" do ISNA sabah | vaktija.ba aktivna |
| Tuzla (T, API pao) | "do kraja sehura · **Ummul-kura**" do UK | "do sabaha · ISNA" | UK fallback |
| Mekka (M) | "do sabaha · **Ummul-kura · kraj sehura**" do UK | "do sabaha · ISNA" do ISNA | Hejaz dual |
| Berlin (B) | "do kraja sehura · **Ummul-kura**" do UK | "do sabaha · ISNA" | Standardno |
| Istanbul (I) | "do kraja sehura · **Ummul-kura**" do UK | "do sabaha · ISNA" | Standardno |
| Stockholm (S, ljeti) | NEMA — sabah/sehur=null → fallback ekran | — | Polarni warning |

**Test URL primjer (Tuzla, 02:30):** `?lat=44.54&lng=18.67&city=Tuzla&time=02:30`

---

### Faza 2 — Kraj sehura → sabah (samo non-Hejaz)

**Period:** sehur prošao, sabah još nije.

| Lokacija | Layout |
|----------|--------|
| Tuzla | "do sabaha · ISNA" centrirano |
| Mekka | **Faza 2 ne važi** — Saudija prelazi direktno u 2b |
| Berlin | "do sabaha · ISNA" |
| Istanbul | "do sabaha · ISNA" |

**Test URL (Tuzla, 03:50):** sabah je oko 03:48, ovo je granični slučaj.

---

### Faza 2b — UK sabah → ISNA sabah (samo Hejaz)

**Period:** UK sabah već nastupio, ISNA sabah još nije (~15-25 min razlika).

| Lokacija | Layout |
|----------|--------|
| Mekka | LIJEVO: "do sabaha · ISNA"<br>DESNO: "do izlaska sunca · kraj sabah-namaza"<br>STATUS: **"Nastupio sabah po Ummul-kura"** |

**Test URL (Mekka, 04:30):**
`?lat=21.39&lng=39.86&city=Mekka&time=04:30`

---

### Faza 3 — Sabah → izlazak sunca

| Lokacija | Lijevi | Desni |
|----------|--------|-------|
| Sve | "do izlaska sunca · kraj sabah-namaza" | "do duha-namaza · 15 min nakon izlaska" |

**Test URL (Tuzla, 05:00):** izlazak ~05:25.

---

### Faza 4 — Izlazak → duha **(ZABRANJENO!)**

| Lokacija | Lijevi | Desni | Status |
|----------|--------|-------|--------|
| Sve | "do duha-namaza · ISNA/UK" | "do podne · ISNA/UK" | **"Zabranjeno vrijeme za namaz"** |

**Vizualno:** kartica `cd-warning` (terra cotta border + boja).

**Test URL (Tuzla, 05:30):** izlazak ~05:25, duha ~05:40 (15 min nakon).

---

### Faza 5 — Duha → kraj duhe

| Lokacija | Lijevi | Desni |
|----------|--------|-------|
| Sve | "do isteka duha-namaza" | "do podne" |

**Test URL (Tuzla, 11:00):** podne ~12:42, kraj duhe ~12:27.

---

### Faza 6 — Kraj duhe → podne **(ZABRANJENO!)**

**Vrlo kratko — 15 minuta.**

| Lokacija | Lijevi | Desni | Status |
|----------|--------|-------|--------|
| Sve | "do podne · sunce u zenitu" | NEMA | **"Zabranjeno vrijeme za namaz"** |

**Test URL (Tuzla, 12:35):**

---

### Faza 7 — Podne ISNA → lokalni podne **(samo BiH/Sandžak)**

**Period:** ISNA podne nastupi (npr. 12:42), lokalni podne kasnije (13:00 ljeto, 12:00 zima).

| Lokacija | Lijevi | Desni |
|----------|--------|-------|
| Tuzla (ljeto) | "do ezana u džamiji · lokalno podne" do 13:00 | "do ikindije · ISNA" |
| Mekka, Berlin, Istanbul | **Faza 7 ne važi** — nemaju lokalni podne |

**Test URL (Tuzla, 12:50):**

---

### Faza 8 — Podne → ikindija

| Lokacija | Sub | Layout |
|----------|-----|--------|
| Tuzla (BiH) | "do ikindije · **ISNA · lokalno**" | Centrirano |
| Mekka | "do ikindije · Ummul-kura" | Centrirano |
| Berlin/Istanbul | "do ikindije · ISNA" | Centrirano |

**Test URL (Tuzla, 14:00):**

---

### Faza 9 — Ikindija → akšam

| Lokacija | Lijevi | Desni | Layout |
|----------|--------|-------|--------|
| Tuzla (BiH) | "do akšama · **ISNA · iftar**" | "do ezana u džamiji · akšam · lokalno" | Dual |
| Mekka | "do akšama · **Ummul-kura · iftar**" | NEMA | Single |
| Berlin/Istanbul | "do akšama · **ISNA · iftar**" | NEMA | Single |

**Test URL (Tuzla, 18:00):** akšam ~20:00.

---

### Faza 10 — Akšam → lokalni akšam **(samo BiH)**

**Period:** ISNA akšam nastupio, lokalni akšam (vaktija.ba) još nije (1-2 min kasnije).

| Lokacija | Lijevi | Desni |
|----------|--------|-------|
| Tuzla | "do ezana u džamiji · akšam · lokalno" | "do jacije · ISNA" |
| Ostali | **Faza 10 ne važi** |

**Test URL (Tuzla, 19:59):** akšam ISNA 20:00, akšam lokalno 20:01.

---

### Faza 11 — Akšam → jacija

| Lokacija | Lijevi | Desni |
|----------|--------|-------|
| Tuzla (BiH dual) | "do jacije · ISNA" | "do jacije u džamiji · lokalna jacija" |
| Mekka | "do jacije · Ummul-kura" | NEMA |
| Berlin/Istanbul | "do jacije · ISNA" | NEMA |

**Test URL (Tuzla, 20:30):** jacija ~21:37.

---

### Faza 12 — Jacija ISNA → lokalna jacija **(samo BiH)**

**Period:** Vrlo kratak (1-3 min razlike).

| Lokacija | Lijevi | Desni |
|----------|--------|-------|
| Tuzla | "do jacije u džamiji · lokalni ezan" | "do polovine noći · kraj jacije" |
| Ostali | **Faza 12 ne važi** |

**Test URL (Tuzla, 21:38):**

---

### Faza 13 — Lokalna jacija → polovina noći

| Lokacija | Lijevi | Desni |
|----------|--------|-------|
| Sve | "do polovine noći · kraj jacije" | "do sabaha · sutra · ISNA/Ummul-kura" |

**Test URL (Tuzla, 22:00):** polaNoci ~23:54.

---

## Specijalni scenariji

### A) Saudija (Mekka) — fazni prelaz na sabah
1. **22:00 — Faza 13** (do polaNoci, do sutra sabaha)
2. **00:30 — Faza 1** (Hejaz dual: do UK sabaha, do ISNA sabaha)
3. **04:30 — Faza 2b** (Hejaz: nastupio UK sabah, do ISNA sabaha + do izlaska)
4. **05:00 — Faza 3** (sabah → izlazak)

### B) BiH (Tuzla) — vaktija.ba dostupna
1. **23:00 — Faza 13** (do polaNoci)
2. **02:30 — Faza 1** (do **lokalno** zora, do ISNA sabah)
3. **03:50 — Faza 2** (kratak prelaz, ISNA sabah)
4. **04:00 — Faza 3** (sabah → izlazak)

### C) BiH ako vaktija.ba pada — fallback
- Faza 1 mijenja sub iz "lokalno" u "Ummul-kura"
- Banner u listi vakata: "Za ovu lokaciju nije pronađena lokalna vaktija."

### D) Stockholm (polarni) ljeti
- `sabah=null`, `jacija=null`, `sehurKraj=null`
- Vakti prikazani kao "--:--"
- Source note: warning poruka o astronomskoj kalkulaciji
- Live countdown: fallback `getCountdownTarget` ide na sledeći NE-null vakat (akšam, izlazak, podne, ikindija)

### E) BiH/Sandžak izvan radius-a 50km
- npr. korisnik u nekom selu 80km od najbližeg vaktija.ba grada
- `localUnavailable=true`
- Banner: "Za ovu lokaciju nije pronađena lokalna vaktija."
- Faze rade kao van BiH (UK fallback)

---

## ✅ Status (statička analiza)

Sve 13 faza:
- ✅ Logički ispravno definisane
- ✅ Sve potrebne varijable provjeravane sa `has()` (null-safe)
- ✅ Tekstovi sub label-a su konzistentni
- ✅ Warning status (Faza 4, 6) postavljen ispravno
- ✅ Hejaz specifičnost (Faza 1, 2b) odvojeno tretirana
- ✅ BiH specifičnost (Faza 7, 9-12) odvojeno tretirana
- ✅ `getCountdownTarget` fallback radi za polarne lokacije

**Nema identifikovanih bug-ova u logici.** App spreman za realan test.

---

## 🧪 Realni test (TI radiš)

Pošalji prod URL-ove iznad (sa različitim `?time=` parametrima) i provjeri da svaka faza pravilno prikazuje očekivane vrijednosti.

Ili, jednostavnije: koristi app preko dana, prati šta se prikazuje u svakom periodu, javi ako vidiš nešto neočekivano.
