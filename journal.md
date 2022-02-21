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
