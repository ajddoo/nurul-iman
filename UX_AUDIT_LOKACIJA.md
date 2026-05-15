# UX Audit — Selektor lokacije
**Verzija:** v2.26.69 (audit pre-implementation)
**Datum:** 11. maj 2026.
**Pitanje koje audit istražuje:** Da li korisnik prepoznaje da može sam mijenjati grad iz gornjeg dijela aplikacije?

---

## 📋 Trenutno stanje

### Lokacija u UI-u
Gornja lijeva kartica (`.danas-weather-card`) sadrži dva podelementa:
1. **`.danas-place-wrap`** — klikabilan wrapper oko grada (linija 3647)
2. **`.danas-city`** — ime grada (npr. "Tuzla")
3. **`.danas-loc-source`** — sub linija sa državom i potencijalno "Ručno" chipom

### Trenutni vizuelni signali (kronološki implementirani)

| Verzija | Šta je dodato | Status |
|---------|--------------|--------|
| v2.26.46 | Modal otvara klikom na grad | ✅ Funkcija postoji |
| v2.26.47 | Strelica "›" pored grada + dashed border | Implementirano |
| v2.26.48 | **SKINUTA strelica**, ostala samo dashed border | Trenutno stanje |

### Šta sada VIDI korisnik

```
┌───────────────────────────────────┐
│  Tuzla                            │  ← ime grada (1.05rem, normal)
│  ‾‾‾‾‾                            │  ← dashed underline (RGBA 25% opacity)
│  Bosna i Hercegovina              │  ← sub tekst (0.68rem, gray)
└───────────────────────────────────┘
```

Ili sa manualnom lokacijom:

```
┌───────────────────────────────────┐
│  Tuzla                            │
│  ‾‾‾‾‾                            │
│  Bosna i Hercegovina · [Ručno] X │  ← gold pill chip + reset X
└───────────────────────────────────┘
```

---

## 🚨 Identifikovani problemi

### Problem 1 — Dashed border je premokao signal
**Trenutno:** `border-bottom: 1px dashed rgba(var(--warm-ivory-rgb), 0.25)`

- **25% opacity** = jedva vidljivo
- 1px = teško primijetiti na high-DPI ekranima
- Konvencija "dashed underline = klikabilan link" **nije univerzalna** u mobilnim app-ama
- Mnogi korisnici razumiju to kao stilski element, ne kao afordans (mogućnost klika)

**Procjena vidljivosti:** 🔴 Niska (3/10)

---

### Problem 2 — Prvi-put korisnici nemaju "Ručno" chip
- "Ručno" gold pill je **uvijek vidljiv kao indikator** kad je manual mod aktivan
- Ali za korisnika koji **prvi put** otvara app — chip ne postoji
- Onaj koji nikad nije ručno mijenjao = nikad ne vidi chip = ne zna ni da postoji ručna opcija

**Procjena:** 🔴 First-time UX problem

---

### Problem 3 — Nema eksplicitne pozive na akciju
- Nema microcopy: "Tapni za promjenu"
- Nema ikone (caret ⌄, pencil ✎, ili sličan vizuelni signal)
- v2.26.48 namjerno **uklonio** strelicu jer je djelovala vizuelno bučno
- **Rezultat:** elegantan UI, slab afordans

---

### Problem 4 — Nema onboarding hint
- Prvi put app pokazuje grad iz GPS-a
- Ne postoji način da korisnik shvati da to može mijenjati
- Nema first-run tooltip/hint
- Nema "swipe up" ili "tap here" guide

---

## 📊 Industrijski reference (mobilni app-ovi za vrijeme/lokaciju)

| App | Vizuelni signal za promjenu lokacije |
|-----|-----------------------------------|
| **Apple Weather** | Posebno dugme za listu lokacija (bottom right) |
| **Google Maps** | Search bar gore, mijenjanje preko search-a |
| **Yr.no** | Strelica/caret + grad u top bar |
| **Citymapper** | "..." menu ili eksplicitno dugme |
| **MuslimPro** | Grad na vrhu + GPS ikona pored za refresh |

**Zaključak:** Većina koristi **eksplicitne** signale (ikona ili dugme), ne suptilne.

---

## 💡 Predložene opcije (4 varijante)

### Opcija A — Minimalan upgrade: jača dashed border + microcopy
**Promjena:**
- Border-bottom: 1px dashed → **2px solid** akcentne boje sa 40% opacity
- Dodaj microcopy ispod grada: "Tapni za promjenu" (samo kad nije manual)
- Microcopy nestaje nakon prve interakcije ili nakon 10 sekundi

