// Global Site Health Overview (Dot Map)
// Status is derived from how many network devices Dynatrace currently sees per site tag.

// ---------- Site 1 — BEA-ADV (Cleveland area) ----------
fetch dt.entity.network_device
| fieldsAdd tags
| filter matchesValue(value: tags, pattern: "site:BEA-ADV")
| summarize deviceCount = count()
| fieldsAdd expectedMin = 1
| fieldsAdd category =
    if(condition: deviceCount == 0,
       then: "Critical",
       else: if(condition: deviceCount < expectedMin,
                then: "Warning",
                else: "Good"))
| fieldsAdd
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087
| fields name, category, deviceCount, latitude, longitude

// ---------- Site 2 — CAR01-PD (Fort Mill) ----------
| append [
    fetch dt.entity.network_device
    | fieldsAdd tags
    | filter matchesValue(value: tags, pattern: "site:CAR01-PD")
    | summarize deviceCount = count()
    | fieldsAdd expectedMin = 1
    | fieldsAdd category =
        if(condition: deviceCount == 0,
           then: "Critical",
           else: if(condition: deviceCount < expectedMin,
                    then: "Warning",
                    else: "Good"))
    | fieldsAdd
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451
    | fields name, category, deviceCount, latitude, longitude
]
