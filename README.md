// Global Site Health Overview (Dot Map)
// Category is derived from how many devices are reporting per site

// ---------- Site 1 — BEA-ADV (Cleveland area) ----------
fetch dt.entity.network_device
| filter arrayContains(tags, "site:BEA-ADV")
| summarize deviceCount = count()
| fieldsAdd category = case(
    deviceCount == 0, "Critical",
    deviceCount < 1,  "Warning",
    true,             "Good"
)
| fieldsAdd
    name      = "BEA-ADV",
    latitude  = 41.4839,
    longitude = -81.5087
| fields name, category, deviceCount, latitude, longitude

// ---------- Site 2 — CAR01-PD (Fort Mill) ----------
| append [
    fetch dt.entity.network_device
    | filter arrayContains(tags, "site:CAR01-PD")
    | summarize deviceCount = count()
    | fieldsAdd category = case(
        deviceCount == 0, "Critical",
        deviceCount < 1,  "Warning",
        true,             "Good"
    )
    | fieldsAdd
        name      = "CAR01-PD",
        latitude  = 35.0074,
        longitude = -80.9451
    | fields name, category, deviceCount, latitude, longitude
]