**Rizik:** Nizak. Suptilan upgrade.
**Učinak:** Srednji. Microcopy je eksplicitan, ali se nestaje pa ne smeta.

```
Tuzla
═════
Bosna i Hercegovina
Tapni za promjenu        ← samo prvi put
```

---

### Opcija B — Eksplicitna ikona caret
**Promjena:**
- Dodaj caret ⌄ ili "..." ikonu desno od imena grada
- Diskretan (akcentna boja, mala veličina)
- Ikona pokazuje "ovo otvara meni"

**Rizik:** Srednji. Vraća se odluka iz v2.26.48 (skinuta je strelica).
**Učinak:** Visok. Univerzalni signal.

```
Tuzla ⌄
Bosna i Hercegovina
```

---

### Opcija C — GPS dugme kao "razmijenjeni" element + tap city = modal
**Promjena:**
- Trenutno GPS dugme je posebno
- Učini ga vizuelno **integrisanijim** sa gradom — par GPS+City sa jasnim signalom "ovo je interaktivno"
- Možda hair-line border oko cijele grupacije + suptilan accent
- Klik bilo gdje na toj grupaciji = modal

**Rizik:** Viši (redesign mini sekcije).
**Učinak:** Najveći ali zahtjeva vizuelni rad.

```
┌─────────────────────────┐
│ 📍 Tuzla              │  ← cijela grupa s ivicom
│    Bosna i Hercegovina  │
└─────────────────────────┘
```

---

### Opcija D — First-run tooltip + zadržati current dizajn
**Promjena:**
- Zadržati trenutni minimalan dizajn (dashed border)
- Pri prvom load-u app-e (provjera u localStorage), pokazati tooltip "Tapni grad za promjenu" sa strelicom
- Tooltip nestaje nakon 5s ili nakon prve interakcije
- Nikad se više ne pojavljuje

**Rizik:** Srednji (treba implementirati tooltip komponentu).
**Učinak:** Visok za nove korisnike, 0 za postojeće.

---

## 🎯 Moja preporuka

**Opcija A + Opcija D kombinacija.**

### Razlog
- **Opcija A** poboljšava postojeći signal za **sve korisnike** (jača dashed border + microcopy hint)
- **Opcija D** dodaje **first-run** tooltip koji eksplicitno educira nove korisnike
- Kombinacija pokriva i postojeće (jača vizuelnost) i nove (eksplicitan hint) korisnike
- **Bez vraćanja strelice** (čuva v2.26.48 estetsku odluku)

### Implementacija (2 mala commit-a)

**Commit 1: Microcopy + jača border (v2.27.6 ili kasnije)**
```css
.danas-place-wrap .danas-city {
  border-bottom: 1.5px dashed rgba(var(--accent-rgb), 0.45);
}
.danas-place-hint {
  font-size: var(--fs-xs);
  color: var(--warm-gray);
  opacity: 0.7;
  margin-top: 2px;
  font-style: italic;
}
```

```html
<div class="danas-place-wrap">
  <span class="danas-city">Tuzla</span>
  <div class="danas-place-hint" id="placeHint">Tapni za promjenu</div>
</div>
```

```js
// Sakrij hint nakon prve interakcije ili nakon 8s
setTimeout(() => document.getElementById('placeHint')?.classList.add('hidden'), 8000);
```

**Commit 2: First-run onboarding (v2.27.7, opciono)**
- Tooltip "👆 Tapni grad za promjenu" sa strelicom
- Provjera localStorage `mn-first-run-hint-seen`
- Nestaje nakon klika ili 5 sekundi

---

## ⚠️ Šta NE bih dirao

- Modal sam po sebi (radi dobro)
- Search funkcionalnost
- Hierarhiju URL → manual → GPS → cache
- "Ručno" chip kad je manual aktivan
- GPS dugme stil

---

## 📋 Sljedeći korak

**Ti čitaš ovaj dokument i biraš:**

1. **Opcija A** sama — minimalan upgrade
2. **Opcija B** — eksplicitan caret (vraćanje strelice)
3. **Opcija C** — redesign mini sekcije
4. **Opcija D** sama — first-run tooltip
5. **A + D** (moja preporuka)
6. **Druga kombinacija** (ti opisuješ)

Ili kažeš:
- "Ostavi kao jest, problem je samo font" (ako misliš da je trenutni signal dovoljan kad fontovi budu veći)

**Bez tvoje odluke — ne dirikam UI.**
