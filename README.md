# Planet Fitness

Umbraco CMS-webbplats för Planet Fitness.

## Teknik

- .NET 10
- Umbraco CMS 18
- uSync 18 (synkning av dokumenttyper, datatyper, mallar m.m. mellan utvecklare och miljöer)

## Så fungerar det

Alla utvecklare har **sin egen lokala databas** (SQLite), som aldrig checkas in. Det som delas via git är **uSync-filerna** i `src/PlanetFitness.Web/uSync/`, som beskriver sajtens struktur: dokumenttyper, datatyper, mallar, språk, mediatyper m.m.

- När du sparar något i backoffice skriver uSync automatiskt ändringen till uSync-filerna.
- När sajten startar läser uSync in uSync-filerna i din databas (`ImportAtStartup: "Settings"`).
- Första gången efter installationen importeras allt automatiskt (`ImportOnFirstBoot: true`).

Inställningarna finns under `uSync` i `src/PlanetFitness.Web/appsettings.json`.

## Kom igång första gången

**Krav:** [.NET 10 SDK](https://dotnet.microsoft.com/download) och git.

1. Klona repot:

   ```bash
   git clone https://github.com/isakpro/planet-fitness.git
   cd planet-fitness
   ```

2. Starta sajten:

   ```bash
   dotnet run --project src/PlanetFitness.Web
   ```

3. Öppna https://localhost:44350 (eller http://localhost:46859) i webbläsaren.
4. Gå igenom Umbracos installationsguide:
   - Fyll i namn, e-post och lösenord för ditt lokala admin-konto.
   - Välj **SQLite** som databas.
5. Logga in i backoffice och kontrollera under **Settings** att dokumenttyperna och datatyperna finns. Saknas de, stoppa sajten (Ctrl+C) och starta den igen så importeras de.
6. Kör `git status`. Om installationen har lagt till `Imaging:HMACSecretKey` i `appsettings.json`, flytta den till `appsettings.Local.json` och återställ `appsettings.json` med `git checkout -- src/PlanetFitness.Web/appsettings.json`. Nyckeln är hemlig och får inte checkas in.

Anslutningssträngen till din databas sparas i `src/PlanetFitness.Web/appsettings.Local.json`. Den filen ignoreras av git och är bara din.

## Arbetsgång varje dag

1. **Hämta senaste ändringarna**

   ```bash
   git pull
   ```

2. **Starta (eller starta om) sajten.** uSync importerar då kollegornas ändringar till din databas.
3. **Gör dina ändringar** i backoffice (t.ex. lägg till ett fält på en dokumenttyp) och i koden (t.ex. vyer i `Views/`).
4. **Checka in uSync-filerna tillsammans med koden** som hör till ändringen:

   ```bash
   git add src/PlanetFitness.Web/uSync src/PlanetFitness.Web/Views
   git commit -m "Lägg till fält för öppettider på Gym"
   git push
   ```

## Regler i teamet

- **Gör alltid pull och starta om sajten innan du ändrar något i backoffice.** Om din databas inte är uppdaterad kan du skriva över en kollegas ändring när du sparar.
- **Säg till innan du ändrar en dokumenttyp eller datatyp** som någon annan också kan jobba med. Konflikter i uSync-filer går att lösa men är krångliga.
- **Checka in uSync-filer och kod i samma commit**, så att en vy aldrig pekar på ett fält som inte finns.
- **Checka aldrig in** `appsettings.Local.json`, databasfiler eller hemliga nycklar. `.gitignore` sköter det mesta, men titta alltid igenom `git status` innan du committar.
- **Innehåll och media synkas inte mellan utvecklare.** Skapa egna testsidor lokalt. Riktigt innehåll skapas i den gemensamma miljön.

## Felsökning

**En kollegas ändring syns inte hos mig**
Starta om sajten. Hjälper inte det, gå till **Settings → uSync** i backoffice och klicka på **Import**.

**Merge-konflikt i en uSync-fil**
Behåll den ena versionen av filen, starta om sajten så att den importeras och gör om den andra ändringen i backoffice. uSync skriver då en korrekt fil som du checkar in.

**Jag vill börja om med en tom databas**
Stoppa sajten, radera `src/PlanetFitness.Web/umbraco/Data/` och `src/PlanetFitness.Web/appsettings.Local.json`, och följ sedan *Kom igång första gången* igen.
