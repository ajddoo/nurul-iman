# Šejh review — Moj namaz by Divan
**Verzija:** v2.26.60
**Datum:** 10. maj 2026.
**Cilj:** Provjera islamskih pravila pred launch.

---

## 1. Sabah / Fajr

### Logika
- **Van Saudije i van BiH/Sandžaka:** ISNA standard, **-15° depression**
- **Saudija:** Ummul-kura **-18.5°** (kao kraj sehura)
- **BiH/Sandžak:** ISNA **-15°** se prikazuje kao "Sabah", **vaktija.ba zora** se koristi za sehur countdown

### Konkretno
| Lokacija | Sabah prikaz | Sehur kraj |
|----------|--------------|------------|
| Tuzla | ISNA 03:48 | vaktija.ba zora (npr. 03:42) |
| Mekka | UK 04:20 | UK 04:20 (isti vakat) |
| Berlin | ISNA 03:30 | UK -18.5° |
| Stockholm (ljeti) | ⚠ "--:--" + warning | ⚠ "--:--" + warning |

### ❓ Pitanja za šejha
1. Da li je ispravno da Bosanci vide ISNA sabah (15°) kao primarni, a vaktija.ba zora (lokalna) kao kraj sehura?
2. Da li 18.5° za Saudiju (Ummul-kura) odgovara šerijatskom standardu Hidžaza?
3. Polovi (Stockholm ljeti) — prihvatljivo da se prikaže warning bez vakata?

---

## 2. Akšam

### Logika
- **Univerzalno:** Akšam = sunset + **1 minuta** (ihtiyat)
- Po naredbi šejha UG Divan
- Importantno: aplikacija **uvijek dodaje 1 minutu**, ne mijenja se po regiji

### ❓ Pitanja
1. Da li je 1 minuta dovoljno za sve regije ili treba više za visoke geografske širine (gdje sunce zalazi pod manjim uglom, sumrak duži)?

---

## 3. Sehur

### Logika po regiji

| Region | Kraj sehura | Source |
|--------|-------------|--------|
| **BiH/Sandžak** sa vaktija.ba API | `vaktija.ba zora` (npr. 03:42) | Lokalno zvanično |
| **BiH/Sandžak** ako API ne radi | UK -18.5° (npr. 03:35) | Najstroži fallback |
| **Saudija** | UK -18.5° = isto što i UK sabah | Ummul-kura |
| **Ostali svijet** | UK -18.5° | Univerzalno najstroži |

### Sehur countdown UI
- Glavni timer: "do kraja sehura"
- Sub tekst:
  - BiH sa API: "lokalno"
  - Ostali: "Ummul-kura"
- 3-minutna nula faza nakon isteka

### ❓ Pitanja
1. Pravilo "BiH = lokalna vaktija primary, UK fallback ako API ne radi" — odobreno?
2. Saudija Sabah = sehur kraj jer su oba -18.5°? Da li to vodi do problema da se ne stigne do faze "između sehura i sabaha"?

---

## 4. Polovina noći i posljednja 1/3 noći

### Logika
- **Polovina noći** = (akšam + sabah_sledeći_dan) / 2
- **Posljednja 1/3** = akšam + (sabah_sledeći - akšam) × 2/3

### Po regiji
| Region | Sabah za izračun |
|--------|------------------|
| Saudija (Hejaz) | UK sabah (-18.5°) |
| Svi ostali (uključujući BiH) | ISNA sabah (-15°) |

### Highlight period
- Polovina noći se "aktivira" (kartica goldna) od polaNoci do nightEnd
- nightEnd = ISNA sabah (non-Hejaz) ili UK sabah (Hejaz)

### ❓ Pitanja
1. Pravilo "polovina noći do ISNA sabaha za sve osim Saudije" — ispravno?
2. Da li je trebalo koristiti UK sabah za polovinu noći u BiH (jer je vaktija.ba zora bliža UK)?

---

## 5. Ikindija (Asr)

