fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)

// ---- Join OPEN events back to devices (lookup command syntax) ----
| lookup [
    fetch events
    | filter status == "OPEN"
    | summarize
        critical = countIf(severityLevel == "CRITICAL"),
        warning  = countIf(severityLevel == "WARNING"),
        openTotal = count(),
      by:{ affectedEntity }
],
  sourceField: entity.id,
  lookupField: affectedEntity

// ---- Normalize nulls so all devices show cleanly ----
| fieldsAdd
    critical  = if(isNull(critical), 0, critical),
    warning   = if(isNull(warning), 0, warning),
    openTotal = if(isNull(openTotal), 0, openTotal)

// ---- Output fields ----
| fields entity.name, dt.ip_addresses, openTotal, critical, warning

// ---- Sort so the most broken devices appear first ----
| sort critical desc, warning desc, openTotal desc, entity.name asc
