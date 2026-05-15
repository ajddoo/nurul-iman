# Font Audit — Moj namaz by Divan
**Verzija:** v2.26.69 (audit pre-implementation)
**Datum:** 11. maj 2026.
**Cilj:** Identifikovati uzroke žalbi na čitljivost i predložiti čistu tipografsku skalu.

---

## 🚨 Glavni nalazi

| Metrika | Vrijednost | Industrijski standard | Status |
|---------|-----------|----------------------|--------|
| Ukupan broj `font-size` deklaracija | **255** | < 50 | ❌ |
| Različitih vrijednosti | **120** | 6-10 | ❌ |
| Elemenata ispod 11px | **61** | < 5 (samo legalni dokumenti) | ❌ |
| Najmanji font u app-i | **8px (0.50rem)** | minimum 12px za body | ❌ |
| `!important` deklaracije sa font-size | **~80** | 0 idealno | ❌ |

**Glavni uzrok žalbi:** veliki broj elemenata između 8-11px koji su nečitljivi za prosječnog korisnika, posebno starije ili osobe sa slabijim vidom.

---

## 📊 Distribucija veličina (top 20)

| Vrijednost | px ekvivalent | Broj pojava | Procjena čitljivosti |
|-----------|---------------|-------------|---------------------|
| `0.66rem` | ~10.6px | 11+ | 🔴 Teško čitljivo |
| `0.78rem` | ~12.5px | 10 | 🟡 Granično |
| `0.62rem` | ~9.9px | 10 | 🔴 Teško čitljivo |
| `0.72rem` | ~11.5px | 9 | 🟡 Granično |
| `0.7rem` | ~11.2px | 7 | 🟡 Granično |
| `0.56rem` | ~9.0px | 7 | 🔴 Nečitljivo |
| `0.92rem` | ~14.7px | 6 | 🟢 OK |
| `0.82rem` | ~13.1px | 6 | 🟡 Marginalno |
| `0.68rem` | ~10.9px | 6 | 🔴 Teško čitljivo |
| `0.98rem` | ~15.7px | 5 | 🟢 OK |
| `0.58rem` | ~9.3px | 5 | 🔴 Nečitljivo |
| `0.50rem` | ~8.0px | 5 | 🔴 Nečitljivo |
| `2.4rem` | ~38px | 4 | 🟢 Display |
| `1rem` | ~16px | 4 | 🟢 OK (base) |
| `0.54rem` | ~8.6px | 3 | 🔴 Nečitljivo |
| `0.52rem` | ~8.3px | 3 | 🔴 Nečitljivo |
| `2.72rem!` | ~43px | 3 | 🟢 Display |

---

## 🎯 Mapiranje po kontekstu

### Glavni countdown (najvažnije za korisnika)
| Element | Trenutno | Procjena |
|---------|---------|----------|
| `#cdLeftTimer` (glavni broj) | `clamp(1.4rem, 5.5vw, 2.1rem)` | 🟢 OK |
| `.danas-next-name` | `0.75rem` (~12px) | 🟡 Granično — labela |
| `.danas-next-name small` | `0.62rem` (~9.9px) | 🔴 SUB-LABELA preslabo |
| iftar varijanta `#cdLeftTimer` | `2.4rem` (~38px) | 🟢 Dobro |
| iftar varijanta `#cdRight .danas-next-name small` | `0.54rem` (~8.6px) | 🔴 Kritično malo |

**Problem:** Sub-labele ispod countdown brojeva su između 9-12px. Korisnici koji ne mogu pročitati "ISNA · lokalno" ili "kraj sabah-namaza" ispod glavnog brojača.

---

### Lista vakata (drugi najvažniji panel)
| Element | Trenutno | Procjena |
|---------|---------|----------|
| `.danas-row-name` (Sabah, Podne, ...) | `0.95rem` (~15.2px) | 🟢 OK |
| `.danas-row-name small` (ISNA, lokalno) | `0.58rem` (~9.3px) | 🔴 Nečitljivo |
| `.danas-row-time` (vrijeme) | `1.1rem` (~17.6px) | 🟢 OK |
| `.danas-row-name.danas-row-local` ("lokalni ezan") | `0.66rem` (~10.6px) | 🔴 Teško |
| Mobilni override (< 390px) `.danas-row-name small` | `0.56rem` (~9px) | 🔴 Kritično |

