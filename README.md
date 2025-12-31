// Global Site Health Overview (Dot Map)
// Live alert visibility using ACTIVE events + device coverage
//
// TEST (ONE SITE ONLY):
//   Set testMode for BEA-ADV to force a category for visualization checks.
//   testMode values:
//     0 = live logic (no override)
//     1 = force Critical
//     2 = force NoData
//     3 = force Warning
//     4 = force Good
//
// Note: CAR01-PD remains fully live (no test overrides).


// ===========================
// SITE 1 — BEA-ADV  (TEST ENABLED HERE ONLY)
// ===========================

// ---- Device visibility ----
fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| summarize deviceCount = count()

// TEST MODE (ONLY HERE)
| fieldsAdd testMode = 0

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

// TEST OVERRIDES (ONLY BEA-ADV)
// (Keeps everything numeric + safe)
| fieldsAdd hasAlert =
    if(condition: testMode == 1, then: 1, else: hasAlert)
| fieldsAdd deviceCount =
    if(condition: testMode == 2, then: 0, else: deviceCount)
| fieldsAdd coveragePct =
    if(condition: testMode == 3, then: 0.50, else: coveragePct)

| fieldsAdd category =
    if(condition: testMode == 4,
       then: "Good",
       else: if(condition: hasAlert == 1,
                then: "Critical",
                else: if(condition: deviceCount == 0,
                         then: "NoData",
                         else: if(condition: coveragePct < 0.8,
                                  then: "Warning",
                                  else: "Good"))))

| fieldsAdd categoryRank =
    if(condition: category == "Critical",
       then: 0,
       else: if(condition: category == "NoData",
                then: 1,
                else: if(condition: category == "Warning",
                         then: 2,
                         else: 3)))

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
// SITE 2 — CAR01-PD (LIVE ONLY)
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
// | fields site_code, site_name, region, owner, category, alertCount, deviceCount, expectedDevices, coveragePct, latitude, longitude



// ===================================================================
// TEMPLATE: Copy/paste this block to add a NEW SITE
// ===================================================================
/*
| append [
    // ---- Device visibility ----
    fetch `dt.entity.network:device`
    | filter in(needle: "site:SITE-CODE-HERE", haystack: tags)
    | summarize deviceCount = count()
    | fieldsAdd expectedDevices = 10
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))

    // ---- Live alert signal (OPEN events) ----
    | append [
        fetch events
        | filter status == "OPEN"
        | filter in(needle: "site:SITE-CODE-HERE", haystack: tags)
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
        site_id   = "site:SITE-CODE-HERE",
        site_code = "SITE-CODE-HERE",
        site_name = "Friendly Site Name Here",
        region    = "AMER",
        owner     = "Network Ops",
        name      = "SITE-CODE-HERE",
        latitude  = 00.0000,
        longitude = -00.0000

    | fields name, site_id, site_code, site_name, region, owner,
             category, categoryRank,
             alertCount, deviceCount, expectedDevices, coveragePct,
             latitude, longitude
]
*/
