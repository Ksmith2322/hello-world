Absolutely — here’s a **clear, concise write-up** you can paste directly into Confluence, the dashboard description, or a README.

---

# Global Site Health Overview – Dot Map (DQL)

**Purpose:**
Provide a single, global, real-time view of network site health in Dynatrace using device presence and active alerts, visualized on a geographic dot map.

---

## What this script does (high level)

This DQL script builds a **site-level health model** for multiple network locations and renders each site as a dot on a map.
Each dot represents **one physical site**, color-coded by health and sized by device presence.

The script:

* Counts network devices per site
* Detects active (OPEN) alerts affecting those devices
* Calculates basic coverage health
* Assigns a health category (Critical / Warning / Good / NoData)
* Outputs latitude and longitude so Dynatrace can place the site on a map

---

## How the script works (step-by-step)

### 1. Device discovery per site

For each site block:

```dql
fetch `dt.entity.network:device`
| filter in(needle: "site:SITE-CODE", haystack: tags)
| summarize deviceCount = count()
```

This:

* Finds all **network devices** with a matching `site:<SITE-CODE>` tag
* Counts how many devices are currently reporting

---

### 2. Coverage calculation

```dql
| fieldsAdd expectedDevices = 1
| fieldsAdd coveragePct = deviceCount / expectedDevices
```

This provides a simple coverage metric:

* Used to detect partial visibility
* Enables “Warning” states when coverage drops below threshold

> `expectedDevices` is currently static per site and can be tuned later.

---

### 3. Active alert detection

```dql
| append [
    fetch events
    | filter status == "OPEN"
    | filter in(needle: "site:SITE-CODE", haystack: tags)
    | summarize alertCount = count()
]
```

This:

* Pulls **live OPEN events**
* Limits them to devices at the same site
* Counts how many active alerts exist

No time window is used, ensuring **current state only**.

---

### 4. Health classification logic

```dql
| fieldsAdd category =
    if(hasAlert == 1, "Critical",
       if(deviceCount == 0, "NoData",
          if(coveragePct < 0.8, "Warning", "Good")))
```

Each site is classified as:

| Category     | Meaning                   |
| ------------ | ------------------------- |
| **Critical** | One or more active alerts |
| **Warning**  | Partial device visibility |
| **NoData**   | No devices reporting      |
| **Good**     | Fully healthy             |

A numeric `categoryRank` is also added for sorting and tables.

---

### 5. Site metadata & map placement

Each site block adds:

```dql
| fieldsAdd
    site_id,
    site_code,
    site_name,
    address,
    region,
    owner,
    latitude,
    longitude
```

This allows:

* Accurate geographic placement
* Human-readable labels
* Filtering/grouping by region or owner later

---

### 6. Final output

Each site emits **one row** with:

* Health status
* Alert count
* Device count
* Coverage percentage
* Latitude / longitude

These rows are combined using `append` into a **single dataset**.

---

## How this is used in Dynatrace

### Dot Map visualization

* **Latitude / Longitude** → map placement
* **Color** → health category
* **Size** → device count or alert count
* **Label** → site code

### Troubleshooting flow

1. Engineer views global map
2. Identifies unhealthy site
3. Clicks site dot
4. Drills into:

   * Network devices
   * Active problems
   * Events
   * Interfaces

---

## Why this approach was chosen

* No reliance on dashboard variables (tenant-safe)
* Scales to many sites
* Uses Dynatrace’s **entity-first troubleshooting model**
* Easy to extend with new sites using a standard template
* Works reliably with tag-based automation

---

## Future enhancements (optional)

* Auto-calculate expectedDevices
* Add interface-level health signals
* Region-level rollups
* Replace manual site blocks with a lookup-driven model

---

If you want, I can also provide:

* A **one-paragraph executive summary**
* A **diagram explaining the flow**
* A **commented version of the DQL** inline

Just tell me where this write-up is going.