### Logika
- **Univerzalno:** ISNA standard
- ISNA Asr odgovara vaktija.ba Asr za BiH — nema razlike u praksi

---

## 6. Duha

### Logika
- **Početak duhe** = izlazak sunca + 15 min
- **Kraj duhe** = podne - 15 min
- Univerzalno za sve regije

### Zabranjena vremena
1. **Faza 4** (izlazak → duha): 15 min — zabranjeno za namaz
2. **Faza 6** (kraj duhe → podne): 15 min — zabranjeno (sunce u zenitu)

### ❓ Pitanja
1. 15 min nakon izlaska — dovoljno?
2. 15 min prije podneva — dovoljno za "sunce u zenitu" period?
3. **Treća zabrana** (poslije ikindije do akšama) — namjerno NE prikazujemo. Prihvatljivo?

---

## 7. Akšam +1 ihtiyat

### Pravilo
Akšam vrijeme prikazano u app = matematički zalazak sunca **+ 1 minuta**.
Po izričitoj naredbi šejha UG Divan.

### Implementacija
```javascript
const aksam = aksamBase + 1/60;  // +1 minuta
```

### ❓ Pitanja
1. Potvrda da je 1 minuta tačan ihtiyat?
2. Da li ima razlika između regija (ravnica vs planina, blizu mora vs unutrašnjost)?

---

## 8. Predstojeći islamski dani

### Lista (samo autentično potvrđeni dani)
1. **Prvih deset dana Zul-hidžže** (1.–9. Zul-hidžže)
2. **Dan Arafata** (9. Zul-hidžže) — post sunet
3. **Kurban Bajram** (10. Zul-hidžže) — post zabranjen
4. **Bijeli dani** (13.–15. svakog hidžretskog mjeseca) — post sunet
5. **Dan uoči Ašure i Ašura** (9.–10. Muharrema) — post sunet
6. **Početak ramazana** (1. Ramazan) — farz post
7. **Ramazanski Bajram** (1. Ševval) — post zabranjen
8. **Šest dana ševvala** (od 2. Ševvala) — post sunet

### Izuzeti (NE prikazujemo)
- Mevlud (rođenje Poslanika, sallallahu alejhi ve sellem) — bid'a po nekim učenjacima
- Mi'radž (27. Redžeb) — slabi izvori
- Berat-kandil (15. Šaban) — slabi izvori

### ❓ Pitanja
1. Lista predstojećih — autentično dovoljno za prikaz korisniku?
2. Treba li dodati nešto što smo izostavili?
3. Treba li ukloniti nešto što smo uključili?

---

## 9. Ručni unos lokacije

### Pokrivene države (~280 gradova)
BiH, Sandžak, Saudija, Njemačka, Austrija, Slovenija, Švicarska, Nizozemska, Belgija, Švedska, Norveška, Danska, UK, Francuska, Italija, Hrvatska, Turska, SAD, Kanada, Australija.

### ❓ Pitanja
1. Treba dodati neke države? (Egipat, Maroko, Bangladeš, Indonezija, Malezija, Egipat, Pakistan, Indija?)
2. Treba ukloniti neke?

---

## 10. Tekstovi i terminologija

### Lista termina koji se koriste
- **Vakat** / **vakti** / **vakte** (umjesto "vakute")
- **Sabah** (ne "Fajr")
- **Akšam · iftar** (kombinacija)
- **Ikindija** (ne "Asr")
- **Jacija** (ne "Isha")
- **Kraj sehura**
- **Polovina noći**
- **Posljednja 1/3 noći**
- **Duha-namaz**
- **Sabah-namaz**

### ❓ Pitanja
1. Sva terminologija — bosanski ispravan?
2. "Posljednja 1/3 noći" → bolje "Zadnja trećina noći"?
3. "Duha-namaz" sa crticom — ispravno?

---

## 11. Faze countdown-a (13 faza dnevno)

