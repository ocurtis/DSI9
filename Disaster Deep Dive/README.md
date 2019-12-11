# Project 5: Natural disasters and FEMA payouts in the USA


## Introduction

In project 5, we look at FEMA disaster data in the USA from 2004 to present. Specifically we extract the data showing the amount spent by FEMA broken down by zip code, damage declared by zip code and the population mean and median income by zip code. This was done by web scrapping 90,000 rows from FEMA website using skip API function, 50,000 rows from the disaster code and over 30,000 zip codes and their population and income. All three datasets were combined into one dataset which has x rows and y columns. The data file is a .csv file called "insert_name_here.csv" along with a Jupyter notebook with the statistical analysis. A PDF of the presentation is also included in the project 5 folder. 



## Problem Statement

FEMA has a significant amount of data to sift through when it comes to disaster impact and relief prioritization. We are seeking to assist FEMA in building out a process and prototype vizualization toolkit that will better enable them to navigate such decisions. Specifically, our goals are two-fold:

1) To understand historical FEMA support in the context of average income and population sizes per zipcode. Has the organization correctly prioritized zipcodes that may not be able to, on average, lean on private insurance for disaster coverage? Can the SBA be leaning in further to help stimulate economies after disaster strikes?
 - Zipcode Explorer: https://public.tableau.com/profile/owen.curtis3576#!/vizhome/DisasterDashZipcodeExplorer/PrioritizationDash?publish=yes

 
2) To arm FEMA with a vizualization toolkit which enables them to assess priority zipcodes when disaster strikes. This includes a tool to estimate payouts at the zipcode level, and easily explore disaster and socioeconomic data at the zipcode level
 - Prediction Dash: https://public.tableau.com/profile/owen.curtis3576#!/vizhome/DisasterDashZipcodeExplorer/HurricaneTool


## Data Dictionary
| Variable                 | Type     | Description                                                                      |
|--------------------------|----------|----------------------------------------------------------------------------------|
| disaster_number          | int64    | FEMA disaster ID                                                                 |
| incident_type            | object   | Type of disaster (hurricane, flood)                                              |
| incident_begin           | datetime | Start date of disaster                                                           |
| incident_end             | datetime | End date of disaster                                                             |
| state                    | object   | State where disaster occurred                                                    |
| county                   | object   | County where disaster occurred                                                   |
| city                     | object   | City where disaster occurred                                                     |
| zipCode                  | int64    | Zipcode where disaster occurred                                                  |
| valid_registration       | int64    | Number of valid FEMA registrations in zipcode                                    |
| avg_damage               | int64    | Average damage experience by zipcode                                             |
| tot_damage               | int64    | Total damage at the zipcode level for a zipcode                                  |
| approve_assistance       | float64  | Approved assistance for disaster relief (not synonymous with below feature)      |
| tot_approve_ihp_amt      | float64  | Total approved funds provided by FEMA for disaster relief (TARGET)               |
| replair_replace_amt      | float64  | Initial repair support provided by FEMA ($)                                      |
| rental_amt               | float64  | Ongoing rental support provided by fema ($)                                      |
| other_needs_amt          | float64  | Other support provided by FEMA not covered by initial repair funds, rental funds |
| tot_max_grants           | int64    | Total max grants given by FEMA                                                   |
| Median                   | int32    | Income Median for each zipcode                                                   |
| Mean                     | int32    | Income Average for each zipcode                                                  |
| Pop                      | int32    | Population at the zipcode level                                                  |
| duration                 | float64  | Duration of disaster Event                                                       |
| registrations_per_capita | float64  | Number of FEMA registrants per zipcode populatoin                                |


## Executive Summary

*Data Wrangling and Merging*
 - A number of different data sources were leveraged here including: FEMA Housing Disaster Assistance API, FEMA Disaster Details API, Census socioeconomic Data
 - In the case of FEMA API data, a majority of this data was joined on Disaster Number. For more granular joins, zipcode and date were also leveraged

*Feature Engineering*
 - To assist with predictive power of our model, a number of additional features were engineered. This included: Disaster Duration, FEMA Registrations Per Capita, Average Disaster Damage ($)
 

*EDA*
 - Initial EDA of the relationship between disaster support fund distribution and income (average and median) at the zipcode level did not reveal any glaring relationships. This suggests that this is a guiding factor in determining where funds are allocated **at the zipcode level**
 - asd

*Modeling*
 - A number of different models were leveraged here including LogisticRegression, KNN Regressor, Adaboost, RandomForest, etc.
 - A train/test approach was taken to validate the resultant R^2 scores from our model.

*Tool Development*
 - We sought to create two primary views: a Zipcode Prioritization Dashboard, and Estimated Payout Dashboard.
 - The Zipcode Prioritization Dashboard includes all historical data from FEMA's disaster database, in addition to census level data. Analysts can dynamically filter at the state level to find zipcodes that have been significantly impacted, and the zipcodes that are most populous with lowest average income for prioritization.
 - The Estimated Payout Dashboard: includes model predictions against the dataset we were able to retrieve from FEMA. While this is currently based on historical data, it can be applied to future estimates with some additional tweaking. 

## What's Next?

*Establishing a Live Feed*
 - Currently our Tableau dashboard is sitting on historicals through mid 2019. In the coming month we will work to build a livestream from the FEMA API ontop of which our tools will sit

*Model Refinement / Estimate Tool Enhancements*
 - While we believe our prototype is an interesting and effective tool to assist FEMA analysts with payout predictions, we believe there is significant room for refinement. The incorporation of deeper data cuts (week level) and additional historical data (pre 2004) will enable this
 -The incorporation of confidence levels against each of our estimates. Because this is at the zipcode level, sample is problematic here and estimates at the level of granularity are unstable in the current iteration.
 - In addition to this, our next goal will be to integrate the estimation tool with real time hurricane tracker data. This will enable us to tie the hurricane track to the map functionality in place




