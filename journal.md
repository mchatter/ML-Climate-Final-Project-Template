Week 1: Data Gathering - Crop Yields or Planted Areas
This week, I've been trying to gather and clean data for at least 10 years of wheat and poppy crop yields or planted hectares in every Afghan province. In 2020, the United Nations Office of Drugs and Crime (UNODC) published a PDF with the Indicative district level estimates of opium poppy cultivation in hectares covering 2005-2020. The data was collected in conjunction with the National Survey Institute of Afghanistan (NSIA). In 2020, due to pandemic-related ground survey restrictions, remote sensing techniques to calculate poppy cultivation in hectares.

In order to render the UNODC tables into a workable formate, I extracted the pages from containing the necessary data from the published PDF report, then ran OCR (optical character recognition) on these pages. Then, I ingested the OCR results into Excel worksheets. Since OCR is not a perfect process, I manually fixed column content issues for each extracted file, and saved them all as CSVs. In a latter state of this project, I will simply convert these CSVs into dataframes with Python's pandas library.

UNODC 2020 Afghanistan Opium Survey Report: https://www.unodc.org/documents/crop-monitoring/Afghanistan/20210503_Executive_summary_Opium_Survey_2020_SMALL.pdf

On wheat cultivation: I have not yet been able to find province-by-province data on any food crop yields in Afghanistan. I was able to use the United States Department of Agriculture's (USDA) Foreign Agricultural Service's (FAS) Open Data Services, implemented through the [Swagger UI](https://apps.fas.usda.gov/opendata/swagger/ui/index#/), to find time-series data on the total estimated wheat production of the entire country of Afghanistan. I am still searching for provincial data. If nothing else works, I am hoping to implement the methodology outlined in this paper:

Tiwari Varun, Matin Mir A., Qamer Faisal M., Ellenburg Walter Lee, Bajracharya Birendra, Vadrevu Krishna, Rushi Begum Rabeya, Yusafi Waheedullah, "Wheat Area Mapping in Afghanistan Based on Optical and SAR Time-Series Images in Google Earth Engine Cloud Environment", Frontiers in Environmental Science, Vol. 8, 19 June 2020. URL=https://www.frontiersin.org/article/10.3389/fenvs.2020.00077
DOI=10.3389/fenvs.2020.00077    
	
The authors only predict wheat-sown areas in Afghanistan for the year 2017. A visualization of their work can be found [here](http://geoapps.icimod.org/afwheat/). Since interpreting and implementing their methodology for all years would be a time-consuming task (especially given that we are only in the first stage of the overall project and might not necessarily have an accurate forecast of how much time the remaining stages will take), I have emailed Varun Tiwari and Mir Matin at ICIMOD to ask if the code for their project could be made available to me as a reference point. If they do not respond, and if I cannot find any provincial survey day in the next few days, I will consult with the professor and TA and decide whether to move ahead with only the poppy yields predictive model (a pretty significant task in and of itself, given the political instability in the region), or whether to replicate Tiwari et al's methodology in earnest.

Further data gathering steps include local heat mapping, rainfall index, and soil composition data from the World Bank Climate Knowledge datasets, as well as numerical scores for rule of law preservation and other political instability indicators for the region from the Transparency Index and World Bank.


Week 2: Climatological Data Gathering

This week I sourced and added Mean Temperature and Precipitation data for all provinces. I got this data primarily from the World Bank Climate Knowledge [Data Portal](https://climateknowledgeportal.worldbank.org/download-data). This data was compiled by the Climatic Research Unit (CRU) of University of East Anglia. I only got observed data and did not any predicted numbers for 2022 largely because I wanted to reduce the downstream opacity in my model inputs. In order to make my work replicable, I'm hoping to stick mainly to real-world observations. As we have observed from class, climatological data is most accurate in the short-term, and rapidly loses accuracy when doing long forecasts.

Week 3: Incumbent Soil Conditions

The soil data took me a little bit of time to clean up and standardize, since I'm getting most of it from previously commissioned agricultural studies and reports. My main data source was the Afghanistan Soil Atlas report compiled in 2020 by the FAO or the Food and Agriculture Organization of the United Nations. Fields for each province include Soil type, Area, Sand %, Clay %, pH score, Total Nitrogen, Available Phosphorus, Exchangeable Potassium, Available Sulphur, Electrical Conductivity in a 1:2.5 soil:water solution, Calcium Carbonate content. 

Process: I OCR-ed specific pages from the report, then for one province I manually compiled the CSVs from the TXT exports of the OCR-ed pages. That was taking way too long, so I found a software to do this for me. Because the tables were only part of a page that also contained many other complex image artefacts, I could no longer use the methods from Week 1. Instead, I downloaded Tabula, and used their local deployment to select only the section of the page that contained the soil composition table for a given province, and the exported the table as a CSV. From there, only minor corrections were needed before I could commit the data to the repo.

Overall Update:
T'm running up against some data-gathering challenges for getting province-by-province data on total number of wheat-sown hectares for the past 10 years, but am in a pretty good position in terms of data-gathering on all the other aspects of the project (province-by-province opium poppy-sown hectares, soil sample composition, rainfall, and temperature data). I'm seeing two ways forward:

1. Push ahead on trying to get data for wheat-sown hectares for the region with a satellite image-classification model using a singular reference paper (Tiwari et al, 2020), but risk not having enough time to focus on actual crop-yield predictions.

2. Focus on thoroughly building out the crop-yield prediction models for opium poppy only (eg. experiment with finding optimal neural network architecture, include additional features to account for regional political instability metrics, and build out a prediction testing mechanism using satellite products a la Kim et al, 2019).

I'm leaning towards the second avenue both in terms of personal interest, and because I think it would enable me to deliver the most "complete" project by the end of the semester.

Week 4: Feature Engineering

Part 1: Get the CSVs into DataFrames

Opium Sown: Have number of sown hectares for each district in each province for each year between 2010 - 2020, except for part of Bamyan, Day Kundi, Farah, Faryab, Ghazni, and Ghor, where we have 2008-2018 data. Pending further investigation, we will code these provinces with zeros. All fields marked '-' or 'p-f' (poppy-free) in the CSV are replaced by zeros. Data is of numerical type. All NaN values are set to zero.

Soil Data: Dropped the WRB Codes column because it contained numerous inaccuracies as an artefact of the scraping process and because it was collinear with the Soil Type column. Soil Type column was categorical and has been turned into dummy variables with one hot encoding. All other data was of numerical type. Province name is broadcast for each row of soil sample information. All fields marked (-)(-) in the CSV are replaced by zeros. Dataframe only contains the estimates of sown areas. All margin of error information is removed from the dataframe. All NaN values are set to zero.

Temperature and Precipitation Data: All data is transposed such that the years are columns and months are rows. Province name is broadcast for each row of climatological information. All NaN values are set to zero. Data is of numerical type.

Overall Update: The verdict is in. For the remainder of the semester, I will be focusing on thoroughly building out the crop-yield prediction models for opium poppy only (eg. experiment with finding optimal neural network architecture, include additional features to account for regional political instability metrics, and possibly build out a prediction testing mechanism using satellite products a la [Kim et al, 2019](https://www.mdpi.com/2220-9964/8/5/240)).

By next week, my goal is to have a random forest model running. This shall serve as my benchmark model. Before I set up the model, I need to be clearer about what my prediction goal is. Do I want to predict the most optimal regions for sowing poppy? Or do I want to predict numerical yields or sown areas for next year? One is a categorization problem and the other is a numerical regression problem. 

Suggestions to Consider:

Prof. Kucukelbir: "You may want to consider building a hierarchical prediction model? In any case, I would recommend that you lay out all of the properties of your data and your assumptions before jumping ahead with a neural network based approach — there are many other, likely better/more interpretable/Bayesian, options available to you if you are not working with image data."

Nicolas Beltran: "Given the argument about data division and the fact that you'd be using random forests, I think that your approach at the moment of trying random forests and then neural networks seem reasonable. I would also discourage you go for the Bayesian approach as a first model because most people usually have a better intuition with neural networks/random-forests/[insert another traditional ml model] and from what I've read from your journal it seems like the data collection that you've had to do has not been easy and the time that could be spent learning about new Bayesian methods might be better spent on getting additional predictors."

## 2022-03-07 check in: alp

Looking good. Would stick with Nicolas' recommendation to throw something like a RF at the problem. Encourage you to keep this journal up to date weekly.

## 2022-03-08

I was making all my observations and comments in-line in my Python notebook, and so forgot to update my actual research journal - sorry! Here is a quick update on what happened since the Feature Engineering update:

I need to add a BIG CAVEAT FOR SOIL DATA. We do not unique soil data for all 34 provinces. We only have soil data for 9 provinces - some of the most populuos and biggest in the nation by area, but certainly not complete. I previously thought to to make up for any missing data by running an image-to-numbers script to obtain features from country-wide heatmaps from 2011 (source: Afghan Geodesy and Cartography Head Office, conforms to United Nations Afghanistan Regions 3958.1 R3, June 2011) showing, among other factors, topsoil texture distribution across the country. But I realized that the features for the 9 provinces with granular soil do not map well to the country-wide features presented in the heat maps in the FAO report (no other reliable data sources for soil quality in the region are available to the best of my knowledge). So I used the maps and qualitative assessments from the UNODC Opium Yields reports to subjectively broadcast the soil data from each of the 9 provinces to the closest provinces whose topsoil texture distributions closely resemble each others per the 2011 maps. To get a single reading for each province, we will consider the area of each soil type in each province as the "weight" vector and multiply it to the respective chemical measurement of that soil type, and take the sum of each multiplied column, kind of like a weighted average. And so we will have the same set of soil quality metrics for each province. But these broadcasted readings should be subsituted with unique soil sample results as soon as the data becomes available.

Part of the reason I feel comfortable proceeding is that the lack of soil quality data is a rather country-specific problem and can be solved given time. So I'm trying to not let perfect be the enemy of good enough for a proof-of-concept model.


Random Forest Benchmark Regressor:

For each province, for each year, the X dataset is the local soil features, and the mean temperature and mean precipitation in the 12 months of the precending year, while the Y is the number of hectares sown in the current year. We have climatological data from 2010 through 2020 (11 years). We will use 34 provinces * 10 years from 2010 to 2019 = 340 datapoints in total for training and testing. Once we have fine-tuned our benchmark, we will use the 2020 climatological features (and existing soil featuers) to predict the number of hectares of opium sown in 2021, and compare our prediction against the UNODC report that will come out later in the year.

Results: Poor. My R^2 evaluation is negative! That's worse than drawing the equivalent of a horizontal line through this high-dimension data. 

Well, recognizing that predicting an exact number of hectares sown is remarkably difficult, and also not quite necessary per our original intention, I'm going to try one more thing - categorize the provinces as high, medium, or low yield based on which percentile they fall into, and try to run a categorical random forest model again to see if I get any better results. I will no doubt run into unbalanced classes and will need to fix my sampling accordingly.


## 2022-03-27

Added intercept term. Updated datasets for random forest (RF) regression model. Switched to using the statsmodels.api library. The soil chemical composition features are now normalized by the area of the base province. 

Achieved 13.7% adjusted R^2 (better than negative!). 

I also tried to figure out which independent variables are important by running the regression minus one column at a time and looking at the drop in adjusted R^2 value, if any which would indicate that the dropped column was explaining at least some percentage of the variation in the Y variable (number of hectares of opium sown). Conclusions pending results interpretation.

Trained an RF classifier from sklearn on data from 27 (or roughly 80%) of the 34 provinces, and tested the classifier accuracy on data from the remaining 7 provinces. Used the same set of independent variables for X as was used in the regressor, but created indicator variable for whether poppy was sown in a province in a particular year for the Y variable. 

Achieved 68.6% accuracy. 

Next up: (1) Make sure classes are balanced in the training and test sets. (2) Set up and use a CNN classifier. Will likely need to use transfer learning.

## 2022-04-15

I added five new features to account for political instability. These are worldwide governance indicators at the national level over the past 10 years, broadcast across all the provinces.

To address the class Imbalance issue, I upsampled the positive class (poppy-having data-points) in the training set, but not in the test-set, since there are far fewer poppy-having provinces than poppy-free regions in real life.

I also trained and ran an SVM Classifier.

Results from all three predictive models:

![image](https://user-images.githubusercontent.com/30819781/165384701-2b41d1d9-3b2a-41e0-ab76-ac775738172a.png)

None of the variables in the regression model were significant. I think this is attributable to two things: 

(1) data quality / scarcity: I had to broadcast the soil data from 9 provinces to the geographically closest provinces whose topsoil texture distributions most resembled the nucleus province per 2011 soil-maps. Plus, to get one normalized weighted average reading per soil feature for each province, I did the following: 

Sum(Area covered by a soil type in a province * Percent presence of that soil type) / Area of base province.

I also had to use the national-level precipitation, temperature, and governance indicators for each province.

(2) feature set does not fully capture reality: where climatological and soil data might be enough to predict crop yields when the crop itself does not contribute to the production of a dangerous narcotic, and the political situation in the country is stable, neither holds true for the opium poppy crop we're looking at. Hence, I think even the addition of the national-level political stability indicators do not fully capture the various factors affecting decision-making for poppy growers.

Still, recognizing that predicting an exact number of hectares sown is remarkably difficult for the number of data points we have, and also not really necessary per our problem statement, the relative success of the ML classification models are quite promising. 

The Random Forest Classifier performs better on average than SVM, but both are far from perfect. Repeated runs shows that the Random Forest Classifier has 80% accuracy on average and the SVM Classifier has 71.4% accuracy on average. But the F-1 Score for the SVM Classifier is higher at 56.5%, compared to 41.6%. The F-1 score is important because of the underrepresentation of the positive class (poppy-having provinces) in real life, which means our classifier should be able to correctly recall all the real positive cases in the test set to be used as an effective prediction tool. From this perspective, the SVM Classifier has the edge.





