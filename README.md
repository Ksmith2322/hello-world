fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| lookup [
    fetch events
    | filter status == "OPEN"
    | summarize alertCount = count() by affectedEntity
] on affectedEntity == entity.id
| fields entity.name, dt.ip_addresses, alertCount
| fieldsAdd alertCount = if(isNull(alertCount), 0, alertCount)
| sort alertCount desc, entity.name asc