**Problem:** Sub-labele kraj imena namaza (ISNA, lokalno, Ummul-kura) su među najmanjim elementima u app-i.

---

### Header / Lokacija
| Element | Trenutno | Procjena |
|---------|---------|----------|
| `.danas-city-name` (Tuzla, Mostar) | `1.05rem` (~16.8px) | 🟢 OK |
| `.danas-city-sub` (država) | `0.68rem` (~10.9px) | 🔴 Teško |
| `.danas-cal-day` (datum) | `2.4rem` | 🟢 OK |
| `.danas-cal-year` (godina) | `0.68rem` (~10.9px) | 🔴 Teško |
| Weather temp | `1.6rem` | 🟢 OK |
| Weather cond | `0.7rem` (~11.2px) | 🟡 Marginalno |

**Problem:** Država ispod grada, godina ispod datuma su preslabo.

---

### Source note (ispod liste vakata)
| Element | Trenutno | Procjena |
|---------|---------|----------|
| `.danas-source-note span` | `0.62rem` (~9.9px) | 🔴 Nečitljivo |

**Problem:** Bitna informacija ("ISNA · Lokalno – vaktija.ba") je premalena.

---

### Bottom nav
| Element | Trenutno | Procjena |
|---------|---------|----------|
| `.bnav-label` (Danas, Kibla, Predstojeće) | `0.56rem` (~9px) | 🔴 Nečitljivo |

**Problem:** Glavna navigacija je među najmanjim elementima u app-i. Ovo bi trebalo biti **JASNO** vidljivo.

---

### Kibla panel
| Element | Trenutno | Procjena |
|---------|---------|----------|
| `.kibla-deg` (glavni broj) | `2.4rem` | 🟢 OK |
| `.kibla-deg span` | `1rem` | 🟢 OK |
| `.kibla-dir-deg` | `0.82rem` (~13.1px) | 🟡 Granično |
| Labels | `0.78rem` (~12.5px) | 🟡 Granično |

---

### Predstojeći događaji
| Element | Trenutno | Procjena |
|---------|---------|----------|
| Naziv događaja | `1.1rem` | 🟢 OK |
| Sub label | `0.58rem` (~9.3px) | 🔴 Nečitljivo |
| Datum | `0.82rem` (~13.1px) | 🟡 Granično |

---

## 💡 Predložena tipografska skala

### Filozofija
- **Body minimum: 14px** (0.875rem)
- **Sub-labels minimum: 12px** (0.75rem)
- **Display elementi**: 24-48px
- **Micro/uppercase tags**: 11px MINIMUM, samo ako su bold + letter-spacing
- **Bottom nav**: 13px (nikako ispod)

### Skala (CSS tokeni)

```css
:root {
  /* Tipografska skala — modularna, ratio 1.2 */
  --fs-micro:    0.6875rem;  /* 11px — samo za uppercase tags sa letter-spacing */
  --fs-xs:       0.75rem;    /* 12px — sub-labele, captions */
  --fs-sm:       0.8125rem;  /* 13px — secondary text, source notes, nav */
  --fs-base:     0.9375rem;  /* 15px — body, lista vakata */
  --fs-md:       1.0625rem;  /* 17px — istaknute labele */
  --fs-lg:       1.1875rem;  /* 19px — vremena u listi */
  --fs-xl:       1.5rem;     /* 24px — sekundarni display */
  --fs-2xl:      2rem;       /* 32px — temp, kalendar */
  --fs-3xl:      2.5rem;     /* 40px — countdown desktop */
  --fs-4xl:      3rem;       /* 48px — sehur expanded */
  --fs-5xl:      clamp(2rem, 8vw, 3.5rem); /* responsive display */
}
```

### Težine (CSS tokeni)

```css
:root {
  --fw-light:    300;  /* Display brojevi (countdown, temp, datum) */
  --fw-regular:  400;  /* Body text */
  --fw-medium:   500;  /* Labele, vrijeme u listi */
  --fw-semibold: 600;  /* Naslovi modala, istaknuti elementi */
}
```

### Line-height (CSS tokeni)

