---
name: local-food-discovery
description: Find local food and drink producers, recorded products, food fairs and markets, their participants or map coordinates by place and proximity. Use for culinary travel and professional sourcing, not general geography or personal calendars.
---

Use Chisan's published catalog to find producers, their recorded products and
reviewed food event editions.
Follow the user's requested place, food and language; use the available MCP tool
schemas for exact parameters. Cite returned Chisan producer and event URLs.

## Recognize the discovery request

Consider this workflow for requests such as “queserías cerca de Vic”, “ferias de
alimentación en Girona este mes” or “sitúa los productores de esa feria en un
mapa”. Check
published coverage before claiming a place is covered. General geography,
restaurant-only recommendations and personal calendar management do not by
themselves call for this workflow. Selection depends on the host; installation
does not prove that a request activated a skill or tool.

## Complete the discovery journey

Identify the requested outcome: find producers, find a specific product, compare
options, find an event or its participants, plan a visit or reach a producer's sales channel. Preserve explicit
constraints across follow-ups. Ask only for missing information that changes
the answer; broad discovery does not require knowing every preference first.

For open discovery, start with a useful shortlist. Image inventories use the
complete-source workflow below instead. Reuse coverage, returned identities and facts
within the task; fetch detail only for a chosen option or a missing comparison
field. For follow-ups such as “the second one,” use its returned identity instead
of repeating an ambiguous name search. Refresh reads when current data is needed.

## Resolve the request

- Use the user's stated meaning of “local.” If no reference place or radius is
  known, ask for it before classifying results as local; producer identification
  can proceed meanwhile. A catalog area is not a default definition of local.
- Call `chisan_catalog` to discover current published countries, region/area slugs,
  category tokens and available area languages. Reuse discovery within the task.
  Never infer publication from a country's existence outside this response.
- For literal filters, use the returned country code and exact region/area slug. Region and area
  require country. Use a named municipality in natural search; literal proximity
  takes the public place reference returned by that search.
  Catalog areas need not be counties: Osona and Priorat may have no area token.
  Natural search understands reviewed district names as province-plus-mention
  constraints, not official boundary polygons. For an unsupported subregion, clarify the municipality or search centre, or
  explicitly describe a broader search. Text mentioning a place does not prove
  its boundary. Do not silently replace the requested territory with a province.
- Resolve the food to a returned category token before filtering. The producer's
  category does not prove that it sells a particular product or variety.
- Reply in the user's language. Natural search accepts the published search-page
  language and keeps all areas through default-language data when needed; inspect
  `data_locale`. For literal operations, supply `locale` only when the relevant
  area publishes it; omitting it uses the country's default. Do not exclude useful
  results merely because the conversation language is unavailable there.

## Choose the read operation

- `chisan_search_catalog`: prefer this for sentences, multilingual discovery,
  proximity and basic comparisons. Pass the user's criteria naturally in `q`,
  with a published country. It uses the same engine as Chisan's web search;
  do not manually rewrite its vocabulary into guessed synonyms. It returns
  interpreted criteria, supported comparison facts and match snippets. Use
  `relaxation: none` when every interpreted term/radius must hold. If an older
  server does not advertise this operation, use its available precise tools.
- `chisan_search_producers`: base name, municipality, categories, featured-product
  summary and base description. It does not search expanded product records.
- `chisan_search_products`: individually recorded visible products. `q` searches
  product name, description and format; a variety must literally occur there.
  `month` records usual seasonality, never current availability. Preserve
  `(country, producer_id, product_id)`; do not split a summary into invented products.
- Text matching in these two precise search operations is literal, accent-insensitive and requires every term. It has
  no stemming, synonyms or automatic translation. Try explicit useful alternatives
  when needed, reporting any relaxation of the user's criteria.
- Recorded certifications: natural search interprets words such as “ecológico”,
  but a text mention is not a recorded certification. Present a certification
  only from the returned `facts.certifications`, and read detail for its scope.
- `chisan_get_producer`: fetch selected identities using the returned `country`
  and `producer_id` to inspect products, facts and handoff links. Preserve official
  names and the producer's existing profile route. Do not reconstruct URLs.
  Search already returns the producer's place, coordinates and profile URL;
  avoid a detail call for every hit when those fields answer the request.
- `chisan_search_events`: find reviewed food fairs, markets and other editions
  by venue area, category, literal event text, date interval, a
  producer's recorded participation or a centre-radius. Check the separate
  `chisan_catalog.events.countries` coverage. `date_from`/`date_to` include any
  edition overlapping those local calendar dates; without bounds, archived
  editions are included. Resolve “this weekend” using the conversation date and
  event country, clarifying only if ambiguous. Do not pass a whole natural
  sentence as literal `q`. For multilingual requests prefer structured category,
  place and date filters; any literal terms must fit the searched data language.
  A radius uses the venue coordinates and sorts by
  distance before pagination. Require the same explicit centre as producer
  radius searches; nearby origin never establishes event participation.
