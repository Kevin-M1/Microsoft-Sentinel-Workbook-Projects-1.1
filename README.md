# Entra ID (Azure) Authentication Failures Geo-Map — Sentinel Workbook + KQL

This repo documents exactly what I did:
- Built a **Microsoft Sentinel Workbook** to visualize **failed Entra ID (Azure AD) authentications** on a **world map**.
- Wrote a **KQL** query over `SigninLogs` to count failed logins per **Identity** and **geolocation** (City/Country/Latitude/Longitude).
- Displayed results both as a **map** and a **table**.

## Screenshots
- **Workbook world map**
  <!-<img width="1934" height="1056" alt="image" src="https://github.com/user-attachments/assets/06ae6ac3-a482-4887-a4da-af48fe1163df" />

  

- **KQL results table**
  <img width="1963" height="1053" alt="image" src="https://github.com/user-attachments/assets/b297a0fb-6cc5-4f31-b534-51bb5e33efa4" />

  

## Environment
- **Platform**: Microsoft Sentinel (Azure)
- **Data source**: `SigninLogs`
- **Workbook**: *Entra ID (Azure) Authentication Failures*
- **Time range**: *Last 24 hours*

## KQL Used
```kusto
SigninLogs
| where ResultType != 0 and Identity !contains "-"    // Failures; exclude lab system IDs
| summarize LoginCount = count() by
    Identity,
    Latitude  = tostring(LocationDetails["geoCoordinates"]["latitude"]),
    Longitude = tostring(LocationDetails["geoCoordinates"]["longitude"]),
    City      = tostring(LocationDetails["city"]),
    Country   = tostring(LocationDetails["countryOrRegion"])
| order by LoginCount desc
| project Identity, Latitude, Longitude, City, Country, LoginCount,
         friendly_label = strcat(Identity, " - ", City, ", ", Country)
