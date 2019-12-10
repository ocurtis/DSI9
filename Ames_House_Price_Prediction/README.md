# Predicting Housing Prices in Aimes Iowa


## Problem Statement

Kaggle has tasked the General Assembly DSI Immersive team with estimating housing prices for the small city of Aimes Iowa. The housing data recieved spans from 2006 to 2010 with over 75 features (a mixture of discrete, continous, nominal, and ordinal variables) per home, and close to 3000 homes documented. The following project will include a comprehensive assessment of the data recieved, and an in-depth explanation of how to best model sales price. The following steps will be outlined in detail:

 - [Exploration](#Exploration)
 - [Cleaning](#Cleaning)
 - [Engineering](#Engineering)
 - [Modeling](#Modeling)
 - [Findings](#Findings)


## Data Dictionary

**Please see below for a breakdown of the data sets we will be working with over the course of the this analysis.** We'll be referencing this over the course of our analysis as we explore the data set and continue to iterate on our model. This data dictionary was constructed by Rachel Harbart, another DSI BOS immersive student.

**Ames Iowa Housing Data Set 2006-2010**


| Feature         | Type   | Dataset | Description                                                            |
|---|---|---|---|
| 1st_flr_sf      | int    | train   | first floor sq ft                                                      |
| 2nd_flr_sf      | int    | train   | second floor sq ft                                                     |
| 3ssn_porch      | int    | train   | Three season porch area in square feet                                 |
| alley           | object | train   | Type of alley access to property                                       |
| bedroom_abvgr   | int    | train   | Bedrooms above ground                                                  |
| bldg_type       | object | train   | Building type                                                          |
| bsmt_cond       | object | train   | General condition of the basement                                      |
| bsmt_exposure   | object | train   | Walkout or garden level basement walls                                 |
| bsmt_full_bath  | float  | train   | Basement full bathrooms                                                |
| bsmt_half_bath  | float  | train   | Basement half bathrooms                                                |
| bsmt_qual       | object | train   | Height of the basement(height range for each category)                 |
| bsmt_unf_sf     | float  | train   | Unfinished square feet of basement area                                |
| bsmtfin_sf_1    | float  | train   | basement finished square feet                                          |
| bsmtfin_sf_2    | float  | train   | second finished area square feet                                       |
| bsmtfin_type_1  | object | train   | Quality of basement finished area                                      |
| bsmtfin_type_2  | object | train   | Quality of second finished area (if present)                           |
| central_air     | object | train   | Y/N for central air                                                    |
| condition_1     | object | train   | Proximity to main road or railroad                                     |
| condition_2     | object | train   | Proximity to main road or railroad (if a second is present)            |
| electrical      | object | train   | electrical system type                                                 |
| enclosed_porch  | int    | train   | Enclosed porch area in square feet                                     |
| exter_cond      | object | train   | Present condition of the material on the exterior                      |
| exter_qual      | object | train   | Exterior material quality                                              |
| exterior_1st    | object | train   | Exterior covering on house                                             |
| exterior_2nd    | object | train   | Exterior covering on house (if more than one material)                 |
| fence           | object | train   | Fence quality                                                          |
| fireplace_qu    | object | train   | fireplace quality                                                      |
| fireplaces      | int    | train   | Fireplaces                                                             |
| foundation      | object | train   | Type of foundation                                                     |
| full_bath       | int    | train   | Full bathrooms above ground                                            |
| functional      | object | train   | home functionality(ex:typical, salvage)                                |
| garage_area     | float  | train   | Size of garage in square feet                                          |
| garage_cars     | float  | train   | number of cars that can fit in garage                                  |
| garage_cond     | object | train   | Garage condition                                                       |
| garage_finish   | object | train   | Interior finish of the garage                                          |
| garage_qual     | object | train   | Garage quality                                                         |
| garage_type     | object | train   | Garage location                                                        |
| garage_yr_blt   | float  | train   | Year garage was built                                                  |
| gr_liv_area     | int    | train   | Above grade (ground) living area square feet                           |
| half_bath       | int    | train   | Half bathrooms above ground                                            |
| heating         | object | train   | type of heating                                                        |
| heating_qc      | object | train   | Heating quality and condition                                          |
| house_style     | object | train   | House style                                                            |
| id              | int    | train   | randomly assigned id                                                   |
| kitchen_abvgr   | int    | train   | Kitchens above ground                                                  |
| kitchen_qual    | object | train   | Kitchen quality                                                        |
| land_contour    | object | train   | Flatness of the property                                               |
| land_slope      | object | train   | Slope of property                                                      |
| lot_area        | int    | train   | Lot size in square feet                                                |
| lot_config      | object | train   | Lot configuration                                                      |
| lot_frontage    | float  | train   | Linear feet of street connected to property                            |
| lot_shape       | object | train   | General shape of property                                              |
| low_qual_fin_sf | int    | train   | Low quality finished square feet (all floors)                          |
| mas_vnr_area    | float  | train   | Masonry veneer type in sq ft                                           |
| mas_vnr_type    | object | train   | Masonry veneer type                                                    |
| misc_feature    | object | train   | misc features                                                          |
| misc_val        | int    | train   | value of misc feature                                                  |
| mo_sold         | int    | train   | month sold                                                             |
| ms_subclass     | int    | train   | The building class (ex: 20 = 1 story, 90=duplex)                       |
| ms_zoning       | object | train   | Identifies the general zoning classification of the sale               |
| neighborhood    | object | train   | Physical locations within Ames city limits                             |
| open_porch_sf   | int    | train   | Open porch area in square feet                                         |
| overall_cond    | int    | train   | Overall condition rating(range 1-10, 10 best)                          |
| overall_qual    | int    | train   | Overall material and finish quality(range 1-10, 10 best)               |
| paved_drive     | object | train   | Paved driveway                                                         |
| pid             | int    | train   | randomly assigned id                                                   |
| pool_area       | int    | train   | Pool area in square feet                                               |
| pool_qc         | object | train   | Pool quality                                                           |
| roof_matl       | object | train   | Roof material                                                          |
| roof_style      | object | train   | Type of roof                                                           |
| sale_type       | object | train   | type of sale                                                           |
| saleprice       | int    | train   | sale price                                                             |
| screen_porch    | int    | train   | Screen porch area in square feet                                       |
| street          | object | train   | Type of road access to property                                        |
| total_bsmt_sf   | float  | train   | Total square feet of basement area                                     |
| totrms_abvgrd   | int    | train   | total rooms above grounds(no baths)                                    |
| utilities       | object | train   | Type of utilities available                                            |
| wood_deck_sf    | int    | train   | Wood deck area in square feet                                          |
| year_built      | int    | train   | Year built                                                             |
| year_remod/add  | int    | train   | Remodel date (same as construction date if no remodeling or additions) |
| yr_sold         | int    | train   | year sold                                                              |

## Executive Summary

### Exploration
**Null Detection**: 
  - The provided Ames Iowa dataset featured a number of null values across both the test and training dataset. Thus, an approach was developed to concatenate together the test and training data sets to pass them through the same processes for cleaning.
  - NaN values were spread across a variety of features including both nominal/ordinal/discrete/continuous data types.

**Distributions**: 
  - Initial views of SalePrice demonstrate a strong positive/right skew. From the outset, we can start thinking about transforming our target to arrive at a normal distribution.
  - A number of features also demonstrate this skew. These features tend to be different expressions of square footage / area. These are also good candidates for transformation testing. 
  
**Correlation**: 
  - *Gr Liv Area, Overall Qual, Exter Qual* are among the features that are most strongly correlated with our target.
 
### Cleaning
**Null Imputation**: 
  - Several approaches were taken to account for NaN values in our dataset.
  - For continous and discrete variables, the mean was typically leveraged for imputation. Several imputation approaches (median, 0) were also explored with minimal differentiation in R2
  - *Lot Frontage* is a continous feature with high amount of NaN values. The approach taken here to adjust for this, given that Lot Frontage could be a potentially important feature, is to impute with the averages by neighborhood. Please reference the Jupyter Notebook for more details here
  
**Nominals**:  
  - Major NaN contributor columns included: *Alley, Lot Frontage, Pool QC, Misc Features* -- a mix of features that should have values encoded, and other features that are just rare features of a home. These were more often than noted given value 'None' where data is missing

**Ordinal Encoding**: 
  - In order to feed our ordinal features into our model, it's essential to encode our ordinal data. Leverage the data dictionary, we translate our ordinals into ordered numeric scales that retain the relationships among the ordinal values
  - A number of ordinal features shared the same encoding, so we leveraged these groupings where possible. In some cases, custom encodings were applied
  
### Engineering
**Dummies**:   
  - With all of our data cleaned, we proceeded with the construction of *dummy features* to numerically capture the various values our categorical data took on in our dataset. This resulted in roughly ~60 additional features
  - Dummy features showing a sample of less than 100 for data set were dropped from our feature set to ensure that only the highest sample dummies were part of our training data
  
**New Features**:  
  -  In addition to the dummy features created, we also leveraged our knowledge of the real estate market to engineer new features
  -  Specifically, Real Estate listings often include **total square footage and total bathrooms**. Our current feature set does not include this at a high level 
  
**Polynomial Features**:  
  -  Interaction terms were also something that our team investigated. Models were constructed with up to ~150 polynomial features, and as little as 2 polynomial features with varying results in terms of R2 scoring
  -  In the end, to enhance our ability to generalize our model, and to prevent significant multicollinearity among our feature set, we opted to select the **top two interaction terms** for model implementation
  
**Feature Reduction**:  
  - To improve the generalizability of our model, we also spend some time dropping features. We made this determination by assessing final correlation strengths with our dependent variable, SalePrice.  
  -  Our final feature count, after much experimentation with correlation strength cutoff, was ~50 features.

### Modeling
**Transformation**:  
  - Because of the strong positive/right skew exhibited by our target, we proceeded to log transform SalePrice to generate a normal target distribution
  -  Similarly, a number of features-- specifically those which involve SF/Area measurements -- were treated in the same manner. We accomploshed this by generated a distribution skew cutoff and tuning this by assessing model accuracy impact
  
**Selection**:  
  -  A number of different models were selected for testing here, including **Linear Regression, Lasso, Ridge, ElasticNet**
  -  Our model accuracy assessment began with a simple Linear Regression Model and we moved into regularization (Lasso, Ridge) which introduce additional parameters for hypertuning and driving marginal improvements to R2
  -  The ElasticNet model is a hybrid of Ridge and Lasso, enabling us to tweak the L1 Ratio to adjust the weightings that correspond to the influence that Lasso/Ridge have on the outcomes 
  
**Scoring**: 
  -  We assessed the performance of our model by assessing the **R2** score which describes the percent of variance in our data described by our model.
  -  To assess each model, we leveraged *train_test_split()* to build a training and test data set. This enabled us to train on a percentage of available data, and leverage the remaining portion of our data for testing the model. Because it's unseen data, this gave us the opportunity to assess variance 
  - *Cross_val_score()* was also utilized here. This method allowed us to use a **k-fold validation** approach for model validation. It allows us to randomly subset our training data and score for a better understanding of performance.
  
### Findings 
  -  After iterative model testing across Linear, Lasso, Ridge, and ElasticNet models, it was determined that our R2 value was strongest with Lasso. Lasso regularization results in coefficient minimization for unimportant features. We explored these coefficients and felt that the resulting features were plausbile and in keeping with our domain knowledge.
  - Our resultant R2 across the full training set was **0.902**, with a RMSE of **~20K**.
