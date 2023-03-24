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
    <li><b>US State where the Outage is Happening:</b> Knowing the state where the outage is happening is important because it helps in determining the the policies, resources and the infrastructure available to handle power outages in that state. For any power outage that is happening at a given time, we would know it's affected geographical location and ultimately its State</li><br>
    <li><b>Climatic Region of the State where the outage is Occuring:</b> Climatic conditions like hurricanes, rain and snow can affect the supply of power and damages caused due to severe weather conditions. For any power outage that is happening at a given time, we would know it's affected geographical location and ultimately its climatic and weather conditions</li><br>
    <li><b>Time stamp of when the Outage started:</b> During Off-work hours and weekends, there is a likelyhood of higher intentional attacks to power grids due to less security. For any power outage that is currently happening, we are likely to know what time it started.</li><br>
    <li><b>Total Price of Electricity in the Area where the Outage is Happening:</b> The price of electricity in the area where the outage is occurring is important because it can indicate the infrastructure available to maintain the power grid and the incentives for the utility company to invest in proper infrastructure. It is easily available through official government websites, utility company websites, or public databases.</li><br>
    <li><b>Percentage of Land in the Area where the Outage is Happening:</b> This information is required to determine the size and scope of the outage, particularly for larger regions. The percentage of land in the area can be obtained through geographic information system (GIS) data or through government agencies. </li><br>
    <li><b>Total Number of Customers in the Area where the Outage is Happening:</b> This information would help us know potenitally how many people can be affected by this outage. Additionally, with more customers, power grid corporations are more likely to invest in more stable infrastructure. This information should be available to power corporations trying to predict outages.</li><br>
    <li><b>Percentage of Urban Customers in the Area where the Outage is Happening:</b> This information is required to determine the impact of the outage on urban areas, particularly for emergency response efforts. Additionally, in more urbanized areas, the power corporations are more likely to upkeep latest infrastructure. The percentage of urban customers can be obtained from census data or through government agencies.</li><br>
    <li><b>Percentage of Inland Water in the Area where the Outage is Happening:</b> This information is required to assess the potential impact of the outage on water resources, particularly for areas that rely on electricity for water treatment and distribution. The percentage of inland water can be obtained through GIS data or through government agencies.</li><br>
    <li><b>The oceanic El Niño/La Niña (ONI) index at the place where the outage is occuring:</b>This information is required to determine the potential impact of the El Niño or La Niña weather patterns on the outage, particularly in coastal regions. The ONI index can be obtained through the National Oceanic and Atmospheric Administration (NOAA)</li>
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

5. The `DEMAND.LOSS.MW` column has too many NaN values. Furthermore, there it does not consistently report the same information, so we decided to drop that column from our analysis.


After thoroughly cleaning the dataset, here's a preview of our cleaned dataframe for power outages:

<iframe src="Plots/cleaned_df.html" width=800 height=320 frameBorder=0></iframe>

## **Baseline Model**

Initially, to build our classifier, we will train the model on the following features:
<ul>
    <li> <b>U.S._STATE (Qualitative Nominal)</b>: The state in which the power outage took place can provide the model with valuable information because some states are more prone to certain kinds of outages than others. A state may be more prone to hurricanes than others, and this would be valubale to the model in its prediction of cause.</li>
    <li> <b>CLIMATE.REGION (Qualitative Nominal)</b>: Climate region tells the model about the climate conditions of the state in which the outage has taken place. Climate conditions would likely have a causal relationship with the nature of the outage, thus helping the model better classify outages based on cause.</li>
    <li> <b>CLIMATE.CATEGORY (Qualitative Ordinal)</b>: Describes the oceanic El Nino / La Nina index as an ordinal variable. This is another climate-related variable that is related to the year, rather than the region. This will allow our model improve its classification by taking temporal factors into consideration</li>
    <li> <b>YEAR (Quantitative Discrete)</b>: This is an important factor that will help other features, like CLIMATE.CATEGORY to be of value to the model. Furthermore, this column may also encapsulate some trend in the distribution of the CAUSE.CATEGORY that may be useful to the model.</li>
    <li> <b>MONTH (Quantitative Discrete)</b>: The month in which an outage took place may provide the model with valuable information about the chances of each of the CAUSE.CATEGORYs occurring. For instance, winter time may see more outages in snowy regions due to weather.</li>
    
</ul>

To build our initial model, we will be using a <a href='https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html'>RandomForestClassifier</a> as they are an ensemble method that combines the results of multiple decision trees. This approach can help to reduce overfitting and improve the accuracy and stability of the prediction model. Additionally, random forest algorithm can handle nonlinear relationships between the input features and the response variable. This is important for power outage prediction, as there may be complex relationships between different factors such as weather patterns, infrastructure age, and population density. Thereby, for the baseline model, RandomForest seem to better than just training regular decision trees. 

