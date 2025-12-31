// Global Site Health Overview (Dot Map)
// Adds live alert visibility using ACTIVE events (tenant-safe)


// ===========================
// SITE 1 — BEA-ADV
// ===========================

// ---- Device visibility ----
fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| summarize deviceCount = count()
| fieldsAdd expectedDevices = 1
| fieldsAdd coveragePct =
    if(condition: expectedDevices == 0,
       then: 0.0,
       else: toDouble(deviceCount) / toDouble(expectedDevices))

// ---- Live alert signal (NO timeframe) ----
| append [
    fetch events
    | filter status == "OPEN"
    | filter in(needle: "site:BEA-ADV", haystack: tags)
    | summarize alertCount = count()
]

// ---- Final site status ----
| fieldsAdd hasAlert =
    if(condition: alertCount > 0, then: 1, else: 0)

| fieldsAdd category =
    if(condition: hasAlert == 1,
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

// ---- Site metadata ----
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
         alertCount, deviceCount, expectedDevices, coveragePct,
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
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:CAR01-PD", haystack: tags)
        | summarize alertCount = count()
    ]

    | fieldsAdd hasAlert =
        if(condition: alertCount > 0, then: 1, else: 0)

    | fieldsAdd category =
        if(condition: hasAlert == 1,
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
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]

// Optional for table tile:
// | sort categoryRank asc, coveragePct asc
