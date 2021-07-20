# README

This repository contains a series of notebooks used to analyze anonymized smartphone mobility data from the providers Unacast and Veraset in West Bengal, India. Similar code was used to analyze similar movements in Bangladesh.

Concomitant to the construction of these notebooks, we learned that the World Bank's Global Facility for Disaster Reduction and Recovery (GFDRR) was working on similar analysis for the more narrow use case of understanding disaster resilience. The GFDRR's effort was better funded and well-advanced on the creation of a general-purpose toolkit for analyzing mobility data using Dask (https://github.com/GFDRR/mobility_analysis_archive).

Recognizing the GFDRR's greater resources and continuity we made a strategic decision to narrow our coding ambitions and not duplicate their effort. Therefore, we have prepare a series of notebooks documenting workflows to prepare several general purpose mobility statistics using the Spark environment (more on that below). These may be useful to analysts who wish to work in Spark, need greater scale than Dask can provide, or are tackling use cases not covered by the mobility4resilience kit.

### Notebook purpose

These notebooks focused on use cases well matched to the needs and available data of the developing world. Most developed world analyses of mobility data have focused on daily movement rates, visitation rates to individual stores, and other analyses that assume huge, frequent mobility data sample sizes and rich secondary geospatial datasets of points of interest. Neither of these assumptions hold in South Asia.

However, South Asia witnesses far greater rates of seasonal internal migration, which can be tracked with sparser, less detailed data than is typical in the developed world. Short-term internal migration is a noted lacunae in available migration data with important consequences for development outcomes for vulnerable persons. We therefore elected to focus on creating measuers of such.

### Critical assumptions

Several assumptions underpin these notebooks:

* Analysts have access to sufficiently large samples of mobility data
* Analysts are comfortable working in Spark (PySpark and/or Scala) and vanilla Python
* Analysts have access to a Spark-based distributed processing environment

These assumptions are non-trivial and if they are not met, we recommend you stop here and source the appropriate technical and/or human resources before moving forward.

If these assumptions are met but you're a little confused about the specifics, reading the accompanying *Explaining_distributed_environments.md* doc to familiarize yourself with the high level ideas. Note that said doc is a superficial take on a complicated computer science problem and is meant only as an introduction to economists, geographers, and other potential mobility analysis profiles typically less familiar with such environments.

### Our process

Processing data, even non-spatial data, on servers is expensive and time consuming. Therefore these notebooks are structured to follow a three-stage process which minimizes server computation time:

* First, assign administrative ID codes to each individual row of data based on its location
* Second, run analysis aggregating results by location, time period, or movement pattern from every row ("big data" analysis) in Spark, saving out the results as local CSVs
* Finally, manipulate the aggregated results in vanilla Python on your local machine

Following this process will minimize the time and expense associated with servers and make your manager very happy. More details below

#### Assigning codes

We use GeoSpark to analyze each raw data point within a country-specific mobility dataset. The analysis identify which subnational area each point falls within and assigns it the corresponding administrative ID code. The subnational code so assigned should be of a sufficiently small admin level to provide all the spatial detail necessary for analysis. By assign ID codes in this way, we can assess changes in location using table operations instead of computationally taxing spatial operations. We store the processed data in parquet files stored in a Data Lake, partitioned by date.

#### Aggregating results

We use PySpark and Pandas within the server to aggregate results according to the desired analysis use case. The main cases explored here are:

* Descriptive statistics of daily/weekly/monthly/annual sample sizes per administrative area
* Weekly net movement in/out of an administrative area, as a % of the total sample population
* Weekly OD matrices showing where in/out movement goes to / comes from for each admin area.
* Net movement for smaller, specific time periods (e.g. around Cyclone Amphan)

#### Analzing movement

The non-Spark notebooks break down these aggregates into chunks useful for validating the dataest and exploring movement rates around notable events or agricultural calendar dates in the last two years. The notebooks include code for packaging said chunks into useful charts.

These are written in vanilla Python and require no addditional specialized skills.
