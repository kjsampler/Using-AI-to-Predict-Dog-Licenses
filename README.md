# Using-AI-to-Predict-Dog-Licenses  
## Abstract  
This project focuses on the goal of predicting the number of licenses to be dispensed on a given day using historical data. Three years of data (2015-2017) were used to train several models in order to generate forecasts for the upcoming year, 2018. These models were evaluated not only for their accuracy, which was measured by mean squared error, but also by the amount of time that it took to train and fit the model.  The OSEMN framework was used to organize this project. 

First, the data was scrubbed of all null values and "hidden" null values such as "No name listed". Next the column "ValidDate" was used to create a timestamp index. To limit the scope of the project, a univariate dataframe was created using daily totals of licenses dispensed, accounting for weekends and holidays. The first AI model tested was ARMA, but due to the seasonal nature of the data, SARIMA proved to be more accurate. It should be noted that with the SARIMA model, a grid search was used to generate the best values for p, d, and q, and this took a significantly long time to run. Next, a neural network was used to test if accuracy and training time could be improved. After several tuning steps, an LSTM with a 5 step lag was saved as the final model. This model took only 5 seconds to train, and yielded a lower MSE than the SARIMA model. A function was written to use the final model to recursively forecast "n" steps into the future. 251 steps were attempted, however, the model starts to converge on itself and loses accuracy well before 251 steps.  

My recommendations to a potential client would be to use the function predict_values(). However, it should be noted that this function should not be used for a large number of time steps. With the model trained on 3 years, it seems to converge before 60 steps. In a business example, this model could be used to predict an approximate number of licenses that would be dispensed the next week, and staffing and resources could be adjusted accordingly, thus saving the department money and resources.   
## Files Included  
### CSVs:  
#### These are the raw data pulled from data.gov
* 2015 Dog Licenses.csv  
* 2016 Dog Licenses.csv  
* 2017 Dog Licenses.csv  
* 2018 Dog Licenses.csv  
#### CSVs created as a result of data cleaning:  
* raw_data.csv (Full dataset, cleaned)  
* uni_final.csv (Univariate dataframe with daily total licenses)  
* df_18_final.csv (A cleaned univariate dataframe from 2018 data, used for testing)
### Notebooks:
* Dog License Data Cleaning - A jupyter notebook where data was cleaned. Contains the Obtain, Scrub and Explore sections  
* Dog License ARIMA Forecasting - A jupyter notebook where the ARMA and SARIMA models were developed, trained and tested. Contains Model and Interpretation  
* Flatiron_School_LSTM_Time_Series - A Google Colab notebook where the LSTM model was developed, trained and tested. Contains Model and Interpretation  
### Slide Deck
* Predicting Dog Licences Dispensed per Day - A brief, non-technical Google slides presentation of methodology and results found, including recommendations and future work ideas  

## Methodology  
An OSEMN framework was used to conduct this study.  
Obtain, Scrub, Explore  
Data was obtained from data.gov regarding the dog licenses dispensed in Allegheny county. For this investigation, the years 2015-2018 were obtained, although more data is available and could potentially be used to improve model performance. Data was scrubbed of all null values, including "hidden" null values, where a value was input that did not actually contribute to the data. In order to limit the scope of the project, a univariate model was created that contained a timestamped index (from column 'ValidDate') and the daily totals were calculated, adjusting for weekends and federal holidays.  
Model, Interpret - SARIMA
Next, the data was investigated for suitability with an ARMA model. The data was found to be highly seasonal, so several regularization measures were attempted, to no avail. Because of the high seasonality, it was determined that SARIMA would be more appropriate. A grid search was used to determine the best values of p, d, and q based on AIC. This search took several hours to run, even with limited numbers in the grid. Once the parameters were generated, a SARIMA model was fit on the training data and used to generate a forecast 251 steps (1 year, minus weekends and holidays) into the future. Because we had the fortune of having the actual data for 2018, the MSE was calculated between the actual data and the forecast. If the model predicted a negative value, it was reset to 0, because you can't dispense negative licenses. The MSE was 145107.43.  
The conclusions drawn from the SARIMA model are as follows. The best simple model would be a SARIMA model, although the long grid search time is a definite negative. The MSE is not terrible, but a lower error would improve the ability to make real world decisions. If this model is used, it should be used in conjunction with historical data, and even short term past data because it does not handle seasonality very well.  
Model, Interpret - LSTM  
To further explore the power of this large dataset, and extrapolate on the idea of using short term trends for forecast, an LSTM neural network was developed. First, the data had to be manipulated in a way that LSTM could accept. A preliminary model was built, and then tweaked for the lowest possible MSE. It should be noted that with neural networks, there can always be more tuning. I would suggest training on a larger dataset and experimenting with different lag sizes. The final model for this project was an LSTM network with 5 lags. Next a function was written that takes in a pre-made dataframe that has a time stamp and the first 5 data points that the LSTM should train on. It then recursively makes predictions for the specified number of steps and appends them to the dataframe. The output is a timestamped dataframe with a scaled prediction value, and an inverse-scaled value for calculating MSE.  
Using this recursive function to predict 251 steps (as we did with the SARIMA model) yielded an MSE of 115307.38. This was lower than the SARIMA model and also took less time to train. Because the function is recursive, it starts to converge on itself, so it is not recommended to use it for a large period of time. The accuracy decreases as the model converges. Overall, because of the higher accuracy and the lower training time, it is recommended to use this LSTM model for making future forecasts. It would be more reliable to make business decisions.  

## Future Work  
This dataset is a treasure trove, and there a few next steps that would be valuable to take.  
* Research should be done as to why there are spikes in December and January. Are these true spikes, or is a "dummy" value being imputed when the actual date is not available?  
* Reframing the data into a SQL database could open the door to answering questions regarding relationships between variables, and help to explore this data further.  
* Training the data on more years would ultimately make the models smarter and better able to forecast.  
* Creating a multivariate LSTM model using the categorical variables such as breed, color, or zip code could potentially yield some very interesting results. 
