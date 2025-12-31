```dql
// Global Site Health Overview (Dot Map)
// Includes: NoData vs Warning vs Good, expectedDevices, coveragePct,
// plus standardized site metadata (site_code/site_name/region/owner)
// NOTE: Uses fetch `dt.entity.network:device` (your confirmed working entity)

// ---------- Site 1 — BEA-ADV ----------
fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| summarize deviceCount = count()
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
// NEW: numeric rank for sorting (useful for the companion table tile)
| fieldsAdd categoryRank =
    if(condition: category == "NoData",
       then: 0,
       else: if(condition: category == "Warning",
                then: 1,
                else: 2))
| fieldsAdd
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087
// NEW: standardized site metadata (safe)
| fieldsAdd
    site_code = "BEA-ADV",
    site_name = "Cleveland – BEA Advanced",
    region    = "AMER",
    owner     = "Network Ops"
| fields name, site_code, site_name, region, owner, category, categoryRank, deviceCount, expectedDevices, coveragePct, latitude, longitude

// ---------- Site 2 — CAR01-PD ----------
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:CAR01-PD", haystack: tags)
    | summarize deviceCount = count()
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
    // NEW
    | fieldsAdd categoryRank =
        if(condition: category == "NoData",
           then: 0,
           else: if(condition: category == "Warning",
                    then: 1,
                    else: 2))
    | fieldsAdd
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451
    // NEW
    | fieldsAdd
        site_code = "CAR01-PD",
        site_name = "Fort Mill – CAR01 PD",
        region    = "AMER",
        owner     = "Network Ops"
    | fields name, site_code, site_name, region, owner, category, categoryRank, deviceCount, expectedDevices, coveragePct, latitude, longitude
]

// Optional (for a TABLE tile): uncomment these 2 lines to get “top offenders” ordering
// | sort categoryRank asc, coveragePct asc
// | fields site_code, site_name, region, owner, category, deviceCount, expectedDevices, coveragePct, latitude, longitude
```
