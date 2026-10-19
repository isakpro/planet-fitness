# Planet Fitness

Umbraco CMS-webbplats för Planet Fitness.

## Teknik

- .NET 10
- Umbraco CMS 18
- uSync 18 (synkning av inställningar och innehåll mellan miljöer)

## Kom igång

```bash
dotnet run --project src/PlanetFitness.Web
```

Första gången du startar sajten visas Umbracos installationsguide där du skapar ett administratörskonto och väljer databas.

## uSync

uSync sparar dokumenttyper, datatyper, mallar m.m. som filer i `src/PlanetFitness.Web/uSync/`. Checka in de filerna så att ändringarna följer med till andra miljöer.
