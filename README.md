Report by Saarth Shah and Saathvik Dirisala

## **Introduction**

### Introduction and Prediction Question Identification

For this study, we will focus on:

`People are extremely dependent on power to get on with their daily lives. In this study, we want to predict what might cause a power outage based on information that would intuitively be available to a person "during" or at the "beginning" of an outage.`

<b>Knowing what might have caused an outage have some of the following benefits:</b>

<ul>

<li>Improving Power Grid Recovery: By understanding the common causes of power outages, power grid operators can take steps to improve the ability of the power grid to undo damages and reduce the likelihood of future outages.</li>

<li>Faster Response to Outages: If the cause of a power outage can be quickly identified, power grid operators can respond more quickly to restore power to affected areas, reducing the duration of the outage and minimizing the impact on people's daily lives.</li>

<li>Planning for Outages: Knowing the likely causes of power outages can help people and communities plan for potential outages and take steps to mitigate their impact. For example, people may choose to have backup generators or stock up on essential supplies during periods of high risk.</li>
    
</ul>

<b>Therefore for this study we will be building a multi-class classifier to predict `CAUSE.CATEGORY.` To test this model we will be using the <b>F-1 score</b> as a metric to test the strength of our classifier due to the following reasons:</b>

<ul>
    <li>The proportion of power outages in our dataset caused due to severe weather were around 0.497392, so if our classifier simply always predicts 'severe weather' as a category then it might achieve a high accuracy. However, as we account for both recall and precision in F-1 score, it will be a better test to gauge the strength of our classifier.</li>
    <li>As the number of instances in each class may be too small to provide accurate predictions, F1 score can be more useful than accuracy in such cases because it the utlizes both precision and recall, which help ensure that all classes are correctly identified.</li>
</ul>

<b>Model Type:</b> Multiclass Classifier <br>
<b>Response Variable:</b> `CAUSE.CATEGORY`<br>
<b>Metric:</b> F-1 Score<br>

<b>Information needed during the “time of prediction”:</b>
<ul>
    <li>US State where the Outage is Happening</li>
    <li>Climatic Region of the State where the outage is Occuring</li>
    <li>Time stamp of when the Outage started</li>
    <li>Total Price of Electricity in the Area where the Outage is Happening</li>
    <li>Percentage of Land in the Area where the Outage is Happening</li>
    <li>Total Number of Customers in the Area where the Outage is Happening</li>
    <li>Percentage of Urban Customers in the Area where the Outage is Happening</li>
    <li>Percentage of Inland Water in the Area where the Outage is Happening</li>
    <li>The oceanic El Niño/La Niña (ONI) index at the place where the outage is occuring</li>
</ul>


In this notebook we will be working with the <a href="https://engineering.purdue.edu/LASCI/research-data/outages/outagerisks">Power Outage</a> dataset to potentially find patterns that might be affecting how long a power outage lasts. Our dataset gives us access to the regional details of the outage, the prices of electricity, outage cause, people affected, etc. The relevant columns in our dataset mimic the information we would potenitally have at the onset of an outage.


Here's a quick description of the relevant columns in our dataset:

`YEAR`: Contains the year when this event happened <br><br>
`U.S._STATE`: Name of the US State where the Outage Happened <br><br>
`CAUSE.CATEGORY`: The reported reason for the Outage <br><br>
`CLIMATE.CATEGORY`: Current climate where the outage was reported <br><br>
`OUTAGE.START.DATE`: Date when the outage was reported <br><br>
`OUTAGE.START.TIME`: Time when the outage was reported <br><br>
`CLIMATE.REGION`: Climate in the specified region where the outage was reported <br><br>
`ANOMALY.LEVEL`: Represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season.<br><br>
`MONTH`:  Contains the month when this event happened.<br><br>
`DAY_OF_WEEK`:  Contains the day of the week (Monday, Tuesday, etc) when this event happened.<br><br>
`TIME_OF_DAY`:  Contains the time of the day when this event happened.<br><br>
`TOTAL.PRICE`:  Contains the total price of electricity in the area where the outage happened.<br><br>
`PCT_LAND`:  Contains the percentage of land in the area where the outage happened.<br><br>
`TOTAL.CUSTOMERS`:  Contains the total number of customers in the area where the outage happened.<br><br>
`AREAPCT_UC`:  Contains the percentage of urban customers in the area where the outage happened.<br><br>
`PCT_WATER_INLAND`:  Contains the percentage of inland water in the area where the outage happened.<br><br>

