## Handbook of the **Temperature and Energy Justice Map**ping Tool

### 1. Introduction

#### 1.1 Background

Climate change is leading to increased severity of extreme ambient temperatures which is associated with dramatic health harms.$^{1-3}$ Evidence shows that increasing temperatures are not experienced evenly instead communities of color experience higher summertime temperatures likely due to residential segregation$^{4-7}$ and may be warming faster.$^{8}$ Air conditioning is one of the only sources of personal adaptation to buffer extreme temperatures but communities of color are also likely to experience energy insecurity$^{9-11}$. Energy insecurity represents the inability to pay energy bills or rationing energy use for other household needs$^{10}$. The environmental injustice of temperature disparities and the energy injustice of energy insecurity calls for equitable solutions that protect minoritized populations. Energy efficiency upgrades represent a climate-equitable strategy to protect human health while reducing energy demand from cooling. However access to efficient technologies represents another area of injustice for marginalized communities$^{12–14}$. Equitable access to weatherization and energy efficiency for low-income communities of color is the mission of organizations like the Green and Healthy Homes Initiative (GHHI). Our goal is to use Earth observations to create the **Temperature and Energy Justice Mapping (TEJM)** tool to inform environmental and energy justice in New York. The tool scalable elsewhere aims to help stakeholders identify areas experiencing temperature disparities and energy insecurity. Consequently, these homes would benefit from energy efficiency upgrades, thus advancing equity and environmental justice.

Compared to previous mapping tools, our product has the following advantages:

- We present the population-weighted and race-specific temperature data that are derived from statistical methods, instead of visualizing observed data only. This allows us to identify the racial disparities in different counties.
- Instead of visualizing energy data, we propose an energy insecurity index for each Zip Code Tabulation Area (ZCTA), which enables us to better understand the energy consumption pattern for different geographical areas.
- In addition to temperature and energy data, our map incorporates demographic information in terms of population size, median household income, and race-ethnicity, as well as the social vulnerability index (SVI), to help users quickly locate the county of their interest.

#### 1.2 Concepts

- **Heat Index**: **Heat Index** represents the impact of heat on the human body - **how hot we feel**. It takes into account both the temperature and relative humidity. This is a good variable to look at when thinking about heat and human health.

- **Heat Index Cooling Degree Days (HICDD)**: **HICDD** is calculated as $HICDD = \sum_{i}^n \text{HeatIndex}_i - 65 \, ^\circ\mathrm{F}$, where $i$ represents the day of the season summed over all *n* days of the cooling season. In this case, we calculated **HICDD** for each warm season, which involves 153 days in total.

- **Land Surface Temperature**: **Land Surface Temperature** is the temperature of the surface as measured from satellites. We use this as a variable that is likely more relevant to building level energy use, e.g., how much energy will it take to cool a building that is a certain temperature?

- **Land Surface Temperature Cooling Degree Days (LSTCDD)**: **LSTCDD** is calculated as $LSTCDD = \sum_{i}^n \text{LST}_i - 65 \, ^\circ\mathrm{F}$, where $i$ represents the day of the season summated over all *n* days of the cooling season. In this case, we calculated **LSTCDD** for each month of the warm season, which involves 30.6 days on average.