**Feature Engineering**

For the Qualitative features, we will apply OneHotEncoding so that our model can properly account for the different categorical features. We will achieve this using the OneHotEncoder class and fitting it in a ColumnTransformer()

For example, suppose we have a categorical variable "color" with three categories: "red," "green," and "blue." To encode this variable using one-hot encoding, we would create three binary vectors, one for each category:

"red" : [1, 0, 0]
"green" : [0, 1, 0]
"blue" : [0, 0, 1]

As we have 3 categorical columns, we built a pipeline by using the ColumnTransformer to OneHotEncode the `U.S._STATE`,`CLIMATE.REGION`,`CLIMATE.CATEGORY` columns and letting `YEAR` and `MONTH` to passthrough. We then fit the pipeline on our training data and transformed the training and test data.

**Model Fitting**

To train and test our model, we will be using the standard 75-25 test-train split where 75% of data is used for training and 25% is used for testing.

Here's how our fitted model looks like:

<img src='Plots/fittedbaseline.png'>

**Model Evaluation**

Now, that we have trained our model, let's test it's performance on our training data. For this we will be using the f1_score which is a harmonic average of both precision and recall.

F1 Score: 0.917
Confusion Matrix:

<iframe src="Plots/baseline_train.html" width=800 height=320 frameBorder=0></iframe>

The confusion matrix shows the number of correct and incorrect predictions made by the model on the training data, broken down by class. The diagonal of the matrix represents the true positives for each class, while the off-diagonal elements represent false positives and false negatives.

In this case, the model appears to be performing well on the training set, with high numbers of true positives across most classes and relatively few false positives and false negatives. The F1 score of 0.917 is a measure of the overall performance of the model, taking into account both precision and recall, and indicates that the model is able to achieve high accuracy while minimizing false positives and false negatives.

Now let's test the model on the unseen test data:

F1 Score: 0.688

In this case, the model appears to be struggling to correctly classify some of the classes, as seen in the low number of true positives and high number of false positives and false negatives. The F1 score of 0.688 indicates that the model is not performing well overall, with a relatively high number of false positives and false negatives. This suggests that the model may be overfitting to the training data, or may not have enough information to accurately distinguish between the classes. This means that our model is not generalizing well on unseen data.

<iframe src="Plots/baseline_test.html" width=800 height=320 frameBorder=0></iframe>

Below let's make a quick plot to plot the distribution of the F1 score on the test data.

<iframe src='Plots/baselinef1dist.html' width=800 height=320 frameBorder=0></iframe>

Based on the distribution of the model above, we can see that the f-1 score ranges anyhere from 0.63 to 0.76 with the median F-1 score of 0.69 which shows that the model performs decently on the test split considering the variability and structure of the power outage dataset. However, as we have not tuned the model with it's hyperparameters we can see that it is overfitting to the training dataset. This is evident by looking at the massive f-1 value gap for the training and test split. It is making a lot of False Positive and False Negative predections except for columns 3 and 5, which could potentially be the more popular cause classes. We belive that the model can certainly be improved with more selected feature engineering and proper hyperparameter tuning 🎛️.

## **Final Model**

To build our enchanced classifier, we will train the model on the following additional features:
<ul>
    <li> <b>ANOMALY.LEVEL (Qualitative Ordinal)</b>: This column contains more detailed information about the oceanic El Nino / La Nina index. The CLIMATE.CATEGORY feature has essentially been engineered by ordinalizing the data from this column. We can try and see if using a column with more detailed information can help improve our model's performance.</li>
    <li> <b>DAY_OF_WEEK (Quantitative Discrete)</b>: The day of the week when the outage occurs can affect the CAUSE.CATEGORY because causes may be more likely on some days than others. For instance, system operability disruptions may be more common over the weekends than weekdays because of lazier supervision.</li>
    <li> <b>TIME_OF_DAY (Quantitative Continuous)</b>: The time of the day may also correlate with the CAUSE.CATEGORY because certain times of the day may experience lower consumption of power. For instance, power consumption is generally lower during the night because most businesses are closed.</li>
    <li> <b>TOTAL.PRICE (Quantitative Continuous)</b>: If prices are high in a particular state, there will likely be some unrest from the general public. Two of the CAUSE.CATEGORYs are 'public appeal' and 'intentional attack,' which could be a likely factors in states with higher electricity prices. Furthermore, prices are high most likely because of high demand, so high demand for power may result in outages as well.</li>
    <li> <b>TOTAL.CUSTOMERS (Quantitative Discrete)</b>: More customers in a state may increase the likelihood of certain outages. For instance, the number of customers may be correlated with the probability of seeing an 'intentional attack.'</li>
    <li> <b>PCT_LAND (Quantitative Continuous)</b>: The state's land area normalized by the total area of the US can provide us with a sense of vast the state. The vastness of the state may increase its likelihood of experiencing outages.</li>
    <li> <b>PCT_WATER_INLAND (Quantitative Continuous)</b>: Inland water area in the state normalized by the total inland water are in the US may provide the model with more information about the state's general geographic conditions. Geographical factors could play a major role in predicting outage cause.</li>
    <li> <b>AREAPCT_UC (Quantitative Continuous)</b>: Area encapsulated by urban clusters normalized by the total area of the state may provide the model with an idea of how much of a state is urbanized. Urban areas tend to draw more power than their counterparts, so certain outages may be more frequent in states with high values for this feature</li>
