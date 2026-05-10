# Desktop Browser Test — Moj namaz by Divan
**Verzija:** v2.26.65
**Datum:** 10. maj 2026.
**Cilj:** Provjera desktop layouta i kompatibilnosti u 4 browsera.

---

## URL za testiranje

```
https://ajddoo.github.io/nurul-iman-by-divan/
```

Za simulaciju različitih satnica (bez čekanja):
```
https://ajddoo.github.io/nurul-iman-by-divan/?lat=44.54&lng=18.67&city=Tuzla&time=14:00
```

---

## Desktop layout — šta očekivati

Pri širini ≥ 768px:
- Navy gradient pozadina (tamno plava)
- App se prikazuje kao "phone frame" centriran na ekranu (max 440px)
- Zaobljeni uglovi (32px border-radius)
- Duboka sjenka ispod frame-a
- Frame ima vlastitu warm-white pozadinu (ne vidi se navy kroz unutrašnjost)

Pri širini ≥ 1024px:
- Frame malo širi (460px umjesto 440px)
- Visina do 900px

---

## Checklista po browseru

### Chrome (desktop)

| Provjera | Status |
|----------|--------|
| Navy gradient pozadina vidljiva | [ ] |
| Phone frame centriran, zaobljeni uglovi | [ ] |
| Scroll unutar frame-a (ne cijeli prozor) | [ ] |
| Hover efekti na dugmićima rade | [ ] |
| Tab navigacija tipkovnicom (Tab + Enter) | [ ] |
| Focus outline vidljiv na elementima | [ ] |
| Wake lock dugme vidljivo | [ ] |
| Lokacija modal (ručni unos) radi | [ ] |
| Info modal se otvara centriran na ekranu | [ ] |
| Live expand overlay ide na cijeli ekran (ne unutar frame-a) | [ ] |

### Safari (desktop / macOS)

| Provjera | Status |
|----------|--------|
| Navy gradient pozadina vidljiva | [ ] |
| Phone frame korektno prikazan | [ ] |
| `100dvh` fallback na `100vh` radi | [ ] |
| Fontovi (Amiri, arapski tekst) učitani | [ ] |
| Animacije (tab fade, timer pulse) rade | [ ] |

### Firefox (desktop)

| Provjera | Status |
|----------|--------|
| Navy gradient pozadina vidljiva | [ ] |
| Phone frame korektno prikazan | [ ] |
| Hover efekti rade | [ ] |
| Scroll bez grešaka | [ ] |

### Edge (desktop)

| Provjera | Status |
|----------|--------|
| Navy gradient pozadina vidljiva | [ ] |
| Phone frame korektno prikazan | [ ] |
| PWA install prompt se pojavljuje | [ ] |

---

## Specifični edge case testovi

### 1. Prozor 768px–900px (tablet/mali desktop)
- Resize browser na ~800px širinu
- ✅ Phone frame se pojavljuje (navy bg vidljiv)
- ✅ Frame nije preširok, centriran je

### 2. Prozor ispod 768px
- Resize na ~600px
- ✅ Nestaje navy bg, app ide full-width kao mobile

### 3. Live expand (fullscreen timer)
- Klikni na glavni countdown timer → razvuci u full view
- ✅ Overlay ide na CIJELI ekran (ne ostaje unutar phone frame-a)
- ✅ Navy pozadina nestaje iza overlay-a

### 4. Info modal
- Klikni ⓘ dugme
- ✅ Modal centriran na cijeli ekran (ne unutar phone frame-a)

### 5. Lokacija modal
- Klikni na ikonicu lokacije
- ✅ Modal centriran, search radi
- ✅ Klikom izvan modal-a zatvori

---

## ✅ Status

| Browser | Layout | Funkcionalnost | Animacije |
|---------|--------|----------------|-----------|
| Chrome | [ ] | [ ] | [ ] |
| Safari | [ ] | [ ] | [ ] |
| Firefox | [ ] | [ ] | [ ] |
| Edge | [ ] | [ ] | [ ] |
