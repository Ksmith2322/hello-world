// Global Site Health Overview (Dot Map)
// Combines:
// - Device visibility (coverage)
// - Synthetic availability (reachability)
// Produces: Critical / Warning / Good / NoData

// ===========================
// SITE 1 — BEA-ADV
// ===========================

// ---- Device visibility (what you already built) ----
fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| summarize deviceCount = count()
| fieldsAdd expectedDevices = 1
| fieldsAdd coveragePct =
    if(condition: expectedDevices == 0,
       then: 0.0,
       else: toDouble(deviceCount) / toDouble(expectedDevices))

// ---- Synthetic availability (NEW) ----
| append [
    fetch dt.synthetic.monitor
    | filter in(needle: "site:BEA-ADV", haystack: tags)
    // last 10 minutes is a good “is it up right now?” window
    | filter timeframe(from: now() - 10m, to: now())
    | summarize
        totalRuns   = count(),
        failedRuns  = countIf(condition: success == false)
    | fieldsAdd siteDown =
        if(condition: totalRuns == 0,
           then: 1,                 // no synthetic data = treat as down
           else: if(condition: failedRuns > 0,
                    then: 1,
                    else: 0))
]

// ---- Final site category (availability first, then visibility) ----
| fieldsAdd category =
    if(condition: siteDown == 1,
       then: "Critical",
       else: if(condition: deviceCount == 0,
                then: "NoData",
                else: if(condition: coveragePct < 0.8,
                         then: "Warning",
                         else: "Good")))

// ---- Sorting helper (for table views) ----
| fieldsAdd categoryRank =
    if(condition: category == "Critical",
       then: 0,
       else: if(condition: category == "NoData",
                then: 1,
                else: if(condition: category == "Warning",
                         then: 2,
                         else: 3)))

// ---- Site metadata + map placement ----
| fieldsAdd
    site_id   = "site:BEA-ADV",
    site_code = "BEA-ADV",
    site_name = "Cleveland – BEA Advanced",
    region    = "AMER",
    owner     = "Network Ops",
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087

| fields name, site_id, site_code, site_name, region, owner,
         category, categoryRank,
         siteDown, deviceCount, expectedDevices, coveragePct,
         latitude, longitude


// ===========================
// SITE 2 — CAR01-PD
// ===========================
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:CAR01-PD", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | append [
        fetch dt.synthetic.monitor
        | filter in(needle: "site:CAR01-PD", haystack: tags)
        | filter timeframe(from: now() - 10m, to: now())
        | summarize
            totalRuns  = count(),
            failedRuns = countIf(condition: success == false)
        | fieldsAdd siteDown =
            if(condition: totalRuns == 0,
               then: 1,
               else: if(condition: failedRuns > 0,
                        then: 1,
                        else: 0))
    ]

    | fieldsAdd category =
        if(condition: siteDown == 1,
           then: "Critical",
           else: if(condition: deviceCount == 0,
                    then: "NoData",
                    else: if(condition: coveragePct < 0.8,
                             then: "Warning",
                             else: "Good")))

    | fieldsAdd categoryRank =
        if(condition: category == "Critical",
           then: 0,
           else: if(condition: category == "NoData",
                    then: 1,
                    else: if(condition: category == "Warning",
                             then: 2,
                             else: 3)))

    | fieldsAdd
        site_id   = "site:CAR01-PD",
        site_code = "CAR01-PD",
        site_name = "Fort Mill – CAR01 PD",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451

    | fields name, site_id, site_code, site_name, region, owner,
             category, categoryRank,
             siteDown, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]

// Optional (TABLE tile):
// | sort categoryRank asc, coveragePct asc
