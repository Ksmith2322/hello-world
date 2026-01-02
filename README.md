fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)

// CRITICAL events joined to each device
| lookup [
    fetch events
    | filter status == "OPEN"
    | filter severityLevel == "CRITICAL"
] on affectedEntity == entity.id
| fieldsAdd critical = count()

// WARNING events joined to each device
| lookup [
    fetch events
    | filter status == "OPEN"
    | filter severityLevel == "WARNING"
] on affectedEntity == entity.id
| fieldsAdd warning = count()

| fields entity.name, dt.ip_addresses, critical, warning
| sort critical desc, warning desc, entity.name asc
