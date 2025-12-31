fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| fields entity.name, dt.ip_Address
| sort entity.name asc
