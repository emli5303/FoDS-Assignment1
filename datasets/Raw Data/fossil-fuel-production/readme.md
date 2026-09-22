# Fossil fuel production - Data package

This data package contains the data that powers the chart ["Fossil fuel production"](https://ourworldindata.org/grapher/fossil-fuel-production?v=1&csvType=full&useColumnShortNames=false) on the Our World in Data website.

### Active Filters

A filtered subset of the full data was downloaded. The following filters were applied:

## CSV structure

Each row is an observation for an entity (usually a country or region) at a timepoint.

- "Entity" — the name of the entity, e.g. "United States".
- "Code" — our internal entity code. For most countries this is the [ISO alpha-3](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) code, e.g. "USA"; historical and other non-standard entities get a custom code.
- "Year" or "Day" — the timepoint. Annual data has a "Year" column holding an integer year; otherwise a "Day" column holds a date string in the form "YYYY-MM-DD".
- Every remaining column is a data column, each one a time series. Downloaded with the "full data" option each corresponds to one time series below; with "only selected data visible in the chart" they are transformed depending on the chart type, so the correspondence may be less direct.


## Metadata.json structure

The .metadata.json file contains metadata about the data package. The "charts" key contains information to recreate the chart, like the title, subtitle etc. The "columns" key contains information about each of the columns in the csv, like the unit, timespan covered, citation for the data etc.

## How we process data at Our World in Data

Our World in Data is almost never the original producer of the data - almost all of the data we use has been compiled by others. If you want to re-use data, it is your responsibility to ensure that you adhere to the sources' license and to credit them correctly. Please note that a single time series may have more than one source - e.g. when we stitch together data from different time periods by different producers or when we calculate per capita metrics using population data from a second source.

Preparing this data involves several processing steps. Depending on the data, this can include standardizing country names and world region definitions, converting units, calculating derived indicators such as per capita measures, as well as adding or adapting metadata such as the name or the description given to an indicator.
[Read about our data pipeline](https://docs.owid.io/projects/etl/).

## Detailed information about each time series


### Coal production
Last updated: June 30, 2026  
Next expected update: June 2027  
Date range: 1981–2025  
Unit: terawatt-hours  
Source: Energy Institute – Statistical Review of World Energy (2026) – with major processing by Our World in Data  

#### How to cite this data

Energy Institute – Statistical Review of World Energy (2026) – with major processing by Our World in Data

#### How this data is described by its producers
Commercial solid fuels only, i.e. bituminous coal and anthracite (hard coal), and lignite and brown (sub-bituminous) coal, and other commercial solid fuels. Includes coal produced for Coal-to-Liquids and Coal-to-Gas transformations.


### Oil production
Last updated: June 30, 2026  
Next expected update: June 2027  
Date range: 1965–2025  
Unit: terawatt-hours  
Source: Energy Institute – Statistical Review of World Energy (2026) – with major processing by Our World in Data  

#### How to cite this data

Energy Institute – Statistical Review of World Energy (2026) – with major processing by Our World in Data

#### How this data is described by its producers
Includes crude oil, shale oil, oil sands, condensates (lease condensate or gas condensates that require further refining) and NGLs (natural gas liquids - ethane, LPG and naphtha separated from the production of natural gas). Excludes liquid fuels from other sources such as biofuels and synthetic derivatives of coal and natural gas. This also excludes liquid fuel adjustment factors such as refinery processing gain. Excludes oil shales/kerogen extracted in solid form.

#### Notes on our processing step for this indicator
* Oil production is approximately converted from million tonnes to energy using a standard average oil-equivalent conversion factor of 41.868 petajoules per million tonnes. We then convert 1 petajoule to 0.278 terawatt-hours.


### Gas production
Excludes gas flared or recycled. Includes natural gas produced for Gas-to-Liquids transformation.
Last updated: June 30, 2026  
Next expected update: June 2027  
Date range: 1970–2025  
Unit: terawatt-hours  
Source: Energy Institute – Statistical Review of World Energy (2026) – with major processing by Our World in Data  

#### How to cite this data

Energy Institute – Statistical Review of World Energy (2026) – with major processing by Our World in Data

#### How this data is described by its producers
Excludes gas flared or recycled. Includes natural gas produced for Gas-to-Liquids transformation.


## Sources

These are the sources behind the data in this package. Each time series above names the ones it draws on in its citation.

### Energy Institute – Statistical Review of World Energy

The Energy Institute Statistical Review of World Energy analyses data on world energy markets from the prior year.

Producer: Energy Institute  
Published: 2026-06-30  
Retrieved on: 2026-07-02  
Retrieved from: https://www.energyinst.org/statistical-review/  
License: © Energy Institute 2026 (https://www.energyinst.org/terms)  

Citation: Energy Institute – Statistical Review of World Energy (2026).

    