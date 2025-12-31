// Global Site Health Overview (Dot Map)
// Adds live alert visibility using OPEN events + device coverage


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
    address   = "",
    region    = "AMER",
    owner     = "Network Ops",
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087

| fields name, site_id, site_code, site_name, address, region, owner,
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
        address   = "",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451

    | fields name, site_id, site_code, site_name, address, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]


// ===========================
// SITE 3 — ABE-ADV
// ===========================
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:ABE-ADV", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | append [
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:ABE-ADV", haystack: tags)
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
        site_id   = "site:ABE-ADV",
        site_code = "ABE-ADV",
        site_name = "Aberdeen – ABE Advanced",
        address   = "125 Brown Co Hwy 19 S, Aberdeen, SD 57401",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "ABE-ADV",
        latitude  = 45.464700,
        longitude = -98.486480

    | fields name, site_id, site_code, site_name, address, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]


// ===========================
// SITE 4 — ADA-ADV
// ===========================
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:ADA-ADV", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | append [
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:ADA-ADV", haystack: tags)
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
        site_id   = "site:ADA-ADV",
        site_code = "ADA-ADV",
        site_name = "Ada – ADA Advanced",
        address   = "Ada, MI",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "ADA-ADV",
        latitude  = 42.9541963,
        longitude = -85.4889121

    | fields name, site_id, site_code, site_name, address, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]


// ===========================
// SITE 5 — ALB-ADV
// ===========================
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:ALB-ADV", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | append [
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:ALB-ADV", haystack: tags)
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
        site_id   = "site:ALB-ADV",
        site_code = "ALB-ADV",
        site_name = "Albany – ALB Advanced",
        address   = "6 Tower Place, Albany, NY 12203",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "ALB-ADV",
        latitude  = 42.6855,
        longitude = -73.8411

    | fields name, site_id, site_code, site_name, address, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]


// ===========================
// SITE 6 — ALCLL-ADV
// ===========================
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:ALCLL-ADV", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | append [
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:ALCLL-ADV", haystack: tags)
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
        site_id   = "site:ALCLL-ADV",
        site_code = "ALCLL-ADV",
        site_name = "Lakeland – ALCLL Advanced",
        address   = "Lakeland, FL",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "ALCLL-ADV",
        latitude  = 28.039465,
        longitude = -81.949806

    | fields name, site_id, site_code, site_name, address, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]
// ===========================
// SITE 7 — ALCVA-ADV
// ===========================
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:ALCVA-ADV", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | append [
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:ALCVA-ADV", haystack: tags)
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
        site_id   = "site:ALCVA-ADV",
        site_code = "ALCVA-ADV",
        site_name = "Viera – ALCVA Advanced",
        address   = "Viera, FL",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "ALCVA-ADV",
        latitude  = 28.2664,
        longitude = -80.7289

    | fields name, site_id, site_code, site_name, address, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]


// ===========================
// SITE 8 — ALCWH-ADV
// ===========================
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:ALCWH-ADV", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | append [
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:ALCWH-ADV", haystack: tags)
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
        site_id   = "site:ALCWH-ADV",
        site_code = "ALCWH-ADV",
        site_name = "Winter Haven – ALCWH Advanced",
        address   = "Winter Haven, FL",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "ALCWH-ADV",
        latitude  = 28.022243,
        longitude = -81.732857

    | fields name, site_id, site_code, site_name, address, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]


// ===========================
// SITE 9 — ATL-ADV
// ===========================
| append [
    fetch `dt.entity.network:device`
    | filter in(needle: "site:ATL-ADV", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    | append [
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:ATL-ADV", haystack: tags)
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
        site_id   = "site:ATL-ADV",
        site_code = "ATL-ADV",
        site_name = "Atlanta – ATL Advanced",
        address   = "5555 Glenridge Connector, Ste 1075, Atlanta, GA 30342",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "ATL-ADV",
        latitude  = 33.90409,
        longitude = -84.35982

    | fields name, site_id, site_code, site_name, address, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]


// Optional for table tile:
// | sort categoryRank asc, coveragePct asc
