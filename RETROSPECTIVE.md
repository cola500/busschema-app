# 🔍 Retrospektiv Analys - Busschema-app

**Datum**: 2026-01-17
**Version**: 1.0.0 → 1.1.0 (planerad)

## Sammanfattning

Detta är en retrospektiv analys av busschema-appen efter den första användningen. Appen fungerar grundläggande bra men har flera förbättringsområden, särskilt för att fungera optimalt på en Raspberry Pi med pekskärm.

---

## ✅ Vad fungerar bra

### 1. **Enkel arkitektur**
- Vanilla JavaScript - inga onödiga dependencies
- Tydlig separation mellan frontend och backend
- Lätt att förstå och underhålla

### 2. **Grundläggande funktionalitet**
- API-integration med Västtrafik fungerar
- Realtidsdata visas korrekt
- Auto-refresh implementerad
- LocalStorage sparar senaste hållplatsen

### 3. **Design**
- Snyggt färgschema
- Responsiv layout
- Trevliga animationer (slideIn, pulse)
- Färgkodade linjenummer från Västtrafik

---

## 🔴 Kritiska problem (måste fixas)

### 1. **API-struktur inkompatibilitet** ✅ FIXAT
**Problem**: Frontend förväntade sig `result.stopArea.gid` men API returnerar `result.gid`
**Status**: ✅ Fixat i första session
**Lärdom**: Alltid testa mot faktisk API innan deployment

### 2. **Hardkodad API URL fungerar inte i produktion**
**Problem**: `const API_URL = 'http://localhost:3001/api'` (rad 1 i main.js)
**Impact**: 🔴 Kritisk - Appen fungerar inte på Raspberry Pi
**Lösning**:
```javascript
const API_URL = window.location.hostname === 'localhost'
  ? 'http://localhost:3001/api'
  : '/api';
```

### 3. **Ingen felhantering för nätverksavbrott**
**Problem**: Om WiFi försvinner så stannar appen bara, ingen feedback
**Impact**: 🔴 Kritisk för Raspberry Pi-användning
**Lösning**:
- Visa tydligt felmeddelande vid nätverksfel
- Försök igen automatiskt (exponential backoff)
- Visa senast uppdaterad tid tydligare

### 4. **Token expiry hanteras inte korrekt**
**Problem**: Om access token går ut (typ efter 1h) så fortsätter appen anropa API utan att förnya token
**Impact**: 🔴 Kritisk - Appen slutar fungera efter någon timme
**Lösning**: Backend bör automatiskt förnya token vid 401-fel

---

## 🟡 Viktiga förbättringar

### 5. **Touch-optimering saknas**
**Problem**: Knappar/sök-resultat är för små för pekskärm
**Impact**: 🟡 Viktigt - Svårt att klicka rätt
**Lösning**:
- Minst 44x44px touch targets (Apple HIG standard)
- Större padding på klickbara element
- Touch feedback (visual response på touch)
- Större sökfält

**Rekommendation**:
```css
.search-result-item {
  padding: 20px; /* Från 12px */
  min-height: 60px;
}

.refresh-btn {
  min-width: 120px;
  min-height: 60px; /* Från implicit height */
  font-size: 1.2rem;
}

/* Touch feedback */
.search-result-item:active,
.refresh-btn:active {
  background: #dee2e6;
  transform: scale(0.98);
}
```

### 6. **Ingen favoritfunktion**
**Problem**: Användaren måste söka samma hållplatser varje gång
**Impact**: 🟡 Viktigt - Dålig användarupplevelse
**Lösning**: Implementera favoritlista med stor, klickbar lista

### 7. **Ingen visual feedback vid refresh**
**Problem**: När "🔄 Uppdatera" klickas ser man inte att något händer
**Impact**: 🟡 Viktigt - Känns som appen hängt sig
**Lösning**:
- Rotera refresh-ikonen under uppdatering
- Disable button under loading
- Visa spinner eller progress

### 8. **Ingen offline-funktionalitet**
**Problem**: Service worker saknas, ingen PWA-funktionalitet
**Impact**: 🟡 Viktigt - Kan inte installeras som app
**Lösning**: Implementera service worker + manifest.json

### 9. **Auto-refresh kan störa användaren**
**Problem**: Om användaren scrollar så hoppar sidan tillbaka upp vid refresh
**Impact**: 🟡 Viktigt - Irriterande UX
**Lösning**:
- Pausa auto-refresh vid scroll
- Smooth update utan att DOM:en hoppar

---

## 🟢 Nice-to-have förbättringar

### 10. **Ingen loading state för sökningar**
**Problem**: När användaren söker ser man inte om något händer
**Lösning**: Visa spinner i sökresultaten under sökning

### 11. **Rate limiting saknas**
**Problem**: Debounce finns (300ms) men ingen rate limiting
**Impact**: 🟢 Låg - Men kan belasta API vid spam
**Lösning**: Begränsa till max 5 sökningar per 10 sekunder

### 12. **Ingen dark mode**
**Problem**: Starkt ljus från skärm på natten
**Lösning**: Auto dark mode baserat på tid (18:00-06:00)

### 13. **Accessibility saknas**
**Problem**: Ingen keyboard navigation, ingen ARIA-labels
**Lösning**:
- ARIA labels på alla interaktiva element
- Tab-navigation för sök-resultat
- Focus styles

