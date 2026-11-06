---
name: belgian-open-data
description: Answer questions that need Belgian public data (population, municipalities, companies and the KBO/BCE, employment, mobility, energy, environment, elections, addresses, parcels) by finding the official source, checking its licence and freshness, and reading the real file structure before using it. Use when a user asks for Belgian statistics or datasets, or wants to join, compare or cite Belgian data.
---

# Belgian open data

Belgian public data is split across federal, regional, community and local publishers, three languages and frequently changing boundaries. Most wrong answers come from using the wrong publisher, the wrong boundary year or misreading a file. This skill gives the working method and the background that prevents those mistakes. It uses the Belgium Data Desk connector bundled with this plugin.

## Working method

1. **Find the source.** Call `search_datasets` with the user's own words; Dutch, French and English all work. If the result has a `guidance` block, the first results are the official reference source for that need: say who publishes it and pass on the caveat when it matters for the user's question.
2. **Check before recommending.** Call `get_dataset` for the record you intend to use. Report the publisher, the licence family (`open`, `non-commercial`, `custom`, `unknown`), the update cycle and the link check. `unknown` licence means the user must check the publisher's terms before reuse; do not call it open.
3. **Look at the file.** When `preview_available` is true, call `preview_dataset` to see the real column names, types, delimiter and sample rows before describing what the data contains or how to join it. The preview covers the first 64 KiB only: never present `rows_read` or sample values as totals for the whole file.
4. **Answer with evidence.** Give the dataset title, publisher and landing page. Quote values only from a preview or from the publisher's page, label them as samples, and say which year or reference date they describe. If no record fits, say so and suggest the publisher most likely to hold the data.

The connector describes catalogue metadata, link checks and file samples. It does not certify that the numbers in a dataset are correct, and it cannot compute totals over whole files.

## Subsurface questions in Flanders

For soil, geology, boreholes, groundwater or "what is under this address" in the Flemish Region, call `subsurface_at_location` with the address (or latitude/longitude). It reads Databank Ondergrond Vlaanderen (DOV) live. Then call `get_subsurface_record` with an id it returned: a borehole (`boring/…`) for its layers, a groundwater screen (`filter/…`) for water levels, a CPT (`sondering/…`) for cone resistance.

- Map units (soil map, geological maps) are map-scale information and can be wrong for a single parcel; say so when a user plans works. Boreholes are point observations: give the distance from the address.
- Borehole depths are metres below ground at the borehole; groundwater levels are in m TAW (height above the Belgian datum). A depth below ground needs the ground level, which the record gives.
- Old borehole logs are as written at the time, often in French; translate them and keep the original wording for key terms.
- The groundwater status is the nearest status well's latest level classed against that well's own history, which may be kilometres away; it describes the regional shallow groundwater, not the user's parcel.
- DOV covers the Flemish Region only. Brussels and Wallonia have their own geological services.
- Cite DOV with the `provenance.citation` text; DOV data may be reused for any purpose with that attribution. These tools do not replace a geotechnical study or permits (a geothermal borehole or a well needs the usual permit or notification).

## Who publishes what

- **Federal statistics:** Statbel (population, households, prices and the consumer price index, road accidents, census, enterprises by sector). The National Bank of Belgium publishes company annual accounts and economic statistics.
- **Regional statistics:** Statistiek Vlaanderen (Flanders), IWEPS (Wallonia) and IBSA/BISA (Brussels) publish regional indicators, often more detailed than federal tables.
- **Enterprises:** the Crossroads Bank for Enterprises (KBO in Dutch, BCE in French), kept by FPS Economy, is the register of every enterprise number. A Belgian VAT number is `BE` followed by the 10-digit enterprise number.
- **Employment:** RVA/ONEM counts unemployment-benefit recipients; registered jobseekers are counted by the regional services (VDAB, Le Forem, Actiris and ADG for the German-speaking Community); the ILO unemployment rate comes from Statbel's Labour Force Survey. These three numbers differ and must not be mixed.
- **Environment, mobility, spatial planning, agriculture:** mostly regional. Flanders, Wallonia and Brussels each publish their own data, with different formats and dates.
- **Education, childcare, culture:** community matters. Flemish Community bodies cover Flanders and Dutch-language institutions in Brussels; the Fédération Wallonie-Bruxelles covers French-language ones.
- **Rail:** Infrabel (infrastructure, punctuality) and SNCB/NMBS (passenger services). Public transport timetables are published as GTFS per operator (SNCB, De Lijn, TEC, STIB-MIVB).

When the question is about all of Belgium and the data is regional, say that the user needs three sources and that they may not be comparable.

## Places and codes

- Belgium has 3 regions, 10 provinces and, since the mergers of 1 January 2025, 565 municipalities (581 before). Data published before 2025 uses the old municipalities.
- Join municipal data on the 5-digit REFNIS (NIS/INS) code, never on names: names differ by language (Antwerpen / Anvers, Liège / Luik, Bergen / Mons) and change with mergers. Statbel's REFNIS code table maps codes; check which boundary year each file uses.
- The first digit of a REFNIS code gives the province; Brussels-Capital municipalities are 21001 to 21019.
- Statistical sectors are Statbel's sub-municipal units; their codes start with the municipality's REFNIS code.

## Reading Belgian files

- Many CSV files use a semicolon as delimiter and a decimal comma (`1.234,5` means one thousand two hundred thirty-four and a half). `preview_dataset` reports `delimiter` and `decimal_separator: "comma"` when it sees this.
- Older files are often Windows-1252 rather than UTF-8; the preview reports the encoding it used.
- Column headers are frequently in Dutch or French only; translate them for the user and keep the original name next to the translation.
- A link labelled CSV sometimes returns a spreadsheet or a web page. The preview says when a publisher answered with a web page instead of a file.

## Licences

Report the licence label and family from `get_dataset`. Open licences (for example CC BY or CC0) allow commercial reuse with the stated attribution. `non-commercial` and `no-derivatives` restrict reuse. `custom` and `unknown` mean the user must read the publisher's terms. Do not guess a licence from the publisher's reputation.
