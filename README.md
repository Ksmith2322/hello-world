B) Create the “Site Devices” tile (device list)
Tile: Device table (works and you already validated it)

Use this as the device list tile DQL (example for testing):

fetch `dt.entity.network:device`
| filter in(needle: "site:BEA-ADV", haystack: tags)
| fields entity.name, dt.ip_addresses, tags
| sort entity.name asc


Once you confirm the map interaction can open entities dynamically, you can remove the hardcode and rely on the entity context (try this next):

fetch `dt.entity.network:device`
| fields entity.name, dt.ip_addresses, tags
| sort entity.name asc


If that “no filter” version still shows everything, then the “Site Devices” dashboard is not inheriting scope, and you should stick to Open entities from the map (because that does scope correctly).
