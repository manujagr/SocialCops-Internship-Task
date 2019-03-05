# SocialCops-Internship-Task
The aim of this assignment is to measure key trends in the Agriculture sector in India and to understand trends in APMC (Agricultural produce market committee)/mandi price & quantity arrival data for different commodities in Maharashtra.
Objective of the assignment:
1.	Test and filter outliers.
2.	Understand price fluctuations accounting the seasonal effect
1.	Detect seasonality type (multiplicative or additive) for each cluster of APMC and commodities
2.	De-seasonalise prices for each commodity and APMC according to the detected seasonality type
3.	Compare prices in APMC/Mandi with MSP(Minimum Support Price)- raw and deseasonalised
4.	Flag set of APMC/mandis and commodities with highest price fluctuation across different commodities in each relevant season, and year.

## Insights from the Data
We were given two datasets for the analysis. First, the analysis of dataset “Monthly_data_cmo.csv” is done. It is found that no NULL values are present in the dataset. Also, it doesn’t have any duplicated values. The column “state name” is redundant as the analysing state is only Maharashtra. 
In the dataset  ”CMO_MSP_mandi.csv”, NULL values were present and were removed. Also the year column having values [‘2012’,’2013’] were removed as the first dataset has year values from 2014-2016.
## Methodology
### Outlier Detection
It is observed from the first dataset that this is a time-series data. Now, the first task is to remove the outliers based on column values. Now, we have numeric columns namely, “min price”, ”max price”, ”modal price”, “arrivals in qtl”.
Outliers can be removed directly from these columns separately but on observing the dataset it is found that we have available price of different types of commodities. It is likely to happen that price of different commodities will be different. Also, the price of same type of commodity will vary from year to year. Many factors like rainfall can influence the productivity and subsequently pricing of the commodity as the year changes. As observed in the dataset minimum price can be as low as 10 for math(bhaji) and as high as 142344 for cardamom.
**Hence, outliers are detected in each type of commodity for each year.**

Now, the task is find the method to filter the outliers. On plotting the histogram for a commodity in a specific year, it is found that the histogram is rightly skewed. On applying log transformation to the data, still we were not able to obtain the **Gaussian** or **Normal** distribution. So, we cannot apply the ~~standard deviation~~ method for the outlier detection.
![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/plot%201.png)
![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/log%20orange%202015.png)
  
Now, the best preferred method for such cases is by finding the **interquartile range** and then finding the outlier as this method works on median rather than mean as in the previous case. Here, first the inter-quartile range for each commodity in each year is found, then **lower bound** is set to **“quartile 1 – 1.5 * inter-quartile range”** and **upper bound** is set to **“quartile 3 + 1.5 * inter-quartile range”**.

![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/images-mod1-spread10.gif)

### Seasonality Detection
Now, after removing the outliers, we need to check whether the time series is stationary or not. Here, **Augmented Dickey-Fuller(ADF)** hypothesis test is used, which returns a p-value, **if p-value>0.05, then time series is non-stationary, otherwise not**. Non-stationary time series are filtered for the further analysis.
  
![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/time%20series%20plot.png)
![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/time%20series.png)

Now, the focus is to find the type of seasonality (“additive” or “multiplicative”) in the different cluster of APMC and commodity. Also, modal price is taken for analysing this. Examining each cluster by plotting the graph between and “date” and “modal price” will be a time-consuming process and less accurate method. **So, for detection of seasonality firstly, each cluster of APMC and commodity is decomposed into its substituents considering the time series as both additive and multiplicative. Now the auto-correlation between the residual price is calculated for both additive and multiplicative. If the value of auto-correlation for “additive” is high, then time series is “multiplicative” and vice-versa.**

### De-seasonalising the data
Now, after getting the type of seasonality present in the different cluster of APMC and commodity, “modal prices” are deseasonalised by subtracting the seasonal component from the “modal price”.


![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/satara%2Bpotato.png)
                                             *Satara+Potato*

![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/kurdwadi%20%2B%20sorgum(jawar).png)            *kurdwadi+sorgum(jawar)*

### Comparison of APMC/mandi price with the MSP
The modal price, de-seasonalised price and MSP price are compared on the basis of APMC name and the relevant commodity in that APMC.

![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/compare%201.png)
                                         *Satara+Potato*

![alt text](https://github.com/manujagr/SocialCops-Internship-Task/blob/master/Visualisation/compare%202.png)
                                         *kurdwadi+sorgum(jawar)* 
 
### Flagging the set of APMC/mandis and commodities with highest price fluctuation across different commodities
Now, fluctuation in the prices are calculated by differencing the “min price” column from the “max price” column. The grouping on the basis of Commodity, APMC and Year is done in order and then the maximum value of fluctuation is find among all the seasons or months in a given year. In this way, fluctuations are recorded for each year in a particular month.


