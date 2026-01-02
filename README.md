fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| fields entity.id, entity.name, dt.ip_addresses

| lookup [
    fetch events
    | filter status == "OPEN"
    | filter severityLevel == "CRITICAL"
    | fields affectedEntity
] on affectedEntity == entity.id
| summarize critical = count() by entity.id, entity.name, dt.ip_addresses

| lookup [
    fetch events
    | filter status == "OPEN"
    | filter severityLevel == "WARNING"
    | fields affectedEntity
] on affectedEntity == entity.id
| summarize warning = count() by entity.id, entity.name, dt.ip_addresses
| fieldsAdd
    critical = if(isNull(critical), 0, critical),
    warning  = if(isNull(warning), 0, warning)
| sort critical desc, warning desc, entity.name asc
