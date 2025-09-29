# Solar panel estimator
Estimating solar panel energy output given a specific coordinate in the US

## Overview
My project develops a ML model to estimate average yearly solar panel energy generation for a given location, using historical weather and solar data. It will also feature a GUI which will help the user interpret the data through estimates such as how long a solar panel will take to pay for itself economically and environmentally (payback period). The model will also be able to suggest the tilt to use 

## Motivation
In the green transition ensuring that solar energy is deployed efficiently is important for both businesses and governments. Current tools typically use zip codes, instead I will be using coordinates as they allow greater freedom and may be useful in estimating solar generation in areas with no zip code. For example, a solar farm in the countryside.

## Project Objectives
* Develop a regression model capable of predicting average yearly solar panel energy generation.
* Develop a GUI for users to interpret what the data means, such as estimating the payback period.
* Create a database integrating diverse data to train the model including geographical coordinates, historic weather, and solar radiation data.
* Evaluate model performance using (spatial cross-validation?) on unseen data.

## Limitations
* Data on my ground truth (solar energy generation) is easily available in the US so I will be limiting my project to there.
* Modeling every type of solar panel is beyond the scope of the project so I will be focusing on a widely used type of solar panel with a commonly used configuration.
* I'm not taking into account the affects of climate change on future solar energy generation.
* Fluctuating energy prices means the payback period may fluctuate.

## Scope
The model will focus on estimating yearly energy generation for a solar panel installation in the US. Specifically a Monocrystalline Silicon (c-Si), single axis tracking, ground mount, flat ground, solar panel. I will also be using current US average electricity cost to estimate the payback period.

## Data sources
* OpenWeatherMap API - Historical weather data (temperature, cloud coverage etc)
* NREL National Solar Radiation Database - Historical solar irradiance data for the US
* Open-Meteo Elevation API - Provides elevation data
* Reverse Geocoding APIs - Provides data on if coordinates are in the US and on land
* OpenEI NREL PVDAQ - Solar energy generation from around the US (ground truth)
https://data.openei.org/s3_viewer?bucket=oedi-data-lake&limit=100&prefix=pvdaq%2Fcsv%2Fpvdata%2F

## Part 1: System selection
In this section I will get the metadata of all the systems and then select which systems meet my desired criteria.

* Initial EDA: First I downloaded the metadata csv. Looking at the systems' metadata, 1498 were of type unknown and 1613 were fixed tracking systems out of 1621.
* Decision to pivot: This makes my original plan to used a tracking ground mount solar panel impossible, therefore I will be switching to using fixed systems. While this means I'll be using both ground and roof systems, I should be able to take account of the two main types anyway as the main difference is the tilt which is available in the metadata. 
* Final selection: Next I then filtered the data on: 5+ years of data; quality assurance test passed; fixed systems. I also did some initial data acquisition from the metadata file gathering: latitude, longitude, elevation, azimuth, and tilt.

## Part 2: Label data preprocessing
In this section I will be gathering the raw solar generation data, and calculating the performance ratio which will be the ground truth for my model.

* Initial EDA: Firstly I looked at a how the data was stored on the OpenEI PVDAQ. There wasn't a clear way of structuring the data, some sites has multiple csv files per year, some only 1. There was also no clear naming convention for the files and some files for a single year had insufficient data.
* Defining the structure: I decided to only collect data that: has 1 csv file per year, has 350+ days of data per csv, had at least 5+ years of data.
* Gathering and processing the data: I downloaded the needed data according to the set structure and stored it in the raw data file. After which I calculated the mean power generation for each system and used the generation capacity to get the performance ratio.


