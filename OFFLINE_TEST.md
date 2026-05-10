# Offline Mode Test — Moj namaz by Divan
**Verzija:** v2.26.65
**Datum:** 10. maj 2026.
**Cilj:** Provjera da app radi bez interneta nakon prvog uspješnog load-a.

---

## Service Worker strategija (sw.js)

| Tip resursa | Strategija | Ponašanje offline |
|-------------|------------|-------------------|
| HTML, manifest, ikone | Network-first → keš fallback | ✅ Radi iz keša |
| Fontovi, ostali statički | Cache-first | ✅ Radi iz keša |
| API pozivi (vaktija.ba, open-meteo, nominatim) | Network-only | ❌ Failuju → app-level fallback |

**Vakti se računaju lokalno** (`calcPrayerTimes()`) — ne trebaju API.

---

## Preduvjet

App mora biti posjećena bar jednom dok je online — Service Worker treba keširat shell.

---

## Test procedure

### Test 1 — Osnovno offline (Chrome DevTools)

1. Otvori app, pričekaj da se učita potpuno
2. F12 → Network tab → checkmark **"Offline"**
3. Reload (F5)
4. Provjeri:

| Provjera | Očekivano | Status |
|----------|-----------|--------|
| App se učitava (ne bijeli ekran) | ✅ index.html iz SW keša | [ ] |
| Offline banner prikazan | ✅ "⚠ Offline · zadnji online: [grad] · prije Xh" | [ ] |
| Status dot promjenjen (crvena) | ✅ Da | [ ] |
| Vakti prikazani | ✅ Lokalni proračun radi | [ ] |
| Grad prikazan | ✅ Iz localStorage keša | [ ] |
| Weather kartica | ✅ Iz keša ili "—" | [ ] |
| Countdown timer teče | ✅ Da | [ ] |
| Lokacija modal radi | ✅ Da (lokalna baza gradova) | [ ] |

### Test 2 — Offline od starta

1. F12 → Network → "Offline"
2. **Zatim** otvori app (novi tab ili hard reload Ctrl+Shift+R)
3. Provjeri:

| Provjera | Očekivano | Status |
|----------|-----------|--------|
| App se učitava | ✅ Ako je prethodno keširano | [ ] |
| GPS radi | ✅ GPS ne treba internet | [ ] |

> ⚠️ Ako je potpuno prvi put i nikad nije bilo online — SW nema keš, app se ne učitava. Ovo je normalno PWA ograničenje.

### Test 3 — Offline → online prelaz

1. Idi offline (DevTools → Offline)
2. Pričekaj 1 minutu (banner pokazuje "prije 1min")
3. Vrati internet (ukloni Offline checkmark)
4. Provjeri:

| Provjera | Očekivano | Status |
|----------|-----------|--------|
| Offline banner nestaje | ✅ Automatski | [ ] |
| Status dot zelena ponovo | ✅ Da | [ ] |
| Vakti se refreshaju sa API | ✅ Da | [ ] |

### Test 4 — Mobilni uređaj (stvarni offline)

1. Isključi WiFi i mobilni podatak
2. Otvori app
3. Provjeri iste stavke kao Test 1

| Provjera | Status |
|----------|--------|
| App se učitava | [ ] |
| Offline banner | [ ] |
| Vakti rade | [ ] |
| GPS radi | [ ] |

---

## ⚠️ Poznata ograničenja

| Ograničenje | Razlog | Mitigacija |
|-------------|--------|------------|
| Vakti.ba ezan → UK fallback offline | API nije keširan (by design) | Fallback implementiran |
| Weather offline → keš ili "—" | open-meteo nije keširan | Keš pri svakom uspješnom pozivu |
| Grad offline → localStorage keš | Nominatim nije keširan | Cache pri svakom GPS uspjehu |
| Prvi put offline → ne učitava | SW keš prazan | Inherentno PWA ograničenje |

---

## ✅ Sažetak

**App je funkcionalna offline** u svim realnim scenarijima (korisnik koji je jednom bio online).
Jedino ograničenje je prvi load bez interneta — standardno za sve PWA.
