# AI Climate Risk Portfolio Explorer

<p align="center">
  <img src="1.0%20Architecture/AI%20TF%20Climate%20Explorer.gif" alt="AI Climate Risk Portfolio Explorer architecture and workflow" width="100%" />
</p>

The AI Climate Risk Portfolio Explorer is a browser-based geospatial tool for screening physical climate hazards at asset locations and comparing exposure across a portfolio. Developed by SEI Latin America, the current application focuses on Colombia and combines Climate Impact Explorer (CIE) v2 data, interactive 2D/3D maps, portfolio analysis, and an AI-assisted question-and-report workflow.

> **Screening tool, not a financial-risk model.** The indicators describe climate hazards at roughly 0.5° (~55 km) resolution. They do not estimate financial losses or account for asset vulnerability, value, adaptation, or local site conditions. Do not use the tool alone to make investment or operational decisions.

**Try the deployed explorer:** [ai-climate-risk-portfolio-explorer.org](https://ai-climate-risk-portfolio-explorer.org/)

## What the tool does

- Displays climate indicator grids on Leaflet 2D maps and Cesium 3D globes.
- Compares the included **+1.5°C and +3.0°C global-warming-level** datasets, or selects an RCP scenario and year when using the CIE live API source.
- Adds assets by entering coordinates or importing CSV, TXT, GeoJSON, or JSON location data. CSV uploads use `name`, `lat`, and `lon` columns.
- Matches assets to the grid cell containing their coordinates, or the nearest cell when needed; the application marks nearest-cell matches.
- Summarizes portfolio values and changes, ranks assets, and classifies relative exposure tiers within the selected portfolio.
- Provides spatial drawing and measurement tools, multi-hazard profiles, printable asset factsheets, Excel/CSV exports, and shareable links.
- Guides users through a four-step AI workflow: data selection, risk assessment, response/report, and delivery/feedback. When the AI service is reachable, an open-source LLM drafts the response from submitted portfolio context; responses are checked against the data and can be flagged for SEI specialist review. If the service is unavailable, the explorer falls back to a demonstration response.

The tool reports **hazard indicators and relative exposure**, not absolute risk thresholds. Relative HIGH/MEDIUM/LOW tiers are calculated within the current portfolio. Hazard percentiles compare an asset's grid cell with Colombian grid cells. Values and comparisons depend on the selected indicator, data source, and scenario.

## Data and limitations

The `3.0 Data/` directory contains paired CSV grids for two warming levels for indicators including dangerous heat days (`HI-danger`), annual maximum temperature (`TXx`), precipitation extremes (`rx1day`, `rx5day`), consecutive dry days, fire-weather days (`fwixd`), temperature (`tasAdjust`), precipitation (`prAdjust`), water stress (`wsi`), drought (`spei_gamma_12`), labour productivity loss, and river discharge (`dis`). `diccionario_variables.csv` documents indicator names and descriptions. The grid CSVs use columns such as `lat`, `lon`, `value`, `variable`, `tipo`, and `warming_level`.

The browser app can load the bundled GitHub-hosted warming-level data or request data from the CIE v2 live API. Browser/API availability can vary (including CORS restrictions); the bundled data source is the fallback option. Data are central estimates and do not show the spread across climate models. The grid resolution does not resolve local features such as valleys, coastlines, or urban heat islands. River-flood data and other indicators listed in the dictionary may not be available as app layers just because they are named in the metadata.

## Repository layout

```text
.github/                 Issue templates
0. Old version/          Legacy R analysis and an earlier static dashboard
1.0 Architecture/        Architecture/workflow animation
2.0 docs/                Current browser application and administration page
  admin/index.html       Human-in-the-loop administration prototype
  index.html             Explorer interface
  geovisor.js            Map, data, analysis, export, and AI workflow logic
  geovisor.css           Application styling and themes
  i18n.js                English, Spanish, and Portuguese translations
3.0 Data/                Climate-grid CSVs, variable dictionary, and data logs
4.0 Concept Note/        Project concept note (PDF)
README.md                Project documentation
```

### Application and AI flow

`2.0 docs/index.html` builds the interface and loads Leaflet, Cesium, Tailwind CSS, and the application scripts from CDNs. `geovisor.js` manages climate sources and scenarios, grid rendering, asset matching and portfolio summaries, spatial tools, exports, and the four-step agent interface; `i18n.js` supplies English, Spanish, and Portuguese UI text. The optional AI service is checked at `https://api.ai-climate-risk-portfolio-explorer.org/health`. When online, the browser sends a run to `/v1/agents/run`; delivery and survey events are posted to the corresponding run endpoints. The service is not part of this repository, and the browser-side workflow has a demonstration fallback.

The separate `2.0 docs/admin/index.html` is a prototype review dashboard. It uses Cloudflare Access identity endpoints and browser `localStorage` for its review state and sample data; it is **not** a production database or centralized review system. Deployment must configure Cloudflare Access and the required routes. Do not treat the admin screen's sample records or local browser state as persistent production records.

## Run locally

There is no package manifest, build step, or automated test command in this repository. The current explorer is a static front-end; Python 3 is sufficient to serve it locally:

```bash
git clone https://github.com/sei-latam/AI-Climate-Risk-Portfolio-Explorer.git
cd AI-Climate-Risk-Portfolio-Explorer/2.0\ docs
python -m http.server 8000
```

Open <http://localhost:8000/>. A local static server lets you explore the browser UI; live CIE requests, the optional AI service, and Cloudflare Access administration depend on their external services and deployment configuration. The administration page is at `/admin/` in the configured deployment and requires an authorized SEI account.

## Development notes

- The current app is plain HTML, CSS, and JavaScript; there is no `package.json` or build/test script.
- Most front-end libraries are loaded from CDNs, so internet access is needed for those libraries and online map/data services.
- The files under `0. Old version/` are historical examples, not the current app. The R script contains machine-specific working-directory paths and requires its R packages and external data services; it is not a ready-to-run setup for a fresh clone.
- For contribution guidance, see [CONTRIBUTING.md](CONTRIBUTING.md). The project uses the [GNU GPL v3](LICENSE).

## Credits and references

Climate indicator data are attributed in the application to Climate Analytics' [Climate Impact Explorer v2](https://climate-impact-explorer.climateanalytics.org/). The explorer is developed by [SEI Latin America](https://www.sei.org/centres/latinoamerica/). See the in-app **About the data** panel for methodology, indicator definitions, and citation details.
