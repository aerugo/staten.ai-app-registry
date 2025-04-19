# Staten App Registry

Detta repository är det officiella app-registret för [Staten](https://github.com/statenistes/staten), en öppen källkod GUI-klient för [Model Context Protocol (MCP)](https://modelcontextprotocol.io/introduction). Registret innehåller en utvald lista över MCP-applikationer som kan integreras med Staten för att utöka dess funktionalitet.

Staten.ai är en fork av [Fleur](https://www.fleurmcp.com/).
Detta repository är inte kopplat till Fleur eller dess skapare.

## Vad är MCP?

Model Context Protocol (MCP) är ett standardiserat sätt för språkmodeller att interagera med externa verktyg och datakällor. MCP gör det möjligt för modeller att:

- Komma åt realtidsinformation
- Interagera med webbtjänster
- Hantera filer och data
- Ansluta till olika API:er och plattformar

## Bidra med din MCP

Vi välkomnar bidrag från utvecklare som vill lägga till sina MCP-applikationer i registret. För att skicka in din MCP-server, skapa en pull request enligt riktlinjerna nedan.

Alla inskickade MCP:er kommer att granskas av Staten-teamet innan de läggs till i registret. Vi tillåter endast MCP:er som uppfyller följande kriterier:
- **Integration med API från offentlig sektor eller civilsamhälle**: Din MCP-server ska erbjuda integration med API:er från offentlig sektor eller civilsamhälle.
- **Skickar inte data någonstans**: Det går att skicka data med MCP-servar, men de MCPs som erbjuds genom Staten.ai får inte skicka data någonstans, förutom det som krävs för att göre anrop till officiella APIer för att svara på användarens frågor. Syftet med den begränsningen är att undvika [indirekta prompt-injektionsattacker](https://techcommunity.microsoft.com/blog/microsoft-security-blog/understanding-and-mitigating-security-risks-in-mcp-implementations/4404667) och andra liknande säkerhetsproblem.
- **Inga skrivrättigheter**: Din MCP-server får inte begära skrivrättigheter till användarens system eller data.
- **Gratis**: Din MCP-server ska vara gratis att använda.
- **Öppen källkod**: Din MCP-server måste vara öppen källkod och tillgänglig på GitHub.
- **Dokumentation**: Du måste inkludera tydlig dokumentation

Teamet för Staten förbehåller sig rätten att avvisa eller ta bort MCP:er som anses olämpliga eller som inte följer dessa riktlinjer.

### MCP Schema

Varje MCP i registret måste följa följande JSON-schema:
```json
{
  "name": "Your App Name",
  "description": "En kortfattad beskrivning av vad din app gör och hur den hjälper användare",
  "icon": {
    "type": "url",
    "url": {
      "light": "URL till en ljus ikon för mörkt läge (SVG eller PNG rekommenderas)",
      "dark": "URL till en mörk ikon för ljust läge (SVG eller PNG rekommenderas)"
    }
  },
  "category": "Kategori för din app (t.ex. Kommundata, Politik, Hälsa)",
  "price": "Alla appar ska vara gratis",
  "developer": "Ditt namn eller organisation",
  "sourceUrl": "URL till källkodens på GitHub",
  "config": {
    "mcpKey": "unik identifierare för din MCP",
    "runtime": "körmiljö (t.ex. npx, uvx)",
    "args": [
      "array med argument som krävs för att köra din MCP",
      "--api-key=${API_KEY}",
      "--base-url=${BASE_URL}"
    ]
  },
  "features": [
    {
      "name": "Funktionsnamn",
      "description": "Kort beskrivning av funktionen",
      "prompt": "Exempel på prompt som användare kan använda för att aktivera denna funktion"
    }
  ],
  "setup": [
    {
      "label": "API-nyckel",
      "type": "input",
      "placeholder": "Ange din API-nyckel",
      "value": "",
      "key": "API_KEY"
    },
    {
      "label": "Bas-URL",
      "type": "input",
      "placeholder": "https://api.example.com",
      "value": "https://api.example.com",
      "key": "BASE_URL"
    }
  ]
}
```

### Beskrivningar av schemats fält

| Fält | Beskrivning | Obligatoriskt |
|-------|-------------|----------|
| `name` | Visningsnamnet på din app | Ja |
| `description` | En kort beskrivning av vad din app gör | Ja |
| `icon` | Ikonkonfiguration med varianter för ljust och mörkt läge | Ja |
| `category` | Kategori som din app tillhör | Ja |
| `price` | Prisinformation (Gratis, Betald, etc.) | Ja |
| `developer` | Namnet på utvecklaren eller organisationen | Ja |
| `sourceUrl` | URL till källkodens repository | Ja |
| `config` | Konfigurationsdetaljer för att köra MCP | Ja |
| `config.mcpKey` | Unik identifierare för din MCP | Ja |
| `config.runtime` | Körmiljö (npx, uvx, etc.) | Ja |
| `config.args` | Argument som behövs för att köra din MCP | Ja |
| `features` | Array med funktioner som din app erbjuder | Ja |
| `features[].name` | Namnet på funktionen | Ja |
| `features[].description` | Beskrivning av vad funktionen gör | Ja |
| `features[].prompt` | Exempelprompt för användare | Ja |
| `setup` | Array med installationsinstruktioner för konfiguration | Nej |
| `setup[].label` | Etikett för konfigurationsobjektet | Om setup inkluderas |
| `setup[].type` | Typ av input (input, select, etc.) | Om setup inkluderas |
| `setup[].placeholder` | Platshållartext | Om setup inkluderas |
| `setup[].value` | Standardvärde eller instruktioner | Om setup inkluderas |
| `setup[].key` | Miljövariabelnyckel | Om setup inkluderas |

### Stöd för miljövariabler

Staten stödjer användning av miljövariabler i din MCP-konfiguration. Detta gör att du kan skapa mer flexibla och anpassningsbara MCP:er utan att hårdkoda känslig information eller användarspecifika inställningar.

#### Användning av miljövariabler i Args

Du kan inkludera miljövariabler i din `args`-array med hjälp av `${VARIABLE_NAME}`-syntaxen. Exempelvis:

```json
"args": [
  "start",
  "--api-key=${API_KEY}",
  "--base-url=${BASE_URL}",
  "--port=${PORT}"
]
```
När MCP:n installeras kommer Staten automatiskt att ersätta dessa variabler med motsvarande värden från användarens konfiguration.

#### Funktioner för miljövariabler

- **Enkla variabler**: `--api-key=${API_KEY}`
- **Flera variabler i ett argument**: `--connection=${HOST}:${PORT}`
- **Variabler med kringliggande text**: `--path=prefix_${PATH_VAR}_suffix`
- **Stöd för olika värdetyper**: Nummer, booleska värden och strängar stöds alla
- **Variabler av sökvägstyp**: `--config=${CONFIG_DIR}/settings.json`

#### Inställning av miljövariabler

För att göra din MCP konfigurerbar, definiera de nödvändiga miljövariablerna i `setup`-arrayen. Varje post bör innehålla:

1. En användarvänlig etikett
2. Typen av inmatning
3. En platshållare eller ett standardvärde
4. Nyckeln för miljövariabeln som matchar den som används i dina `args`

Detta ger användarna en smidig konfigurationsupplevelse samtidigt som känslig information hålls säker.

### Riktlinjer för inskickning

1. Forka detta repository
2. Lägg till din MCP i filen `apps.json`
3. Lägg vid behov till din app-ikon i mappen `assets`
4. Skicka en pull request med en tydlig beskrivning av din MCP

### Riktlinjer för ikoner

- Ikoner bör vara i SVG-format om möjligt (PNG är acceptabelt om SVG inte finns tillgängligt)
- Inkludera varianter för både ljust och mörkt läge om din ikon ser olika ut i olika teman
- Placera din ikon i mappen `assets` och referera till den i din MCP-konfiguration

## Licens

Detta repository är licensierat under Apache License 2.0 – se filen LICENSE för detaljer.

## Support

För frågor eller problem relaterade till Staten App Registry, öppna ett ärende i detta repository.