</ul>

Below is the different cause categories in our data for reference:

<img src='Plots/cause_dist.png'>

### **Feature Engineering**

We can apply a StandardScaler() to the TOTAL.PRICE column in order to reduce the impact of outlying prices.

We can apply a QuantileTransformer() to the PCT_LAND, PCT_WATER_INLAND, AREAPCT_UC and TOTAL.CUSTOMERS columns to essentially classify outages based on the level of each variable. These variables are valid for this transformation because they have a lot of unnecessary detail. Instead of emphasizing the value itself, we can classify the values and potentially improve overall classification and prevent any potential overfitting.

The Standard Scaler converts the data to a normal distribution with a mean of 0 and a standard deviation of 1. The Quantile Transformer converts the data to a uniform distribution with a mean of 0 and a standard deviation of 1. The Quantile Transformer is a non-linear transformation that is robust to outliers.

To apply the transformations, we dropped the columns that we did not want to transform. Here's what the dataframe that we passed into the model looks like:

<iframe src='Plots/final_model_df.html' width=800 height=320 frameBorder=0></iframe>

### **Model Fitting**

To train and test our model, we will be using the standard 75-25 test-train split where 75% of data is used for training and 25% is used for testing.

Here's how our fitted model looks like:

<img src='Plots/fittedfinal.png'>

## **Untuned Model Testing**

Testing the untuned final model on the training data:

F1 Score: 0.9991170653185345

<iframe src='Plots/final_untuned_train.html' width=800 height=320 frameBorder=0></iframe>

The model appears to be performing very well on the test data, with high numbers of true positives across most classes and relatively few false positives and false negatives. The F1 score of 0.999 is a measure of the overall performance of the model, taking into account both precision and recall, and indicates that the model is able to achieve high accuracy while minimizing false positives and false negatives.

Testing the untuned final model on the unseen test data:

F1 Score: 0.7115392093891811

<iframe src='Plots/final_untuned_test.html' width=800 height=320 frameBorder=0></iframe>

### **Analysis of performance**

Our new model with the additional features and feature engineering seems to be doing only slightly better on test data. An F-1 score of 71% is an improvement, nevertheless. 

We can do better by tuning the hyperparameters 🎛️

Here's a list of potential hyper parameters that we have identified to tune our model:

1. Random Forest 'n_estimators':- This hyperparameter controls the number of decision trees in the random forest. Increasing the number of trees can improve the model's accuracy, but it can also increase the model's computational cost. The given range of values is reasonable to try out different values and see the model's performance.

2. Random Forest 'max_depth': This hyperparameter controls the maximum depth of the decision trees. A shallow tree is less likely to overfit, but it might not capture all the patterns in the data. A deep tree is more likely to overfit, but it can capture more complex patterns. The 'None' option means that there is no maximum depth limit, which can lead to overfitting.

3. Random Forest 'criterion': This hyperparameter controls the quality of the split in each decision tree. Gini impurity and entropy are two common measures of impurity used in decision trees. Gini impurity tends to be faster to compute, while entropy tends to create more balanced splits.

4. Random Forest 'max_features': This hyperparameter controls the number of features considered for each split in the decision trees. 'sqrt' means the square root of the number of features, while 'log2' means the logarithm of the number of features. A lower number of features can reduce the model's complexity and overfitting, but it can also decrease the model's accuracy. Again, trying both options is a reasonable approach.

5. Quantile Transformer 'n_quantiles': These hyperparameters are specific to the feature engineering pipeline and control the number of quantiles used to break down the continuous variables 'land' and 'area'. The range of values given is reasonable, and the final choice would depend on the distribution of the variables and the resulting performance of the model.

6. Random Forest 'min_samples_split': This hyperparameter controls the minimum number of samples required to split an internal node in the decision trees. A higher number can reduce the model's complexity and overfitting, but it can also decrease the model's accuracy. The given range of values is reasonable, and the final choice would depend on the size and complexity of the dataset.

Let's run a Grid Search with a 5-fold cross validation to find the best performing hyperparamters and then test our model out again.

After doing some quick testing, we found that the best hyperparameters for the Random Forest Classifier are:

<img src='Plots/hyperparams.png'>