- **Social Vulnerability Index (SVI)**: **SVI**, as developed by CDC, refers to the potential negative effects on communities caused by external stresses on human health. Such stresses include natural or human-caused disasters, or disease outbreaks. A larger **SVI** indicates more vulnerability. Overall SVI was derived from social factors in terms of Socioeconomic Status, Household Composition & Disability, Minority Status & Language and Housing type & Transportation. Household SVI was derived solely based on Household Composition & Disability. Further details can be accessed through this [link](https://www.atsdr.cdc.gov/placeandhealth/svi/documentation/SVI_documentation_2018.html).

- **Zip Code Tabulation Area (ZCTA)**: **ZCTA**s (pronounced *zik-tahs*) are a geographic product of the U.S. Census Bureau created to allow mapping, display, and geographic analyses of the United States Postal Service (USPS) Zone Improvement Plan (ZIP) Codes dataset. These areas are different from zip code areas. In this project, we utilize ZCTAs since there is corresponding population data. We provide a list of zip codes that intersect with each ZCTA as reference. 

- **Energy Rationing**: Energy rationing occurs when financial concerns lead individuals to forego or limit the use of air conditioning due to worries about energy bill costs. In this project, we conducted a quantile regression analysis using HICDD to assess energy insecurity within a ZCTA area. We take evidence of rationing as a marker of summer time energy insecurity. Specifically, if the observed energy usage in a given month is lower than our prediction for the same month, we label the ZCTA area as energy insecure for that month.

  

#### 1.3 Overview

The TEJM tool involves the following information for users: 

- county-level temperature measures:

  - county-level HICDD from 2010 to 2019.

  - county-level LSTCDD from 2010 to 2019.
  - county-level racial disparities of HICDD from 2010 to 2019.
  - county-level racial disparities of LSTCDD from 2010 to 2019.
  - county-level demographic information, including population size, number of households and median incomes from 2010 to 2019.
- county-level SVI:
  - Household SVI in 2018.
  - Overall SVI in 2018.
- Zip code tabulation area (ZCTA) level energy insecurity.
  - Proportion of energy insecure months derived from energy data through 2016 to 2020.
  - ZCTA level demographic information, including average population size and proportions of elders (age > 65) and youngs (age < 18).



## 2. Data & Methods

### 2.1 Datasets

| Data                         |                        Source                        |                            Notes                             |
| :--------------------------- | :--------------------------------------------------: | :----------------------------------------------------------: |
| **Shapefiles**               |                 2010 decennial data                  |        Downloaded using ***tidycensus*** package in R        |
| **Energy Data**              | UER community level energy and zip code level energy | Missing data were imputed using adjusted dasymetric apportionment method. Data can be accessed at https://data.ny.gov/resource |
| **HICDD**                    |                 Daymet climate data                  |         Downloaded using ***daymetr*** package in R          |
| **LSTCDD**                   |            MODIS Land Surface Temperature            |           Downloaded using ***luna*** package in R           |
| **Demographic  Information** |        5-year American Community Survey Data         |        Downloaded using ***tidycensus*** package in R        |

### 2.2 Statistical Methods

#### HICDD & LSTCDD

Census-tract level HICDD data were derived from the Daymet dataset. The generalized additive mixed model (**GAMM**) was employed to make predictions for race-specific HICDDs. Specifically, the model was formularized as
$$
\begin{aligned}
HICDD_{ij} &= f_{cubic}(\text{Proportion of non-Hispanic Black}_{ij}) + f_{cubic}(\text{Proportion of non-Hispanic White}_{ij}) 
\\&+ f_{cubic}(\text{Proportion of Asian}_{ij}) + f_{cubic}(\text{Proportion of Hispanic}_{ij}) 
\\&+ f_{cubic}(\text{Proportion of Other}_{ij}) + f_{gp}(\text{coordinates}_i) + b_{j}
\end{aligned}
$$
Where $i$ represents the census tract, $j$ denotes the year. $f_{cubic}(\cdot)$ denotes the cubic regression spline function used to account for the non-linear effects of proportions. $f_{gp}(\cdot)$ denotes the gaussian process smoothing function for coordinates. $b_j$ represents the year-specific random intercept. The model was fitted with the ***mgcv*** package in R. 

We calculated county-level predictions based on all tract-level predictions within the same county and year, using race-specific population size as weights. A race-specific HICDD was defined as the predicted HICDD under the assumption that the population is solely composed of individuals from this race.

The LSTCDD data were predicted using the same model.

#### Energy Use

We proposed a mixed median quantile regression model to identify energy insecure month for each ZCTA area. Specifically, the model was defined as
$$
\begin{aligned}
\text{Total Energy}_{ijk} &= \beta_0+\beta_1 \text{HICDD}_{ij} +\beta_2 \text{Median Income}_{ij} + \beta_3 \text{HICDD}_{ij} \times \text{Median Income}_{ij} 
\\&+ \beta_4 \text{Average Household Size} + \beta_5 \text{Number of Households} + b_{jk}
\end{aligned}
$$
Where $i$, $j$, $k$ denote ZCTA area, time (month and year) and county respectively. $b_{jk}$ denotes the random effects of all combinations of county and time. if the observed energy usage in a given month is lower than our prediction for the same month, we label the ZCTA area as energy insecure for that month.

## 3. ArcGIS Dashboard

We construct our mapping tool with the [ArcGIS Online Dashboard](https://yalemaps.maps.arcgis.com/apps/dashboards/ade55c053abe478ca5a556326ff8042e). The dashboard involves the following components:

- **A**. A main map for county-level information.
- **B**. One panel for county-level demographic information.
- **C**. Two top panels for county-level temperature information.
- **D**. One top panel for ZCTA level energy insecurity map and demographic information.
- **E**. One left sidebar for search and filters.
- **F**. One right panel for county selection. 

![Layout](C:\Users\27448\Desktop\Yale\Project\EHS\23.12.4\Layout.png)

### 3.1 Basic Operations

#### 3.1.1 Zoom up windows

Users can zoom up every window (maps and panels) by clicking the symbol in the top right corner. 

<img src="C:\Users\27448\Desktop\Yale\Project\EHS\23.12.4\ZoomUp.png" alt="ZoomUp" style="zoom:67%;" />

#### 3.1.2 Check Legends

We provide legends for both county-level map and ZCTA level map. 

<img src="C:\Users\27448\Desktop\Yale\Project\EHS\23.12.4\Legend.png" alt="Legend" style="zoom:50%;" />

#### 3.1.3 Use Filters

Users can call a sidebar by clicking the very left button. This sidebar includes widgets for county, map layer (datasets), year, race and values to control the visualization of our map.

- Find a County: Selecting a county will generate a flash showing the position of this county on our map.
- Map Layer: Select a dataset for visualization. Default is Household SVI. 
- Year: Select a year to visualize data for that year. Default is 2019. Note that the energy dataset is available only from 2016 to 2020. Temperature datasets are available from 2010 to 2019.
- Race: Select a race to visualize racial disparity temperature datasets. Default is White. This filter only works for the racial disparity layers for temperature.
- Values: This filter is divided into two widgets: '**Values Greater Than**' and '**Values Less Than**'. It is designed to filter values for all layers. For example, if we are interested only in areas with an SVI greater than 0.8, we can input '0.8' in the '**Values Greater Than**' widget and change the map layer as "Household SVI". Areas that meet this criterion will then be colored on our map. 



### 3.2 Examples

In this section, we present three examples to show how to use our mapping tool to identify vulnerable areas. 

#### 3.2.1 Check County-level Temperature Distribution

The temperature top panels contain three sections:

- **Information**: This section introduces basic information about the temperature measure.
- **By County**: This presents a time series plot for yearly average HICDD/LSTCDD.
- **By Race**: This presents the percent difference of race-specific average HICDD/LSTCDD compared to county averages.

When investigating the county-level temperature distribution, users may follow a three-step procedure:

- First, change the map layer as HICDD or LSTCDD.
- Second, find the county of your interest using the county selection sidebar.
- Then, click the county of your interest and check the temperature panels "By County".

#### 3.2.2 Check County-level Temperature Disparities by race

When investigating the county-level temperature distribution, users may follow a three-step procedure:

- First, change the map layer as racial disparities of HICDD or LSTCDD.
- Second, find the county of your interest using the county selection sidebar.
- Then, click the county of your interest and check the temperature panels "By Race".

#### 3.2.3 Check ZCTA-level Energy Insecurity

The energy insecure panel contains three sections:

- **Information**: This section introduces basic information about energy insecurity.
- **Select a Zip Code Area**: When selecting a county, this section will display all ZCTA areas corresponding to that county.
- **Demographics**: Upon selecting a ZCTA area in the "**Select a Zip Code Area**" section, this section will present all demographic information for the selected ZCTA area.

When identifying energy insecure ZCTA areas, users may follow a three-step procedure:

- First, find the vulnerable county of interest by checking the SVI layer, temperature layers or county-level median incomes.
- Second, select the county to investigate corresponding areas.
- Third, identify ZCTA areas of interest based on energy insecure index (the proportion of energy insecure months of all months) and demographic information.



## References

- [1] Martiello M. A. & Giacchi M. V. "High temperatures and health outcomes: a review of the literature." Scand. J. Public Health 38, 826–837 (2010).
- [2] McMichael A. J. et al. "International study of temperature heat and urban mortality: the ‘ISOTHURM’ project." Int. J. Epidemiol. 37, 1121–1131 (2008).
- [3] Zhao Q. et al. "Global regional and national burden of mortality associated with non-optimal ambient temperatures from 2000 to 2019: a three-stage modelling study." Lancet Planet. Health 5, e415–e425 (2021).
- [4] Hoffman J. S., Shandas V., & Pendleton N. "The effects of historical housing policies on resident exposure to intra-urban heat: A study of 108 US urban areas." Climate 8, 12 (2020).
- [5] Jesdale Bill M., Morello-Frosch Rachel, & Cushing Lara. "The Racial/Ethnic Distribution of Heat Risk–Related Land Cover in Relation to Residential Segregation." Environ. Health Perspect. 121, 811–817 (2013).
- [6] Nardone A., Rudolph K. E., Morello-Frosch R., & Casey J. A. "Redlines and Greenspace: The Relationship between Historical Redlining and 2010 Greenspace across the United States." Environ. Health Perspect. 129, 017006 (2021).
- [7] Gronlund C. J. "Racial and Socioeconomic Disparities in Heat-Related Health Effects and Their Mechanisms: a Review." Curr. Epidemiol. Rep. 1, 165–173 (2014).
- [8] Spangler K. R. & Wellenius G. A. "Spatial patterns of recent US summertime heat trends: Implications for heat sensitivity and health adaptations." Environ. Res. Commun. 2, 035002 (2020).
- [9] Hernández D., Jiang Y., Carrión D., Phillips D., & Aratani Y. "Housing hardship and energy insecurity among native-born and immigrant low-income families with children in the United States." J. Child. Poverty 22, 77–92 (2016).
- [10] Hernández D. "Understanding ‘energy insecurity’ and why it matters to health." Soc. Sci. Med. 167, 1–10 (2016).
- [11] Graff M., Konisky D. M., Carley S., & Memmott T. "Climate Change and Energy Insecurity: A Growing Need for Policy Intervention." Environ. Justice (2021).
- [12] Reames T. G., Reiner M. A., & Stacey M. B. "An incandescent truth: Disparities in energy-efficient lighting availability and prices in an urban U.S. county." Appl. Energy 218, 95–103 (2018).
- [13] Forrester S. P., & Reames T. G. "Understanding the residential energy efficiency financing coverage gap and market potential." Appl. Energy 260, 114307 (2020).
- [14] Goldstein B., Reames T. G., & Newell J. P. "Racial inequity in household energy efficiency and carbon emissions in the United States: An emissions paradox." Energy Res. Soc. Sci. 84, 102365 (2022).

