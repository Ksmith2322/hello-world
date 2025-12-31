```dql
// Global Site Health Overview (Dot Map)
// Steps 1–5 implemented:
// 1) site_id added (single join key, matches tag exactly)
// 2) categoryRank retained + optional table sort/fields block included
// 3) expectedDevices used (set per-site; adjust values as you scale)
// 4) impact placeholder included (synthetic/golden-device/WAN can be layered later)
// 5) scalable "copy/paste site template" included at bottom

// ---------------------------
// Site 1 — BEA-ADV
// ---------------------------
fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| summarize deviceCount = count()

// Step 3: expectedDevices (set to realistic normal for the site)
| fieldsAdd expectedDevices = 1

| fieldsAdd coveragePct =
    if(condition: expectedDevices == 0,
       then: 0.0,
       else: toDouble(deviceCount) / toDouble(expectedDevices))

// NoData / Warning / Good based on coverage
| fieldsAdd category =
    if(condition: deviceCount == 0,
       then: "NoData",
       else: if(condition: coveragePct < 0.8,
                then: "Warning",
                else: "Good"))

// Step 2: numeric ranking (for sorting in a table tile)
| fieldsAdd categoryRank =
    if(condition: category == "NoData",
       then: 0,
       else: if(condition: category == "Warning",
                then: 1,
                else: 2))

// Step 1: standardized join/display fields
| fieldsAdd
    site_id   = "site:BEA-ADV",
    site_code = "BEA-ADV",
    site_name = "Cleveland – BEA Advanced",
    region    = "AMER",
    owner     = "Network Ops"

// Coordinates + map label
| fieldsAdd
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087

// Step 4: impact placeholder (wire in later when you pick a source)
// Keep it numeric so you can chart/threshold it later
| fieldsAdd impactScore = 0

| fields name, site_id, site_code, site_name, region, owner, category, categoryRank, impactScore,
         deviceCount, expectedDevices, coveragePct, latitude, longitude

// ---------------------------
// Site 2 — CAR01-PD
// ---------------------------
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:CAR01-PD", haystack: tags)
    | summarize deviceCount = count()

    // Step 3
    | fieldsAdd expectedDevices = 1

    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | fieldsAdd category =
        if(condition: deviceCount == 0,
           then: "NoData",
           else: if(condition: coveragePct < 0.8,
                    then: "Warning",
                    else: "Good"))

    // Step 2
    | fieldsAdd categoryRank =
        if(condition: category == "NoData",
           then: 0,
           else: if(condition: category == "Warning",
                    then: 1,
                    else: 2))

    // Step 1
    | fieldsAdd
        site_id   = "site:CAR01-PD",
        site_code = "CAR01-PD",
        site_name = "Fort Mill – CAR01 PD",
        region    = "AMER",
        owner     = "Network Ops"

    | fieldsAdd
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451

    // Step 4 placeholder
    | fieldsAdd impactScore = 0

    | fields name, site_id, site_code, site_name, region, owner, category, categoryRank, impactScore,
             deviceCount, expectedDevices, coveragePct, latitude, longitude
]

// ---------------------------
// Step 2 (Table tile usage)
// Uncomment these 2 lines ONLY when you use this query in a Table tile.
// For the Dot Map tile, keep them commented.
// ---------------------------
// | sort categoryRank asc, coveragePct asc
// | fields site_code, site_name, region, owner, category, impactScore, deviceCount, expectedDevices, coveragePct, latitude, longitude

// ---------------------------
// Step 5 (Scalable site template)
// Copy/paste this append block for each new site and edit:
// - site_id / site_code / site_name / region / owner
// - expectedDevices
// - latitude / longitude
// ---------------------------
/*
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:SITE-CODE-HERE", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 10
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))
    | fieldsAdd category =
        if(condition: deviceCount == 0,
           then: "NoData",
           else: if(condition: coveragePct < 0.8,
                    then: "Warning",
                    else: "Good"))
    | fieldsAdd categoryRank =
        if(condition: category == "NoData",
           then: 0,
           else: if(condition: category == "Warning",
                    then: 1,
                    else: 2))
    | fieldsAdd
        site_id   = "site:SITE-CODE-HERE",
        site_code = "SITE-CODE-HERE",
        site_name = "Friendly Site Name Here",
        region    = "AMER",
        owner     = "Network Ops"
    | fieldsAdd
        name      = "SITE-CODE-HERE",
        latitude  = 00.0000,
        longitude = 00.0000
    | fieldsAdd impactScore = 0
    | fields name, site_id, site_code, site_name, region, owner, category, categoryRank, impactScore,
             deviceCount, expectedDevices, coveragePct, latitude, longitude
]
*/
```
