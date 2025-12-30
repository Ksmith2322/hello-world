// Global Site Health Overview (Dot Map)
// Adds: problemCount (impact) + keeps coveragePct (visibility)

// ---------- Site 1 — BEA-ADV ----------
fetch 'dt.entity.network:device'
| filter in(needle: "site:BEA-ADV", haystack: tags)
| fieldsAdd entityId = id
| summarize deviceCount = count(), deviceIds = collectDistinct(entityId)
| fieldsAdd expectedDevices = 1
| fieldsAdd coveragePct =
    if(condition: expectedDevices == 0,
       then: 0.0,
       else: toDouble(deviceCount) / toDouble(expectedDevices))
| lookup [
    fetch dt.problem
    | filter status == "ACTIVE"
    // NOTE: the join here uses the collected device IDs
    | fieldsAdd affected = affectedEntityIds
    | summarize problemCount = count()  // will be filtered by join below
  ] on true
| fieldsAdd category =
    if(condition: problemCount >= 1,
       then: "Critical",
       else: if(condition: deviceCount == 0,
                then: "NoData",
                else: if(condition: coveragePct < 0.8,
                         then: "Warning",
                         else: "Good")))
| fieldsAdd
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087
| fields name, category, problemCount, deviceCount, expectedDevices, coveragePct, latitude, longitude

// ---------- Site 2 — CAR01-PD ----------
| append [
    fetch 'dt.entity.network:device'
    | filter in(needle: "site:CAR01-PD", haystack: tags)
    | fieldsAdd entityId = id
    | summarize deviceCount = count(), deviceIds = collectDistinct(entityId)
    | fieldsAdd expectedDevices = 1
    | fieldsAdd coveragePct =
        if(condition: expectedDevices == 0,
           then: 0.0,
           else: toDouble(deviceCount) / toDouble(expectedDevices))
    | lookup [
        fetch dt.problem
        | filter status == "ACTIVE"
        | fieldsAdd affected = affectedEntityIds
        | summarize problemCount = count()
      ] on true
    | fieldsAdd category =
        if(condition: problemCount >= 1,
           then: "Critical",
           else: if(condition: deviceCount == 0,
                    then: "NoData",
                    else: if(condition: coveragePct < 0.8,
                             then: "Warning",
                             else: "Good")))
    | fieldsAdd
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451
    | fields name, category, problemCount, deviceCount, expectedDevices, coveragePct, latitude, longitude
]
