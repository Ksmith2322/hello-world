// Global Site Health Overview (Dot Map)
// Status is derived from how many network devices Dynatrace currently sees per site tag.

// ---------- Site 1 — BEA-ADV (Cleveland area) ----------
fetch dt.entity.network_device
| fieldsAdd tags
| filter matchesValue(tags, "site:BEA-ADV")
| summarize deviceCount = count()
| fieldsAdd expectedMin = 1
| fieldsAdd category =
    if(deviceCount == 0, "Critical",
       if(deviceCount < expectedMin, "Warning", "Good"))
| fieldsAdd
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087
| fields name, category, deviceCount, latitude, longitude

// ---------- Site 2 — CAR01-PD (Fort Mill) ----------
| append [
    fetch dt.entity.network_device
    | fieldsAdd tags
    | filter matchesValue(tags, "site:CAR01-PD")
    | summarize deviceCount = count()
    | fieldsAdd expectedMin = 1
    | fieldsAdd category =
        if(deviceCount == 0, "Critical",
           if(deviceCount < expectedMin, "Warning", "Good"))
    | fieldsAdd
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451
    | fields name, category, deviceCount, latitude, longitude
]

// ---------- Add more sites below (copy/paste this block) ----------
/*
| append [
    fetch dt.entity.network_device
    | fieldsAdd tags
    | filter matchesValue(tags, "site:SITE-CODE-HERE")
    | summarize deviceCount = count()
    | fieldsAdd expectedMin = 5
    | fieldsAdd category =
        if(deviceCount == 0, "Critical",
           if(deviceCount < expectedMin, "Warning", "Good"))
    | fieldsAdd
        name      = "SITE-CODE-HERE",
        latitude  = 00.0000,
        longitude = -00.0000
    | fields name, category, deviceCount, latitude, longitude
]
*/