## **Cleaning and EDA**

### **Data Cleaning**

Our data for this study is provided in an excel sheet that has certain useless columns that we can avoid during our analysis.
![Excel Sheet Preview](https://i.imgur.com/79yPXtg.png)

Before beginning the data cleaning, we first had to read the data properly by ensuring that pandas does not read these specific columns.

Our next step was to clean the dataset in order to make the rows and columns usable for our study. Here are some of the steps we took to properly clean the dataset:

1. The power outage start date and time is given by `OUTAGE.START.DATE` and `OUTAGE.START.TIME` which are two seperate columns. Aggregating the dates and times into a singular datetime object allowed us to quickly look into when the outages are starting with an exact time, without having to look into two seperate columns.

2. As our study emphasizes on the prediction of the cause of the power outage and other columns, it would make sense for us to drop any datapoints where we do not have a reported cause outage duration. Thereby dropping all columns where the cause of the power outage is null.

3. Additionally, we would also be factoring in the time of the day when this power outage occured by converting the time into seconds for us to easily analyze the numerical values.

4. We also dropped the `OUTAGE.RESTORATION.DATE`, `CUSTOMERS.AFFECTED`, `OUTAGE.DURATION` and `OUTAGE.RESTORATION.TIME` columns as we would likely not have access to these values at the time of predection.

5. The DEMAND.LOSS.MW column has too many NaN values. Furthermore, there it does not consistently report the same information, so we decided to drop that column from our analysis.


After thoroughly cleaning the dataset, here's a preview of our cleaned dataframe for power outages:

<iframe src="Plots/cleaned_df.html" width=800 height=1200 frameBorder=0></iframe>

### **Univariate Analysis**

**Distribution of Power Outage Durations**

<iframe src="Plots/univplot1.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="Plots/univplot2.html" width=800 height=600 frameBorder=0></iframe>

These two plots help us understand the distribution of the how long these power outages last. In plot 1, we broke down the duration in minutes into 10 different bins. Plot 2, does something similar where the duration is in number of days. Looking at these two plots we can infer that most of the power outages that occured lasted less than 1 day and most of them resolving before 5 days. However there are some outlier cases where these outages lasted upto 30 days.

**Distribution of Power Outages per Year**

<iframe src="Plots/uniplot3.html" width=800 height=600 frameBorder=0></iframe>

While the dataset tells us that the power outages are between 2010 and 2016, it is still important to know the distribution of these outages per year to get a better idea of our dataset before any analysis. Looking at the chart above we can see that in our dataset, the mass of the power outages (68%) we have collected information about happened between 2008 and 2015, with the maximum number of outages (269) recorded in 2011.

**Note**: We do not know if our dataset contains **all** the outages that happened between 2000 and 2016, so our statement is only helpful for us to understand our sample and not the entire population.


### **Bivariate Analysis**

**Outage Duration Distributiom by U.S. States**

<iframe src="Plots/bivarplot1.html" width=800 height=1200 frameBorder=0></iframe>

Above is a histogram plot of `OUTAGE.DURATION` by U.S. States in order to study the shapes of the distributions. The y-axis contains the duration in minutes. The conditional distributions above tell us that most of the U.S. States have a similarly right-skewed distribution as the overall `OUTAGE.DURATION` distribution. This means that there is no single state amongst those with more than 1 power outage in which the outage durations are not right skewed by large outlier values.

**Outage Duration versus Customers Affected**

<iframe src="Plots/bivarplot2.html" width=800 height=1200 frameBorder=0></iframe>

Based on the plot above, there seems to be a line of fit but the points seem to be heavily clustered towards the origin. We weren't able to find any visual cues that might hint towards a correlation so we decided to calculate the correlation coefficient and the results of the OLS regression that was used to find the trendline.

![OLS Regression Results](https://i.ibb.co/87NyLFb/Screenshot-at-Feb-23-18-26-46.png)

Based on the correlation coefficient of 0.26, there appears to be a positive correlation between the number of customers affected and the outage duration. However, this strength of the correlation is considered to be moderate, meaning that the relationship between the variables is not particularly strong. Based on this, we can only say that the two variables may have a positive correlation but the trend is not strong enough to come to a conclusion.

### **Interesting Aggregates**

| U.S._STATE           |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|:---------------------|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
| Alabama              |               nan   |                   nan   |                 77   |       nan   |           nan   |           1016.5 |                           nan   |
| Arizona              |                67.5 |                   nan   |                180   |       nan   |           nan   |          26760   |                           384.5 |
| Arkansas             |               105   |                   nan   |                175   |         3   |           300   |           1213.5 |                           nan   |
| California           |               269   |                   882.5 |                117   |       128.5 |           420   |            962   |                           199   |
| Colorado             |               nan   |                   nan   |                105   |         2   |           nan   |           2425   |                           337.5 |
| Connecticut          |               nan   |                   nan   |                  1   |       nan   |           nan   |           2040   |                           nan   |
| Delaware             |                50   |                   nan   |                  1   |       nan   |           nan   |           2153.5 |                           nan   |
| District of Columbia |               159   |                   nan   |                nan   |       nan   |           nan   |           2425   |                           nan   |
| Florida              |               308.5 |                   nan   |                 50   |       nan   |          4320   |           4335.5 |                           121.5 |
| Georgia              |               nan   |                   nan   |                108   |       nan   |           nan   |           1416   |                           nan   |
| Hawaii               |               nan   |                   nan   |                nan   |       nan   |           nan   |            955   |                           237   |
| Idaho                |               nan   |                   nan   |                180   |       nan   |          1548   |            nan   |                           220   |
| Illinois             |               149   |                  2761   |               1450   |       nan   |           120   |           1200   |                           nan   |
| Indiana              |                 1   |                 12240   |                 49.5 |        96   |           nan   |           3022   |                            65   |
| Iowa                 |               nan   |                   nan   |               2161   |       nan   |           nan   |           1044   |                           nan   |
| Kansas               |               nan   |                   nan   |                187   |       nan   |           913   |          13650   |                           nan   |
| Kentucky             |               652   |                 12570   |                108   |       nan   |           nan   |           1680   |                           nan   |
| Louisiana            |               227   |                 28170   |                nan   |       nan   |           533.5 |           3063.5 |                           576   |
| Maine                |               nan   |                  1676   |                  0.5 |       881   |           nan   |           1112   |                           nan   |
| Maryland             |               nan   |                   nan   |                  1   |       nan   |           nan   |           3050   |                           304   |
| Massachusetts        |               nan   |                  2891   |                 64.5 |       nan   |           nan   |            480   |                            67   |
| Michigan             |               761   |                   nan   |               1408   |         1   |          1078   |           4259   |                          2694   |
| Minnesota            |               nan   |                   nan   |                 78   |       nan   |           nan   |           3000   |                           nan   |
| Mississippi          |               nan   |                   nan   |                  5   |       nan   |           nan   |            nan   |                           300   |
| Missouri             |               nan   |                   nan   |                 25   |       nan   |           nan   |           2880   |                            65   |
| Montana              |               nan   |                   nan   |                 93   |        34.5 |           nan   |            nan   |                           nan   |
| Nebraska             |               nan   |                   nan   |                nan   |       nan   |           159   |             60   |                           nan   |
| Nevada               |               nan   |                   nan   |                108   |       nan   |           nan   |            nan   |                           nan   |
| New Hampshire        |               nan   |                   nan   |                 53   |       nan   |           nan   |           1597.5 |                           nan   |
| New Jersey           |               nan   |                   nan   |                  0   |       nan   |           nan   |           5115   |                           748.5 |
| New Mexico           |               nan   |                    76   |                126.5 |       nan   |           nan   |            nan   |                             0   |
| New York             |               247   |                 15855   |                 17.5 |       nan   |          2760   |           4710   |                           191   |
| North Carolina       |               nan   |                   nan   |                484   |       nan   |           nan   |           1336.5 |                            45   |
| North Dakota         |               nan   |                   nan   |                nan   |       nan   |           720   |            nan   |                           nan   |
| Ohio                 |               nan   |                   nan   |                 41   |       nan   |           nan   |           3660   |                          1744.5 |
| Oklahoma             |               nan   |                   nan   |                110   |       984   |           431   |           2895   |                           nan   |
| Oregon               |               200   |                   nan   |                 73   |       nan   |           nan   |           1526   |                           nan   |
| Pennsylvania         |               376   |                   nan   |                903   |       nan   |           nan   |           3059   |                           329   |
| South Carolina       |               nan   |                   nan   |                nan   |       nan   |           nan   |           2947.5 |                           nan   |
| South Dakota         |               nan   |                   nan   |                nan   |       120   |           nan   |            nan   |                           nan   |
| Tennessee            |               404   |                   nan   |                 48   |       nan   |          2700   |            750   |                            20   |
| Texas                |               318   |                 20160   |                 74   |       nan   |           330   |           1725   |                           322   |
| Utah                 |                15   |                   nan   |                 15   |       nan   |          2275   |            957   |                           537.5 |
| Vermont              |               nan   |                   nan   |                 15   |       nan   |           nan   |            nan   |                           nan   |
| Virginia             |               nan   |                   nan   |                  2   |       nan   |           683.5 |            597   |                           241   |
| Washington           |              1204   |                     1   |                 98.5 |        21   |           248   |           3896   |                            25   |
| West Virginia        |               nan   |                   nan   |                  1   |       nan   |           nan   |           9576   |                           nan   |
| Wisconsin            |               nan   |                 13564   |                 90   |       nan   |           388   |            960   |                           nan   |
| Wyoming              |                61   |                   nan   |                  0   |        32   |           nan   |            106   |                           nan   |

Above is the pivot table which showcases the median outage duration for the combination of states and outage causes. Looking at the results, we can see that some states like California & Illinois when paired with certain `OUTAGE.CAUSE` have higher outage durations than other states like Montana and Nebraska. Therefore, there might be some correlation between the state and outage cause on the outage duration. This is tested further in our Hypothesis test.

## **Assessment of Missingness**

### **NMAR Analysis**

The `OUTAGE.RESTORATION` column, which is an aggregation of the `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` columns from the original dataset, is potentially **NMAR**, or not missing at random. The `OUTAGE.RESTORATION` column contains information about when the power was restored and the outage, resolved. It is possible that this column has missing values because the record-keeper for the power outage data may have been sick or on leave on the day of the restoration. This could make the column **NMAR** and not MCAR because the `OUTAGE.RESTORATION` column would not be random and, additionally, also be dependent on an external variable, i.e., the presence of the record-keeper for the power outages. If we had data on the presence of the record-keeper on the day of restoration, then we would be able to conclude whether the variable    `OUTAGE.RESTORATION` is MAR (Missing at random).

### **Missingness Dependency**

#### 1. Why TVD for checking Missingness?
**TVD (Total Variation Distance)** is a test statistic that is used to compare categorical distributions of a specific variable. For missingness, when we split our data into two sets based on whether data in a certain column is missing, we look at the categorical distributions of the other columns to see if there is any significant difference. For instance, below, we explore the missingness of `CUSTOMERS.AFFECTED` in relation to the columns `CLIMATE.CATEGORY` and `U.S._STATES`. Both these columns are used to classify data and, hence, are categorical. For this reason, we used TVD as our test statistic in our missingness analysis.

#### 2. Identifying a column with potentially MAR data
Our column for `CUSTOMERS.AFFECTED` seems to be missing some values. However, this column does not seem to be missing values due to Design (MD) and contains both extremely large and small values (0 to 3241437 people). In this section, we test whether the missingness of customers affected depends on another column or not.

- **`CLIMATE.CATEGORY` column**<br>

First let's test if the missingess of the `CUSTOMERS.AFFECTED` value depends on the climate of the place where the power outage was recorded. For this, we will first draw a simple plot to check if there is a visual difference between the null and non-null distribution values of `CLIMATE.CATEGORY`

<iframe src="Plots/permplot1.html" width=800 height=600 frameBorder=0></iframe>

In this chart, the distribution between the null and non-null values seem to be fairly similar. We can further investigate by conducting a permuation test to check if this difference in distribution was purely due to chance or if the `CLIMATE.CATEGORY` has a correlation with the missingnes of `CUSTOMERS.AFFECTED`.

**Permutation Test Results:**

Observed TVD = 0.03 <br>
P-value = 0.592 <br>
Signficance level (alpha) = 5% <br>

<iframe src="Plots/permplot2.html" width=800 height=600 frameBorder=0></iframe>

Our p-value of 0.592 is much bigger than our significance interval of 5%, therefore we do not have enough evidence to reject the null hypothesis. Based on this we cannot conclude that the missingness of the `CUSTOMERS.AFFECTED` values depends on the `CLIMATE.CATEGORY` column.

Therefore we **cannot say** that missingness of the `CUSTOMERS.AFFECTED` values is **Missing at Random** due to its correlation with the `CLIMATE.CATEGORY` column.

- **`U.S._STATES` column** <br>

Now, let's test if the missingess of the `CUSTOMERS.AFFECTED` value depends on the US State where the power outage was recorded. For this, we will first draw a simple plot to check if there is a visual difference between the null and non-null distribution values of `U.S._STATE`.

<iframe src="Plots/permplot3.html" width=800 height=600 frameBorder=0></iframe>

There seems to be a significant difference in values in the null and non-null distributions of the `CUSTOMERS.AFFECTED` values. We can further investigate by conducting a permuation test to check if this difference in distribution was purely due to chance or if the `CUSTOMERS.AFFECTED` has a correlation with the missingnes of `U.S._STATES`.

**Permutation Test Results:**

Observed TVD = 0.37 <br>
P-value = 0.0 <br>
Significance level (alpha) = 5% <br>

<iframe src="Plots/permplot4.html" width=800 height=600 frameBorder=0></iframe>

As the p-value is 0 which is less than our 5% confidence level, we have **sufficient evidence** to reject the null hypothesis and **state** that the missingness of the `CUSTOMERS.AFFECTED` column is **MAR** by The `U.S._STATE` column.

## **Hypothesis Testing**

We can now test some hypotheses. Below is a function that takes in a column name and compares category-wise outage medians to the overall population outage median.

Note: We choose the median as our test statistic here because the overall duration distribution is skewed right, so the mean value will be biased by potential outliers.

#### **Outage Cause**
Firstly, we want to compare the median outage duration of each class in the CAUSE.CATEGORY column to the overall population median.

Null Hypothesis: The median duration of each class in the `CAUSE.CATEGORY` column is equal to the overall population median.
Alternative Hypothesis: The median duration of each class in the `CAUSE.CATEGORY` column is not equal to the overall population median.

Below is a dataframe that summarizes the results of hypothesis tests done to compare individual median categorical durations to the overall median duration.

|    | Category                      | H0     | H1     |   Observed Median |   P-value | Reject   |
|---:|:------------------------------|:-------|:-------|------------------:|----------:|:---------|
|  0 | equipment failure             | m = m0 | m < m1 |             221   |    0.0088 | True     |
|  1 | fuel supply emergency         | m = m0 | m > m1 |            3960   |    0      | True     |
|  2 | intentional attack            | m = m0 | m < m1 |              56   |    0      | True     |
|  3 | islanding                     | m = m0 | m < m1 |              77.5 |    0      | True     |
|  4 | public appeal                 | m = m0 | m < m1 |             455   |    0.2416 | False    |
|  5 | severe weather                | m = m0 | m > m1 |            2460   |    0      | True     |
|  6 | system operability disruption | m = m0 | m < m1 |             215   |    0.0001 | True     |

Here is a bar chart comparing the medians of each of the groups:
<iframe src="Plots/hypplot0.html" width=800 height=600 frameBorder=0></iframe>

Analysis: Based on our hypothesis tests, it seems likely that the median duration of outages is less than the overall median duration for outages caused by equipment failures, intentional attacks, islanding, or system operability disruptions. Furthermore, the median duration is likely greater than the overall median duration for outages caused by fuel supply emergencies or severe weather. It is likely that outages caused by severe weather take longer to resolve because the restoration team is less in control of factors influenced majorly by nature, like weather.

#### **Day of the Week**
We can also compare the median outage duration of each class in the DAY_OF_WEEK column to the overall population median.

Null Hypothesis: The median duration of each class in the `DAY_OF_WEEK` column is equal to the overall population median.
Alternative Hypothesis: The median duration of each class in the `DAY_OF_WEEK` column is not equal to the overall population median.

Below is a dataframe that summarizes the results of hypothesis tests done to compare individual median categorical durations to the overall median duration.


|    |   Category | H0     | H1     |   Observed Median |   P-value | Reject   | Day_of_the_Week   |
|---:|-----------:|:-------|:-------|------------------:|----------:|:---------|:------------------|
|  0 |          0 | m = m0 | m < m1 |             430   |    0.0362 | True     | Monday            |
|  1 |          1 | m = m0 | m < m1 |             420   |    0.0274 | True     | Tuesday           |
|  2 |          2 | m = m0 | m < m1 |             510   |    0.1529 | False    | Wednesday         |
|  3 |          3 | m = m0 | m < m1 |             550   |    0.2022 | False    | Thursday          |
|  4 |          4 | m = m0 | m > m1 |             763.5 |    0.3945 | False    | Friday            |
|  5 |          5 | m = m0 | m > m1 |            1847.5 |    0      | True     | Saturday          |
|  6 |          6 | m = m0 | m > m1 |            1080   |    0.0594 | False    | Sunday            |

Here is a bar chart comparing the medians of each of the groups:

<iframe src="Plots/hypplot1.html" width=800 height=600 frameBorder=0></iframe>

Analysis: Based on our hypothesis tests, it seems likely that the median duration of time for which there is an outage is lesser than the overall median outage duration on Mondays and Tuesdays. On the other hand, on Saturdays, the median outage duration is likely longer than the population median. This could potentially mean that restoration teams in the US work faster than the median speed of work to resolve power outages on Mondays and Tuesdays, and the same teams likely work slower on Saturdays.

### **A more focused hypothesis test**
​
We see that there are some interesting trends in the duration of the outages based on the day of the week. We can study these further by formulating another hypothesis test that checks to see if there is a significant difference between the median duration of outages on weekends and that of those on weekdays. More specifically, we can check to see if restoration takes more time if the outage begins on a weekend. Here are our hypotheses:
​
H0: There is no difference between the median duration of outages over weekends and the median duration of outages over weekdays.
​
H1: The median duration of outages over weekends is greater than the median duration of outages over weekdays.
​
In order to do this, we can run a permutation test with the test statistic as the signed difference between the median duration of outages for weekends and the median duration of outages for weekdays. Since our test is directional, it makes sense to not use the absolute difference between the two group medians as our statistic.

**Results:**

Observed Difference = 1035.0 <br>
P-value = 0.0 <br>
Significance level (alpha) = 5% <br>

<iframe src="Plots/hypplot2.html" width=800 height=600 frameBorder=0></iframe>

Conclusion: Based on the plot and our P-value of 0.0, at a significance level of 5%, we can reject the null hypothesis in favor of the alternative hypothesis. The median duration of outages over the weekend does seem to be greater than the median duration of outages over the weekdays. However, there could be **other confounding factors** that may be causing this difference.One possible explanation is that it is likely that restoration teams either don't work at all on weekends (delaying the restoration by days) or they just don't work as much on weekends as they do on weekends.

Our hypothesis test helps provide insight into our original question. Now that we know that our power outage median is higher on the weekends, it can provide insight to the relevant stakeholders on how to improve the restoration process. For example, if the restoration team is not working on weekends, then the stakeholders can try to convince the team to work on weekends. If the team is working on weekends, but not as much as they do on weekdays, then the stakeholders can try to convince the team to work more on weekends. This can help improve the restoration process and reduce the median duration of outages.





