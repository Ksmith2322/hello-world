// Global Site Health Overview (Dot Map)
// Category is derived from how many network devices Dynatrace currently sees per site tag.

// ---------- Site 1 — BEA-ADV ----------
fetch dt.entity.network_device
| filter in(needle: "site:BEA-ADV", haystack: tags)
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

// ---------- Site 2 — CAR01-PD ----------
| append [
    fetch dt.entity.network_device
    | filter in(needle: "site:CAR01-PD", haystack: tags)
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
