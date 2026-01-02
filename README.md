fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)

// ---- Join OPEN events back to devices (explicit entity join) ----
| lookup [
    fetch events
    | filter status == "OPEN"
    | summarize
        critical = countIf(severityLevel == "CRITICAL"),
        warning  = countIf(severityLevel == "WARNING")
      by affectedEntity
] on affectedEntity == entity.id

// ---- Normalize nulls so all devices show cleanly ----
| fieldsAdd
    critical = if(isNull(critical), 0, critical),
    warning  = if(isNull(warning), 0, warning)

// ---- Output fields ----
| fields
    entity.name,
    dt.ip_addresses,
    critical,
    warning

// ---- Sort so the most broken devices appear first ----
| sort critical desc, warning desc, entity.name asc