### 14. **Ingen caching av avgångar**
**Problem**: Onödiga API-anrop om man växlar mellan samma hållplatser
**Lösning**: Cache i 30 sekunder med timestamp

### 15. **Mobilresponsivitet kan förbättras**
**Problem**: På små skärmar bryts avgångskort inte optimalt
**Nuvarande**: Tid går till ny rad på <600px
**Bättre**: Flexigare breakpoints, större text på mobil

---

## 📊 Teknisk skuld

### Backend

1. **Ingen error logging**: Console.error men ingen persistent logging
2. **Ingen monitoring**: Vet inte om appen kraschar på Raspberry Pi
3. **Ingen health check automation**: `/health` endpoint finns men används inte
4. **Ingen HTTPS**: Kör HTTP lokalt (okej för Raspberry Pi, men dokumentera)
5. **Ingen input validation**: Backend litar blint på query params

### Frontend

1. **Ingen TypeScript**: Skulle ge bättre type safety
2. **Ingen bundling optimization**: Vite används men ingen tree-shaking config
3. **Ingen lazy loading**: Laddar allt direkt (okej för liten app)
4. **Magic numbers**: Timeouts, limits hårdkodade (30000, 300, etc)
5. **Ingen state management**: Allt är globala variabler

---

## 🎯 Prioriterad förbättringsplan

### Sprint 1: Kritiska fixes (måste göras innan Raspberry Pi-deployment)
1. ✅ Fixa API-struktur (KLART)
2. Fixa production API URL
3. Implementera bättre error handling + network recovery
4. Fixa token renewal-logik

### Sprint 2: Pekskärmsanpassning
1. **Implementera favoritfunktion** ⭐ (Användaren vill ha detta nu)
2. Förbättra touch targets (större knappar)
3. Lägg till touch feedback
4. Testa på faktisk pekskärm

### Sprint 3: Stability & UX
1. Implementera PWA (service worker + manifest)
2. Visual feedback vid refresh
3. Förbättra auto-refresh (pausa vid scroll)
4. Dark mode

### Sprint 4: Polish
1. Accessibility improvements
2. Better loading states
3. Rate limiting
4. Caching

---

## 🔑 Tekniska lösningar

### Favoritfunktion (implementeras härnäst)

**Datastruktur**:
```javascript
// localStorage: 'favorites'
[
  { gid: "9021014005699000", name: "Betaniagatan, Göteborg", addedAt: "2026-01-17T08:00:00" },
  { gid: "9021014001960000", name: "Centralstationen, Göteborg", addedAt: "2026-01-17T07:00:00" }
]
```

**UI-komponenter**:
- Favorit-sektion ovanför sökfältet
- Stjärn-ikon för att lägga till/ta bort favoriter
- Stora klickbara kort (minst 60px höjd)
- Swipe-to-delete på touchskärmar
- Max 5 favoriter för att hålla UI clean

**Touch-optimering**:
```css
.favorite-item {
  min-height: 70px;
  padding: 20px;
  font-size: 1.2rem;
  /* Större text för läsbarhet på pekskärm */
}

.favorite-item:active {
  background: #e9ecef;
  transform: scale(0.98);
  /* Visuell feedback vid touch */
}
```

---

## 💡 Lärdommar för framtida projekt

### 1. **Testa alltid mot faktisk API först**
- API-dokumentation kan vara inaktuell
- Gör test-anrop innan du skriver frontend-kod
- Dokumentera faktisk API-struktur i kommentarer

### 2. **Designa för touch från start**
- 44x44px minimum för alla klickbara element
- Tydlig visuell feedback på interaktion
- Testa på faktisk device, inte bara i browser dev tools

### 3. **Bygg för offline från dag 1**
- Service workers är inte svåra att lägga till senare, men bättre från start
- Tänk på vad som händer vid nätverksavbrott
- Cache smart, men inte för aggressivt

### 4. **Production-konfiguration är viktigt**
- Hårdkodade localhost-URLer fungerar inte i produktion
- Använd environment variables eller dynamic detection
- Testa produktionsbygget lokalt innan deployment

### 5. **Error handling är inte optional**
- Särskilt för "always-on" devices som Raspberry Pi
- Visa alltid vad som händer till användaren
- Log errors för debugging

---

## 📈 Metrics att följa

När appen deployats på Raspberry Pi, följ:

1. **Uptime**: Hur länge kör appen utan restart?
2. **API-fel**: Hur ofta misslyckas Västtrafik-anrop?
3. **Token renewals**: Fungerar automatisk token-förnyelse?
4. **Användarinteraktion**: Hur ofta används refresh vs auto-refresh?
5. **Favoriter**: Hur många favoriter använder folk?

---

## ✨ Vision för v2.0

- **Multi-hållplats stöd**: Visa 2-3 hållplatser samtidigt
- **Smart scheduling**: Lär sig användarens mönster (morgon = hållplats A, kväll = hållplats B)
- **Push notifications**: När favorit-buss är 5 min bort
- **Störningsvarningar**: Visa trafikinfo från Västtrafik
- **Reseplanering**: Integration med routing API
- **Statistik**: "Du har tagit buss 18 mest den här månaden"

---

**Författare**: Claude + Johan
**Nästa steg**: Implementera favoritfunktion med pekskärmsanpassning
