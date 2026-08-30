# codeby.se

Statisk kontaktsida för Code By AB, byggd med [Hugo](https://gohugo.io)
och publicerad på [GitHub Pages](https://pages.github.com/) automatiskt
vid push till `main`. Ingen CMS — innehållet är två Markdown-filer som
redigeras direkt i repot.

## Struktur

```
content/           Sidinnehåll som Markdown (_index.sv.md / _index.en.md)
layouts/            Egna Hugo-mallar (inget tredjepartstema)
assets/css/         Handskriven CSS (design tokens + komponenter, ingen npm/Tailwind)
static/             Statiska filer: logotyp, CNAME, PGP-nyckel
.github/workflows/  GitHub Actions-workflow som bygger och publicerar sajten
```

Sajten är avsiktligt en enda sida — ingen sektionsindelning, inga taggar
eller menyer, eftersom det inte finns innehåll som motiverar det ännu.

## Lokal utveckling

Kräver [Hugo Extended](https://gohugo.io/installation/) (testat med
v0.165.0). Inget npm/Node.js behövs.

```bash
hugo server -D     # lokal förhandsvisning på http://localhost:1313/, visar utkast
hugo --minify       # produktionsbygge till public/ (utkast exkluderas automatiskt)
```

**Draft/publish:** varje sida har `draft: true/false` i frontmatter.
`hugo --minify` (utan `-D`) utesluter automatiskt allt med `draft: true`
— det är den enda platsen opublicerat innehåll hålls borta från
live-sajten.

## Redigera innehåll

Sidans text (rubrik, tagline, kontaktuppgifter) ligger i
`content/_index.sv.md` (svenska, publiceras på `/`) och
`content/_index.en.md` (engelska, publiceras på `/en/`). Ändra texten
direkt i filerna och committa — ingen CMS eller inloggning krävs.

## Driftsättning

GitHub Actions (`.github/workflows/hugo.yml`) bygger sajten med
`hugo --minify` och publicerar till GitHub Pages vid varje push till
`main` — ingen manuell build-process. I repots inställningar måste
**Settings → Pages → Source** vara satt till "GitHub Actions" (gjort en
gång, inte del av koden).

Anpassad domän: `static/CNAME` innehåller `codeby.se` (apex-domän).
GitHub Pages läser filen från den publicerade `public/`-mappen, men
domänen måste även vara konfigurerad separat i Pages-inställningarna
(sker inte automatiskt bara för att `CNAME`-filen finns, till skillnad
från äldre branch-baserad Pages-publicering).

## Tillgångar

- **Logotyp** (`static/logo.png`): transparent PNG, används både som
  favicon och som logotyp i sidhuvudet (`layouts/partials/header.html`).
- **PGP-nyckel** (`static/502f4fa003332517decd4441a3f.asc`): publik nyckel,
  länkad från kontaktstycket på sidan.

## SEO

Kanonisk URL, Open Graph-metadata och `hreflang`-alternates för
sv/en genereras per sida (`layouts/partials/seo.html`).
