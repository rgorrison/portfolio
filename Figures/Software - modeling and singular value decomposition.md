# An ensemble approach to extracting shared variability from a database

## Analysis overview
The goal of this project was to design a piece of software arrive at an understanding of dominant modes of variability of the South American Summer Monsoon. This was done using a principle component analysis to isolate the variability shared by a database of discrete records and construct a new set of maps and time series to illustrate the common patterns (spatial and temporal). 

The records used for this analysis were cave features called speleothems that contain information about the past hydrology via a proxy variable for rain: oxygen isotopes captured in calcite ($CaCO_3$). Samples of this proxy along the central axis of a speleothem provide a *floating* time series but which does not contain any temporal information. The dates, called age ties, that *anchor* the oxygen isotope time series to a fixed period in the past is generated using the radio isotopes U/Th, measured at finite points along the axis of a speleothem.  

The challenge addressed here is that each U/Th measurement has some uncertainty. In order to understand how different oxygen isotope time series compare to one another, it is important to include the uncertainty of the age ties in the principle component analysis. This was done by writing a piece of software to model the ages of oxygen isotope time series using a [Monte Carlo method](https://en.wikipedia.org/wiki/Monte_Carlo_method) approach. Below, the stages of this analysis are illustrated:

![About the Monte Carlo resampling protocol](/assets/mceof_about.png)

### 1: 

### 2

## Modeling code

## Plotting results 
The below figure captures the two dominant modes of variability. These maps and time series illustrate the shared patterns (spatial and temporal) of the databse of paleoclimate records, accounting for time uncertainty in their original measurements.

![MCEOF modes 1 and 2](/assets/mceof12.png)
