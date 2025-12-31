fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| fields entity.name, ipAddress