| # | Period | Glavni timer | Sub timer | Status |
|---|--------|--------------|-----------|--------|
| 1 | Polovina noći → kraj sehura | do kraja sehura · lokalno/UK | do sabaha · ISNA | — |
| 2 | Kraj sehura → sabah | do sabaha · ISNA | — | — |
| 2b | (Saudija) UK sabah → ISNA sabah | do sabaha · ISNA | do izlaska sunca | "Nastupio sabah po Ummul-kura" |
| 3 | Sabah → izlazak sunca | do izlaska sunca · kraj sabah-namaza | do duha-namaza | — |
| 4 | Izlazak → duha (zabranjeno) | do duha-namaza | do podne | **Zabranjeno vrijeme za namaz** |
| 5 | Duha → kraj duhe | do isteka duha-namaza | do podne | — |
| 6 | Kraj duhe → podne (zabranjeno) | do podne · sunce u zenitu | — | **Zabranjeno vrijeme za namaz** |
| 7 | Podne ISNA → lokalni podne (BiH) | do ezana u džamiji · lokalno podne | do ikindije · ISNA | — |
| 8 | Podne → ikindija | do ikindije · ISNA | — | — |
| 9 | Ikindija → akšam | do akšama · ISNA · iftar | do ezana u džamiji · akšam · lokalno (BiH) | — |
| 10 | Akšam → lokalni akšam (BiH) | do ezana u džamiji · akšam · lokalno | do jacije · ISNA | — |
| 11 | Akšam → jacija ISNA | do jacije · ISNA | do jacije u džamiji · lokalna (BiH) | — |
| 12 | Jacija ISNA → lokalna jacija (BiH) | do jacije u džamiji · lokalni ezan | do polovine noći · kraj jacije | — |
| 13 | Lokalna jacija → polovina noći | do polovine noći · kraj jacije | do sabaha · sutra · ISNA | — |

### ❓ Pitanja
1. Sve 13 faza logički ispravne?
2. Tekst svake faze — odgovara islamskom kontekstu?

---

## 12. Saudijska Arabija (Hejaz) specifičnosti

- Sve granice države (ne samo Mekka/Medina)
- Sabah po Ummul-kura (-18.5°) primarni
- Sabah po ISNA (-15°) prikazan kao informacija (manji vakat, ranije)
- Period između UK sabah i ISNA sabah: "Nastupio sabah po Ummul-kura"
- Polovina noći i posljednja 1/3 računaju se sa UK sabahom (najstroži)

### ❓ Pitanja
1. Da li je dvostruki sabah prikaz (UK + ISNA) zbunjujući za korisnika?
2. Treba li samo UK prikaz, ili da se zadrži dual za informativne svrhe?

---

## 13. Tehničke garancije

- Vakti se računaju **lokalno na uređaju** (matematika), ne traži internet
- vaktija.ba se zove samo za BiH/Sandžak korisnike sa vaktija.ba pokrivenošću
- Ako vaktija.ba ne radi, fallback na Ummul-kura (najstroži)
- Cache zadnji uspješan grad/weather za offline upotrebu
- GPS zaštita — `enableHighAccuracy:true` za tačan grad pri prvom load-u

---

## 14. Zaključak

**Šejh, molim provjeru sledećih kritičnih tačaka:**

1. ✅/❌ Sabah/zora po regiji (ISNA -15° vs UK -18.5°)
2. ✅/❌ Sehur logika (BiH lokalno, ostali UK)
3. ✅/❌ Akšam +1 minuta univerzalno
4. ✅/❌ Asr ISNA za Bosance (umjesto Hanefijskog)
5. ✅/❌ Polovina noći / Posljednja 1/3 do ISNA sabaha
6. ✅/❌ Lista predstojećih islamskih dana
7. ✅/❌ Terminologija na bosanskom jeziku
8. ✅/❌ 13 countdown faza
9. ✅/❌ Predloženi gradovi za ručni unos
10. ✅/❌ Saudija Hejaz režim (dual sabah prikaz)

**Po tačkama gdje šejh kaže "treba ispraviti" — vraćamo se na razgovor i implementiramo izmjene prije launch-a.**

---

## Kontakt
Ajdin — UG Divan Tuzla
