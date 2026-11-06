# Belgium Data Desk for Claude

Find, check and preview Belgian open data from inside Claude. The plugin connects Claude to the Belgium Data Desk catalogue of about 21,700 records from federal, regional and local publishers (Statbel, the KBO/BCE enterprise register, the National Bank, regional environment and mobility agencies, cities) and adds a skill with the background needed to use Belgian data correctly: who publishes what, municipality codes and the 2025 mergers, Belgian CSV conventions and licences.

## What you get

- **Multilingual search** in Dutch, French or English. For common needs (population, enterprise numbers, air quality, unemployment, train punctuality, elections and more) the official reference source comes first, with who publishes it and the usual pitfalls.
- **Evidence per dataset:** publisher, licence and what it allows, update cycle, contact point, declared columns and whether the download link worked when last checked.
- **File preview:** column names, inferred types, delimiter, encoding and sample rows read from the first 64 KiB of a dataset's CSV or TSV file.
- **Subsurface of Flanders, live:** for an address or point, the soil-map unit, geological map units, groundwater bodies and drinking-water protection zone, plus nearby boreholes, cone penetration tests and groundwater wells; then layer-by-layer borehole logs, CPT profiles and groundwater levels. Read at question time from Databank Ondergrond Vlaanderen (DOV), with the citation DOV asks for.
- **A skill** (`belgian-open-data`) that tells Claude how to choose sources, join on REFNIS codes, read semicolon/decimal-comma files and report licences.

## Example prompts

- "Which dataset gives the population per municipality, and can I reuse it commercially?"
- "Zoek de officiële bron voor ondernemingsnummers en toon welke kolommen het bestand heeft."
- "Quelles données existent sur la qualité de l'air à Bruxelles, et qui les publie ?"
- "I want to compare unemployment across Flemish municipalities. Which numbers should I use?"
- "Wat zit er in de ondergrond onder Grote Markt 1 in Leuven, en hoe diep staat het grondwater daar?"

## What it connects to and what it sends

The plugin declares one remote MCP server, `https://data.vecture.be/mcp`, operated by Vecture. It needs no account or API key. When Claude calls a tool, the server receives only the tool arguments: search words, a dataset id, a row count, or for subsurface questions an address, coordinates or a DOV record id. For a file preview the server reads the start of that dataset's public file from the publisher listed in the catalogue; for subsurface questions it sends the address to the Flemish Government's geolocation service and coordinates or record ids to DOV. It never fetches a web address supplied by the caller. All tools are read-only. The plugin runs no local code, hooks or scripts.

## Privacy

Search words, dataset ids and addresses are used only to answer and are not stored. The server keeps aggregate usage counters (tool name, success, result count, response time, coarse topic) without search text, ids, IP addresses or conversation content, for up to three months. Full policy: https://data.vecture.be/privacy

## Limits

Answers describe catalogue metadata, link checks and file samples, not the correctness of the numbers in a dataset. Previews cover the first 64 KiB of CSV/TSV files only. Link checks cover HTTPS tabular files and are refreshed weekly. The catalogue is broad but not exhaustive. Subsurface answers cover the Flemish Region only and are map-scale and point-observation information, not a site investigation.

## Support

Documentation and troubleshooting: https://data.vecture.be/connect/
Questions, wrong records or security issues: info@vecture.be

## License

MIT, see `LICENSE`. Datasets belong to their publishers and carry their own licences.
