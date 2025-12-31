fetch `dt.entity.network:device`
| filter in(needle: $site, haystack: tags)
| fields entity.name, ipAddress, tags
| sort entity.name asc
