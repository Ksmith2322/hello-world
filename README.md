fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| append [
    fetch events
    | filter status == "OPEN"
    | summarize alertCount = count() by affectedEntity
]
| fields entity.name, dt.ip_addresses, alertCount
| fieldsAdd alertCount = if(isNull(alertCount), 0, alertCount)
| sort alertCount desc, entity.name asc
