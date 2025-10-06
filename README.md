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
* OpenEI NREL PVDAQ - Solar energy generation from around the US (ground truth)
* NASA Prediction of Worldwide Energy Resource (POWER)

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

## Part 3: Feature data preprocessing
For the 3rd section I'll be gathering and calculating data on the climate of each system such as the mean solar irradiance.

* Initial EDS: First I looked at how data was stored on the NASA POWER. I was able to input a coordinate and get all climate data I needed for a system at that coordinate.
* Data gathering: I then did this process for each system and downloaded data on each climate variable for 2019.
* Data preprocessing: Afterwards I looked through the downloaded data for each system and calculated the mean of each climate variable I needed.

## Part 4: Data partitioning 
Next before I train the model I'm going to split up the data

* Data splitting methodology : I decided to use simple random sampling to split the data, in ratios of: 60% training, 20% validation, 20% testing.

## Part 5: Model training
In the 5th part I'm training the model and deciding what degree to use for my regression model.

* Training the model: I used the training data to train the model for polynomial regression from degree 1-5 using a sklearn pipeline.
* Tuning hyperparameters: I then ran this model against the validation set to get the RMSE for each degree.
* Selecting final the model: I found that a degree 1 had the lowest RMSE so i decided to use a linear regression model.

## Part 6: Model testing and evaluation
For the final part I'm testing the model on the test data and evaluating its performance.

* Testing the model: First I ran the model on the test data getting a RMSE of 0.055 and a r^2 value of 0.52.
* Visualising the results: I plotted a series of graphs to compare predicted outcomes and actual outcomes to find where my model could've improved.
* Testing a hypothesis: Looking at the data it looked like the bigger an outlier the true performance ratio was from the mean the worse my model was at predicting it's true output. I confirmed this by finding the covariance between the two variables which turned out to be 0.58.

## Conclusion
My model can be seen as a partial success, while it is able to find some pattern between the variables and solar performance ratio an improvement can still be made. This could be done by gathering more data however I am limited in how many solar systems i could get data for. Instead more closely looking at data on a day by day basis instead of looking at year average patterns may improve results. However this would require significant computation to train the model on a daily data.
