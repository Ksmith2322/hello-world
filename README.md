// Global Site Health Overview (Dot Map)
// Adds: label + bubbleSize (safe enhancements)

// ---------- Site 1 — BEA-ADV ----------
fetch 'dt.entity.network:device'
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
| fieldsAdd
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087
// ⬇️ NEW: readable label + bubble sizing
| fieldsAdd
    label      = name + " (" + toString(deviceCount) + "/" + toString(expectedDevices) + ")",
    bubbleSize = deviceCount
| fields name, label, category, bubbleSize, deviceCount, expectedDevices, coveragePct, latitude, longitude

// ---------- Site 2 — CAR01-PD ----------
| append [
    fetch 'dt.entity.network:device'
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
    | fieldsAdd
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451
    // ⬇️ NEW
    | fieldsAdd
        label      = name + " (" + toString(deviceCount) + "/" + toString(expectedDevices) + ")",
        bubbleSize = deviceCount
    | fields name, label, category, bubbleSize, deviceCount, expectedDevices, coveragePct, latitude, longitude
]