- `chisan_get_event`: read a returned `event_slug` for its bounded reviewed
  roster. Follow `next` to complete a requested inventory. Each `exhibitors`
  entry supplies the producer profile and its own coordinates, plus recorded
  presence, stand and note. The roster may be partial; cite the event and the
  profiles used. Event language follows published translations; producer links
  fall back to the country language when needed. The venue point, producer
  origin and stand reference have distinct meanings. Never locate an unmapped
  producer at the venue or infer a stand position. Dates and `schedule` are
  recorded information; confirm changes or cancellation at the official source.
  If an older server does not advertise event operations, state that limitation
  and use the public event pages when web access is available.

## Read the natural search result

- Inspect `status` before presenting a shortlist. `alternatives` drops words or
  widens a radius and must be labelled separately from full matches. `no_matches`
  is a successful empty search; `needs_query` asks for a meaningful criterion.
  `needs_location` asks for a reference, not a conclusion about local coverage.
- Return corrections, approximate words, dropped terms and unsupported criteria
  when they change the answer. Opening status, live prices and travel times are
  never fulfilled by matching those words. A free-from text claim is not proof
  of allergen safety, and `text_mention` is not a recorded certification.
- Natural results sort by distance when proximity asks for it, across the complete
  eligible set before pagination. A named municipality uses an estimated centre
  from catalog points. Use `center` only with coordinates of a named place;
  never request or infer the user's own device position.
- Reuse returned `facts`, public sales/contact fields and match evidence for
  comparisons. Inspect certification scope and unknowns; detail remains available
  when richer information is needed.
- Offer measured `suggestions` as choices. Their `input` is replayable; a `drop`
  explicitly changes a condition. Follow the complete `next` input for more of
  the same set; changed sentences or centres require restarting without revision.
- `links.search_url` and `links.map_url` reopen the same sentence in Chisan's
  list/map. They are absent when a private caller centre would be lost or a
  strict empty search would differ from web recovery. Use each producer's
  individual `map_url` instead; never add the caller's coordinates to a URL.

## Match names read from labels

For a photo, document or transcription inventory, use the packaged
[photo-to-producers](../photo-to-producers/SKILL.md) workflow. It owns source order,
readings, per-item identity/product resolution, unresolved items and research
candidate handoff. Its complete-inventory requirement replaces the default
shortlist, without changing these tools or their search semantics. Chisan receives
text searches; host vision reads the image. Reading a brand alone does not
identify a factory, a specific product or its origin.

## Handle proximity and completeness

- Search around a place the user names, such as a town or a returned event
  venue. Natural search accepts the town in `q` (“a menos de 30 km de Vic”) and
  estimates its centre from catalog points. For literal producer/product or event
  radius searches, first obtain `interpretation.proximity.place_ref` from natural
  search for that town. Supply that reference, the same `country` and `radius_km`
  together (at most 500 km). The reference is validated against public catalog
  town centres; do not invent one or pass raw city/coordinate fields.
  Never ask for, guess or use the user's own GPS position or home address.
  For “near me”, ask which public town or place they want to explore.
- An 80 km radius around Vic includes surrounding municipalities. Do not replace
  it with an exact-town filter, or add an area unless the user wants that limit.
- Distances are approximate straight-line distances. Unmapped producers are
  excluded by radius and required proximity joins. Literal search ordering is not
  nearest-first; natural search reports `ordering: distance` when it sorts by
  distance over the full eligible set. Only label a result nearest with that
  evidence or a complete relevant comparison;
  never infer driving time, a road route or a commercial relationship from proximity.
- Use bounded pages. Carry the filters, offset and revision returned in a search
  `next` URL; natural search returns the complete next input object. On
  `catalog_changed`, restart at offset zero without the old revision. Narrow a
  `query_too_broad` plan. Stop and report persistent errors instead of treating
  them as an empty catalog. Respect a rate-limit retry delay.
- Product `visibility: unavailable` means only basic products may be visible;
  absence then cannot establish complete coverage. An empty search means no
  matching published records, not that a food or producer does not exist.

When present, inspect `applied`, `ordering` and `limitations` to describe what was
actually searched. On older server versions, use the tool's documented semantics.
For no literal matches, check published language and category, then try useful explicit
singular/plural or translated text alternatives. Keep required geography and
facts intact. Describe any broader fallback; do not silently turn an unmet
constraint into a match. A server failure needs an error explanation, not a
“nothing found” answer.

## Present useful choices

Give a concise selection with the recorded producer or product name, municipality,
the reason it matches and a Chisan profile link. Mention when `next` means more
results exist. Compare options on the user's criteria, leaving unavailable
fields unknown; search order is not a “best producer” ranking. Professional sourcing
may use recorded `professional_sales`, minimum order and delivery conditions;
missing fields do not establish wholesale availability or capacity. For live
availability and direct contact, apply `truthfulness-and-handoff`.

Offer the returned `map_url` to open Chisan focused on the producer. Use returned
coordinates if the host can render a map, or the recorded `contact.maps` link
when present. Older servers may omit `map_url`; never reconstruct it. Missing
coordinates remain unmapped. A map pin
locates the recorded producer, not necessarily a public shop or the factory of
the photographed product. This plugin does not supply its own embedded map UI.
