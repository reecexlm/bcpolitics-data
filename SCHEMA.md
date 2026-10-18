# BC Politics Guide — data schema

All files live in `data/` and are UTF-8 JSON (pretty-printed, 2-space indent).
The iOS app decodes these exact shapes; do not rename keys. Optional fields may be `null`.

## Neutrality rules (apply to every file)
- Facts only. No adjectives that judge ("controversial", "radical", "common-sense", "extreme").
- Party positions are paraphrased from the party's own published documents and cited.
- Every record carries `sources` (array of URLs) so a reader can verify it.
- If a fact can't be verified, leave it `null`. Never guess.
- Data must reflect the state **as of the date you collect it** (it is now October 2026). Check for
  by-elections, resignations, floor-crossings, party switches, cabinet shuffles since the Oct 19 2024 election.

## IDs
- MLA id: lowercase slug of full name, e.g. `david-eby`.
- Riding id: lowercase slug of the electoral district name, e.g. `vancouver-point-grey`.
- Party id: `ndp`, `conservative`, `green`, `independent`, plus slugs for any others (e.g. `onebc`).

## parties.json
```json
[{
  "id": "ndp", "name": "BC New Democratic Party", "shortName": "BC NDP",
  "color": "#F58220", "leaderName": "David Eby", "leaderMlaId": "david-eby",
  "seats": 47, "status": "Government",            // Government | Official Opposition | Third party | Other
  "website": "https://www.bcndp.ca",
  "selfDescription": "Short quote or close paraphrase of how the party describes itself (cite it).",
  "votes2024": 1000000, "voteShare2024": 44.9, "seats2024": 47,
  "sources": ["https://..."]
}]
```

## mlas.json
```json
[{
  "id": "david-eby", "name": "David Eby", "firstName": "David", "lastName": "Eby",
  "partyId": "ndp", "ridingId": "vancouver-point-grey",
  "roles": [{"title": "Premier", "kind": "premier", "since": "2022-11-18"}],
  // kind ∈ premier | minister | minister_of_state | parliamentary_secretary | speaker | deputy_speaker
  //        | party_leader | opposition_leader | critic | house_leader | whip | caucus_chair | other
  "firstElected": 2013, "timesElected": 4,
  "previousOccupation": "Lawyer",
  "bio": "2–4 neutral factual sentences: background, education, career before politics.",
  "photoUrl": "https://www.leg.bc.ca/... (official portrait URL, or null)",
  "email": "...", "legislaturePhone": "...",
  "constituencyOffice": {"address": "...", "phone": "...", "email": "..."},
  "website": null,
  "social": {"x": null, "facebook": null, "instagram": null},
  "notes": ["Neutral factual notes, e.g. 'Elected as a Conservative in 2024; sits as an Independent since 2025-03-…'"],
  "sources": ["https://..."]
}]
```

## ridings.json
```json
[{
  "id": "vancouver-point-grey", "name": "Vancouver-Point Grey",
  "region": "Vancouver",   // one of: Vancouver, Surrey, Fraser Valley, Tri-Cities & Burnaby, Richmond & Delta,
                           //   North Shore & Sea-to-Sky, Vancouver Island & Coast, Okanagan, Kootenays,
                           //   Thompson & Cariboo, North
  "mlaId": "david-eby",
  "registeredVoters": 45000, "totalVotes": 30000, "turnoutPct": 62.1,
  "candidates2024": [{"name": "David Eby", "partyId": "ndp", "votes": 17000, "pct": 56.4, "elected": true}],
  "marginVotes": 9000, "marginPct": 30.1,
  "byElection": null,      // or {"date": "...", "reason": "...", "candidates": [same shape]}
  "sources": ["https://..."]
}]
```

## ridings.geojson
FeatureCollection, WGS84 lon/lat, one feature per district, `properties.id` = riding id,
`properties.name`. Simplified so the whole file is < 3 MB.

## bills.json
```json
[{
  "id": "43-2-15", "number": "15", "parliament": 43, "session": 2, "year": 2025,
  "title": "Infrastructure Projects Act",
  "kind": "government",       // government | private_member | private
  "sponsorMlaId": "...", "sponsorName": "...",
  "status": "Royal Assent",  // First Reading | Second Reading | Committee | Report | Third Reading | Royal Assent | Died on order paper | Withdrawn
  "introduced": "2025-04-01", "lastAction": "2025-05-29",
  "summary": "Neutral 1–3 sentence description of what the bill does (from the bill's explanatory note).",
  "topics": ["housing", "energy"],   // use issue ids from platforms.json
  "divisionIds": ["..."],
  "url": "https://www.leg.bc.ca/...", "sources": ["https://..."]
}]
```

## divisions.json  (recorded votes)
```json
[{
  "id": "2025-05-15-bill-15-3r", "date": "2025-05-15", "parliament": 43, "session": 2,
  "billId": "43-2-15", "question": "Third reading of Bill 15",
  "result": "Passed", "yeas": 47, "nays": 44,
  "votes": {"david-eby": "yea", "john-rustad": "nay"},   // yea | nay | absent | paired
  "sources": ["https://... Votes and Proceedings / Hansard"]
}]
```

## platforms.json
```json
{
  "issues": [{"id": "housing", "name": "Housing & Affordability", "icon": "house"}],
  "positions": [{
    "partyId": "ndp", "issueId": "housing",
    "summary": "1–2 neutral sentences.",
    "commitments": ["Specific commitment as stated by the party"],
    "sourceTitle": "BC NDP 2024 platform", "sourceUrl": "https://..."
  }]
}
```
Issue ids (use exactly): housing, cost-of-living, healthcare, education, economy-jobs, taxes-budget,
resources-energy, environment-climate, public-safety, drugs-addiction, reconciliation, transportation,
childcare-families, seniors, governance.

## meta.json
```json
{"version": 1, "updatedAt": "2026-10-17T00:00:00Z", "parliament": 43, "session": 3,
 "seatCount": 93, "nextElection": "2028-10-21", "sources": [...]}
```

## candidates2026.json  (44th general election — writ Sept 22 2026, voting day Oct 24 2026)
```json
[{
  "id": "vancouver-point-grey--david-eby", "name": "David Eby",
  "ridingId": "vancouver-point-grey",        // same riding slugs as ridings.json
  "partyId": "ndp",                          // ndp | conservative | green | independent | other party slugs
  "partyName": "BC NDP",                     // as printed on the ballot / Elections BC list
  "incumbent": true, "mlaId": "david-eby",   // mlaId only if they were an MLA at dissolution
  "occupation": "...", "bio": "1–3 neutral factual sentences from the candidate's or party's official page",
  "website": null, "email": null, "phone": null, "photoUrl": null,
  "social": {"x": null, "facebook": null, "instagram": null},
  "sources": ["https://elections.bc.ca/..."]
}]
```
