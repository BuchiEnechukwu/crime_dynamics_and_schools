# Crime dynamics around schools

This repository contains notebooks examining how recorded crime and incident patterns around secondary schools in Bradford vary between **school days and holidays** and across **distance from schools**. It uses two spatial definitions: straight line distance in metres and walking network catchments in minutes. The analysis asks whether any school day difference is more concentrated near schools and whether that pattern varies by offence or incident type.

These are observational comparisons of recorded events. A difference near a school does not establish that the school caused those events.

## Analysis paths

| Event data | Straight line distance | Walking catchment |
| --- | --- | --- |
| Crime records | [`1a. final_crimedata_analysis.ipynb`](notebooks/1a.%20final_crimedata_analysis.ipynb) and [`2a. final_crimetype_euclidian_multivariate_analysis.ipynb`](notebooks/2a.%20final_crimetype_euclidian_multivariate_analysis.ipynb) | [`1b. final_isochrones_crimedata_analysis.ipynb`](notebooks/1b.%20final_isochrones_crimedata_analysis.ipynb) and [`2b. final_isochrone_crimedata_multivariate_analysis.ipynb`](notebooks/2b.%20final_isochrone_crimedata_multivariate_analysis.ipynb) |
| Incident records | [`1c. final_incidentdata_analysis.ipynb`](notebooks/1c.%20final_incidentdata_analysis.ipynb) and [`2c. final_incidenttype_euclidian_multivariate_analysis.ipynb`](notebooks/2c.%20final_incidenttype_euclidian_multivariate_analysis.ipynb) | [`1d. final_isochrones_incidentdata_analysis.ipynb`](notebooks/1d.%20final_isochrones_incidentdata_analysis.ipynb) and [`2d. final_isochrone_incidenttype_multivariate_analysis.ipynb`](notebooks/2d.%20final_isochrone_incidenttype_multivariate_analysis.ipynb) |

The `1` notebooks describe and compare the event patterns. The `2` notebooks construct daily count tables and fit negative binomial regressions separately by event type and distance or time pair. Their core formula compares a **within** indicator, a **school day** indicator and their interaction. The interaction tests whether the school day association differs between the within and further groups; exponentiating its coefficient gives an interaction incidence rate ratio. The models use clustered covariance estimates. Interpret the results alongside the relevant model evaluation and multiple testing checks in each notebook.

## Notebook order and data dependencies

1. **Prepare the event data.** Run [`0. crimedata_preprocessing.ipynb`](notebooks/0.%20crimedata_preprocessing.ipynb) and/or [`0. incidentsdata_preprocessing.ipynb`](notebooks/0.%20incidentsdata_preprocessing.ipynb). They clean the respective event data, school locations and Bradford boundary, assign calendar variables, and create spatial event files linked to nearby schools.
2. **Generate walking catchments if needed.** [`school_isochrone_catchments.ipynb`](school_isochrone_catchments.ipynb) builds walking network polygons from a school input file using OSMnx. It considers walking speeds of 0.93, 1.04 and 1.15 m/s and travel times from 5 to 30 minutes. This step needs network access and can be time consuming.
3. **Match events to catchments.** Run [`0. isochrones_crimedata_preprocessing.ipynb`](notebooks/0.%20isochrones_crimedata_preprocessing.ipynb) and/or [`0. isochrones_incidentdata_preprocessing.ipynb`](notebooks/0.%20isochrones_incidentdata_preprocessing.ipynb) after the corresponding event preprocessing and catchment generation.
4. **Explore and compare patterns.** Run the relevant `1a`–`1d` notebook in the table above.
5. **Fit type specific models.** Run the corresponding `2a`–`2d` notebook.

The straight line models compare **250, 500, 750 and 1,000 m** within/further pairs. The walking models compare **5, 10, 15, 20, 25 and 30 minute** within/further pairs. The analysis notebooks also examine rates, offence or incident composition, and school day versus holiday comparisons. Some notebooks use Bradford deprivation data for additional descriptive analysis.

## Inputs and access

The notebooks refer to input names including `crime_data.csv`, `src_BD_IncidentData.csv`, `results.csv` (schools), `boundary.geojson`, `school_isocrone.csv` and, in some analyses, `bradford_imd.csv`. These data files are **not included** in this repository. Derived GeoJSON and CSV files are also absent. The `.gitignore` excludes CSV and GeoJSON files, so running a notebook will not automatically add those data to Git.

The crime preprocessing notebook explicitly selects records from **2019 and 2020**. Its school day/holiday classification uses a Bingley Grammar School calendar. Review the period and calendar assumptions in each preprocessing notebook before interpreting the comparisons; the 2020 period may be affected by pandemic disruption.

Only use data to which you have authorised access, and follow the source and research environment's rules for running notebooks and releasing outputs. The notebooks are a record of the analysis, not a downloadable dataset or a fully self contained reproduction package.

## Running the notebooks

The notebooks use relative input and output paths. Put approved inputs and generated intermediate files in the **working directory used by Jupyter**, or edit their paths before running cells. Typical outputs include `crime_nearest_school.geojson`, `incident_nearest_school.geojson`, `school_catchments.geojson`, `crime_isochrones.geojson` and `incident_isochrones.geojson`.

Dependencies visible in the notebooks include Python, Jupyter, pandas, NumPy, GeoPandas, Shapely, SciPy, scikit-learn, statsmodels, Matplotlib and seaborn. Catchment generation additionally uses OSMnx and NetworkX; some GeoJSON reads request PyArrow support. No lockfile or tested package versions are provided, so the environment and data access may need to be configured for the research setting.

## Scope and limitations

- Spatial association is not evidence of a causal school effect. Nearby land use, population movement, reporting practices and other factors may shape the observed patterns.
- A single school's calendar is a proxy for school days across the study area; other schools may differ.
- The 2019–2020 sample and the 2020 pandemic period limit generalisation to other years.
- The straight line and walking catchment analyses answer related but distinct proximity questions. Their bands should not be treated as interchangeable.
- The notebooks contain intermediate analysis and saved outputs; inspect calculations, model diagnostics and disclosure requirements before reporting results externally.
