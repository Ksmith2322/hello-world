fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| fields entity.name, dt.ip_Address
| sort entity.name asc


fetch events
| filter status == "OPEN"
| fields event.name, severityLevel, affectedEntity


fetch `dt.entity.network:interface`
| fields entity.name, state, speed, macAddress