```css
:root {
  --lh-tight:   1.1;   /* Display brojevi, jednoredne labele */
  --lh-snug:    1.25;  /* Naslovi */
  --lh-base:    1.45;  /* Body text */
  --lh-loose:   1.6;   /* Duži paragrafi (modali) */
}
```

---

## 🗺 Mapiranje: staro → novo

| Funkcionalna grupa | Staro (varijacije) | Novo (token) | Učinak |
|--------------------|---------------------|--------------|--------|
| **Bottom nav labels** | 0.56rem (9px) | `--fs-sm` (13px) | +44% čitljivost |
| **Vakat sub-labels** (ISNA, lokalno) | 0.54-0.62rem (8.6-9.9px) | `--fs-xs` (12px) | +25-40% |
| **Source note** | 0.62rem (9.9px) | `--fs-sm` (13px) | +31% |
| **City sub (država)** | 0.68rem (10.9px) | `--fs-xs` (12px) | +10% |
| **Vakat names** | 0.95rem (15.2px) | `--fs-base` (15px) | bez promjene |
| **Vakat times** | 1.1rem (17.6px) | `--fs-md` (17px) | bez promjene |
| **Countdown main** | clamp(...2.1rem) | `--fs-5xl` (clamp 2-3.5rem) | + na desktop |
| **Display brojevi** | 2.4-3.2rem | `--fs-3xl/4xl` | normalizovano |
| **Section headers** | 0.66rem uppercase | `--fs-micro` (11px) + bold | čitljivije |

---

## 🎬 Plan implementacije

### Phase A: Tokeni (1 commit, no-op visual)
- Dodaj CSS custom properties u `:root`
- Bez ikakve primjene još
- Verzija: v2.27.0

### Phase B: Najvažnije korekcije (1 commit)
- Bottom nav (9px → 13px)
- Source note (9.9px → 13px)
- Vakat sub-labels (8.6-9.9px → 12px)
- Verzija: v2.27.1

### Phase C: Header & info (1 commit)
- City sub, weather cond, kalendar godina
- Verzija: v2.27.2

### Phase D: Countdown sub-labels (1 commit)
- Sve sub-labele ispod countdown brojeva
- Verzija: v2.27.3

### Phase E: Mobile overrides cleanup (1 commit)
- @media (max-width: 390px) — uskladiti
- @media (max-width: 360px) — uskladiti
- Verzija: v2.27.4

### Phase F: !important cleanup (opciono, posljednji)
- Postupno uklanjati `!important` kao usko grlo za buduće izmjene
- Verzija: v2.27.5

---

## 🎯 Očekivani efekti

**Za korisnike:**
- Nema više elemenata ispod 12px
- Bottom nav 44% čitljiviji
- Sub-labele uniformne — manje vizuelnog šuma
- Iste informacije ali jasnije

**Za održavanje:**
- 120 različitih veličina → 12 tokena
- Bilo koja promjena u tipografiji = 1 linija promjene
- Lakše buduće dorade

**Šta SE NE mijenja:**
- Vizuelni hijerarhija (naslovi ostaju naslovi)
- Boje
- Spacing
- Layout
- Funkcionalnost
- Namaska logika

---

## ⚠️ Rizici

| Rizik | Mitigacija |
|-------|-----------|
| Tekst predugačak za malu širinu (320px iPhone SE) | Test u Phase B i E |
| Arapski font (Amiri) drugačije skalira | Provjeri u kalendaru i predstojećim |
| Šejhu odobreni tekstovi izgledaju drugačije | Vizuelni screenshot pred commit |
| Lighthouse Accessibility regresija | Re-run nakon svake faze |

---

## 📋 Sljedeći korak

**Ti čitaš ovaj dokument i odlučuješ:**

1. **Skala** — odobravaš predloženu ili tražiš drugačiju (npr. veće osnove 16px umjesto 15px)?
2. **Naming** — `--fs-xs/sm/base/md/lg/xl/2xl/3xl` ili drugi sistem?
3. **Faze** — krećemo od najkritičnijeg (Phase B) ili idemo po redu?
4. **!important** — uključujemo Phase F sada ili kasnije?

**Bez tvoje potvrde — ne dirikam CSS.**
