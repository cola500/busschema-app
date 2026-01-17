# Changelog

Alla ändringar i projektet dokumenteras i denna fil.

Formatet baseras på [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
och projektet följer [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-01-17

### ✨ Tillagt

#### Favoritfunktion
- **Favoritlista** - Spara upp till 5 favorithållplatser för snabb åtkomst
- **Stjärn-knapp** i header för att lägga till/ta bort aktuell hållplats som favorit
- **Visuell feedback** - Fylld stjärna (★) när favorit, tom stjärna (☆) annars
- **Favoritsektion** med gul bakgrund ovanför sökfältet
- **Stora klickbara kort** för varje favorit (70px höjd)
- **Delete-knapp** (🗑️) för att ta bort favoriter
- **Click-to-select** - Klicka på favorit för att snabbt byta hållplats
- **localStorage-persistens** - Favoriter sparas mellan sessioner
- **Smooth scroll** - Automatisk scroll till toppen vid val av favorit

#### Touch-optimering
- **Större touch targets** - Alla klickbara element minst 60x60px (Apple HIG: 44x44px)
- **Förbättrad padding**:
  - Sökresultat: 20px padding, 60px min-höjd (från 12px padding)
  - Favoriter: 20px padding, 70px min-höjd
  - Refresh-knapp: 140x60px (från implicit storlek)
  - Stjärn-knapp: 60x60px rund knapp
- **Touch feedback** - `:active` states på alla interaktiva element
  - Scale-down effekt (0.98) vid touch
  - Bakgrundsfärgsändring för tydlig feedback
- **Större fonter** - 1.2rem på favorit-namn och knappar (från 1rem)
- **Disabled states** - Visuell feedback när knappar inte kan användas

#### UI/UX-förbättringar
- **Header-layout** förbättrad med flex för stjärn-knapp
- **Favorit-sektion styling** - Tydlig gul bakgrund (#fff8e1) med guldkant
- **Smooth transitions** - Alla hover/active states har 0.2s transition
- **Better responsive** - Förbättrade breakpoints för mindre skärmar

### 🐛 Fixat

#### API-struktur inkompatibilitet
- **Sökresultat** - Fixat så frontend läser `result.gid` istället för `result.stopArea.gid`
- **Filter** - Ändrat filter från `result.stopArea` till `result.locationType === 'stoparea'`
- **Avgångstider** - Fixat så frontend läser `dep.estimatedTime` istället för `dep.serviceJourney.estimatedDepartureTime`
- **Problem** - Ursprunglig kod förväntade sig annan API-struktur än vad Västtrafik API v4 returnerar
- **Impact** - Appen visade inga sökresultat eller avgångar innan fix

### 📝 Dokumentation

- **README.md** uppdaterad med nya funktioner och användningsinstruktioner
- **RETROSPECTIVE.md** - Omfattande analys av förbättringsområden
- **CHANGELOG.md** - Denna fil!
- **Användningsinstruktioner** - Ny sektion för favoriter i README

### 🔧 Tekniska förändringar

- **Ny JavaScript-logik** för favorithantering:
  - `getFavorites()` - Hämta favoriter från localStorage
  - `saveFavorites()` - Spara favoriter till localStorage
  - `isFavorite()` - Kontrollera om hållplats är favorit
  - `addToFavorites()` - Lägg till/ta bort favorit
  - `removeFromFavorites()` - Ta bort specifik favorit
  - `updateFavoriteButton()` - Uppdatera stjärn-knappens utseende
  - `renderFavorites()` - Rendera favoritlistan
- **CSS-tillägg**:
  - `.favorites-section` - Favoritsektion styling
  - `.favorite-item` - Favorit-kort styling
  - `.favorite-btn` - Stjärn-knapp styling
  - `.header-content` - Flex-layout för header
  - Förbättrade `:active` states på alla interaktiva element
- **HTML-tillägg**:
  - Favorit-sektion element
  - Stjärn-knapp i header

---

## [1.0.0] - 2026-01-17

### ✨ Initial release

- ✅ Realtidsavgångar från Västtrafik API v4
- ✅ Sök och välj hållplats
- ✅ Automatisk uppdatering var 30:e sekund
- ✅ Färgkodade linjenummer från Västtrafik
- ✅ Visar minuter kvar eller avgångstid
- ✅ Touchvänligt gränssnitt
- ✅ Sparar vald hållplats i localStorage
- ✅ Responsiv design
- ✅ Node.js + Express backend
- ✅ Vanilla JavaScript frontend med Vite
- ✅ OAuth2-autentisering mot Västtrafik
- ✅ Klocka med realtid
- ✅ Smooth animationer (slideIn, pulse)
- ✅ Deployment-instruktioner för Raspberry Pi

---

## Versionshistorik

- **v1.1.0** (2026-01-17) - Favoriter + Touch-optimering
- **v1.0.0** (2026-01-17) - Initial release
