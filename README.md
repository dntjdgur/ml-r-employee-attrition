# Employee Attrition Predictive Analysis using R

## Project Purpose
The objective of this project is to analyze an open-source dataset from General Electric (GE) concerning employee attrition, focusing on their demographics. The goal is to predict attrition rates based on employees' demographic details.

## Problem Statement
General Electric's HR reports indicate that many high-potential employees are leaving the company for other opportunities. Middle managers have corroborated these reports, noting an increase in job postings. To maintain its competitive edge, GE plans to use a predictive model developed during the pilot stage to identify employees likely to leave and implement measures to retain high-potential employees.

## Data Source
Data source can be obtained from [Here](https://https://github.com/dntjdgur/ml-r-employee-attrition/tree/main/data)

## Preliminary Analysis Using CRISP-DM
### Business Understanding
General Electric is confronting a critical challenge: the departure of numerous high-potential employees to other opportunities. The underlying causes of this attrition are not well understood, making it difficult for the company to develop effective strategies to reduce future turnover.

In the interim, productivity in the affected departments likely declines as remaining employees cover the responsibilities of their departing colleagues. This situation can adversely affect office morale, causing employees to question the reasons behind the attrition of high-performing individuals. Such uncertainty can create a negative atmosphere and potentially lead to further attrition.

It is essential for GE to identify the factors contributing to employee attrition and predict which employees are at risk of leaving. This analysis seeks to provide predictive insights into employee attrition while identifying key causes. The central research question is: "What factors are causing employees to leave the company, and can addressing these factors reduce the attrition rate?" Answering this question will help GE retain high-potential employees and develop strategies to attract more talent.

### Data Understanding & Preparation
The dataset includes 35 columns and 1,270 rows, detailing each employee's information. The 'attrition' column indicates whether an employee has left the company.

#### Data Dimension Check
    dim(attrition);
    [1] 1270   35
    anyNA(attrition);
    [1] FALSE
    summary(attrition);

During the data preparation phase, variables such as employee count, employee number, and over-18 status will be excluded from the analysis, as they are constants and unlikely to influence the outcomes. The data will be cleaned by identifying and removing any null values.

Descriptive statistics will then be used to detect outliers, which could affect model accuracy. Outliers will be identified using R's summary() function and visually via histograms. These outliers will be removed to ensure model accuracy. Additionally, some data formats will be adjusted; for instance, 'attrition' and 'overtime' variables will be converted to Boolean values to facilitate logistic regression analysis. Categorical variables, such as education field, will be encoded numerically as per documented mappings.

![Summary Statistics Screenshot pt.1](https://github.com/dntjdgur/ml-r-employee-attrition/blob/main/images/summary_statistics.png)
![Summary Statistics Screenshot pt.2](https://github.com/dntjdgur/ml-r-employee-attrition/blob/main/images/summary_statistics-2.png)

As the next step, we will re-format the data types and initiate the data cleaning process. This re-formatting is essential for performing logistic regression analysis. Logistic regression generates binary responses, so the dataset must indicate employee attrition as either “Y” or “N.” The attrition column will be converted to numerical values, with “Y” transformed to 1 and “N” to 0. The following command can be used to make this transformation. The second command will ensure the column values are in numerical format, preventing the use of internal integer codes. Similar transformations will be applied to the Gender and OverTime columns: male will be coded as 0 and female as 1, while Yes will be coded as 1 and No as 0 in the OverTime column.

#### Data Transformation
    attrition$Attrition <- with(attrition, ifelse(attrition$Attrition == "Yes", 1, 0));
    attrition$Attrition <- as.numeric(as.character(attrition$Attrition));
    attrition$Gender <- with(attrition, ifelse(attrition$Gender == "Male", 0, 1));
    attrition$Gender <- as.numeric(as.character(attrition$Gender));
    attrition$OverTime <- with(attrition, ifelse(attrition$ OverTime == "Yes", 1, 0));
    attrition$OverTime <- as.numeric(as.character(attrition$OverTime));    

The next step involves checking if certain categorical variables are recognized as factor variables by R. The following commands should be executed for all categorical variables. The variables to be verified include: BusinessTravel, Department, Education, EducationField, EnvironmentSatisfaction, Gender, JobInvolvement, JobLevel, JobRole, JobSatisfaction, MaritalStatus, PerformanceRating, RelationshipSatisfaction, and WorkLifeBalance. 

#### Data Check
    is.factor(attrition$BusinessTravel);
    [1] FALSE
    is.factor(attrition$Department);
    [1] FALSE
    is.factor(attrition$Education);
    [1] FALSE
    is.factor(attrition$EducationField);
    [1] FALSE
    ...

If the “is.factor” function indicates that a categorical variable is not recognized as a factor, it could impact the model analysis. Conversion to factor variables can be achieved with the following commands.

#### Data Transformation
    attrition$BusinessTravel = as.factor(attrition$BusinessTravel);
    attrition$Department = as.factor(attrition$Department);
    attrition$Education = as.factor(attrition$Education);
    attrition$EducationField = as.factor(attrition$EducationField);
    ...

With the data format and types prepared for logistic regression analysis, we will now examine the correlation matrix for all variables and compare their correlations. Our primary focus in this study is on employee attrition and its potential correlations with the 33 variables in the dataset. Therefore, we will specifically analyze the attrition-related correlations. The following commands in R generate the correlation matrix. Note that the cor function requires numeric values, and since the dataset includes many non-numeric values, an index of numeric columns must be created beforehand.

#### Data Transformation
    numericAttrition <- sapply(attrition, is.numeric);
    y1 <- “Attrition”;
    x1 <- setdiff(names(attrition)[numericAttrition], y1);
    cor(attrition[x1], attrition[[y1]]);

![Correlation Coefficients of Attrition versus Employee Demographics](https://github.com/dntjdgur/ml-r-employee-attrition/blob/main/images/correlation.png)

As the final step of this CRISP-DM phase, we will divide the data into two separate sets: training and testing. During this process, we will exclude the Over18 column. The training set will comprise 70% of the entire dataset, while the testing set will consist of the remaining 30%. The following command will accomplish this.

#### Data Loader
    set.seed(100);
    sampleData = sample(c(TRUE, FALSE), nrow(attrition), replace=TRUE, prob=c(0.7, 0.3));
    trainingData = attrition[sampleData, ];
    testingData = attrition[!sampleData, ];

The following codes verify the dimensions of each training and testing datasets.

#### Data Dimension Check
    dim(trainingData)
    [1] 869 36
    dim(testingData)
    [1] 401 36
    trainingData = subset(trainingData, select = -c(Over18));
    testingData = subset(testingData, select = -c(Over18));

### Modeling

Logistic regression will be employed for the analysis, as it is well-suited for binary response variables such as employee attrition (Yes/No). In this model, attrition will be used as the dependent variable, with other prepared variables serving as independent variables.

Initially, the training data will be used to fit the logistic regression model, refining it by excluding insignificant variables. The model's accuracy will be assessed using goodness-of-fit measures like the likelihood ratio test. According to research, a p-value less than 0.05 for the overall model fit statistic indicates a good fit, allowing us to reject the null hypothesis (Evaluating Logistic Regression Models, 2015). The goodness of fit will be determined by comparing the refined model to the initial model with all variables included.

In this phase, we will build a logistic regression model, determine which variables should be included in the analysis, and evaluate the model's performance in predicting employee attrition outcomes. Having cleaned the data in previous steps, we will construct a correlation matrix to examine correlation values and the variance inflation factor (VIF). A correlation value exceeding 0.9 suggests multicollinearity. To facilitate comparison, we will use a heatmap instead of numerical indicators.

#### Dependencies
    library(corrplot); ## Package for Correlation Plots
    library(car); ## Package for VIF Testing
    corrplot(cor(df), type=’upper’, order=’hclust’, tl.col=’black’, tl.srt=45); ## Correlation Coefficient Matrix Heatmap

#### Model
    Model <- glm(formula=Attrition~Age + BusinessTravel + Department + DistanceFromHome + Education + EducationField + EnvironmentSatisfaction + Gender + JobInvolvement + JobLevel + JobRole + JobSatisfaction + MonthlyIncome + NumCompaniesWorked + OverTime + PerformanceRating + RelationshipSatisfaction + StockOptionLevel + TotalWorkingYears + WorkLifeBalance + YearsAtCompany + YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager, data=df_tuned, family='binomial'); ## 1st Model Training
    Model <- glm(formula=Attrition~Age + BusinessTravel + Department + DistanceFromHome + Education + EducationField + EnvironmentSatisfaction + Gender + JobInvolvement + JobRole + JobSatisfaction + NumCompaniesWorked + OverTime + PerformanceRating + RelationshipSatisfaction + StockOptionLevel + TotalWorkingYears + WorkLifeBalance + YearsAtCompany + YearsSinceLastPromotion + YearsWithCurrManager, data=df_tuned2, family='binomial'); ## Second Model with multicollinearity removed.

### Model VIF Test
    testingData = subset(testingData, select = -c(Over18));
    vif_values <- vif(model); ## Assign values of VIF for Bar Plot
    barplot(vif_values, main='VIF Values', horiz=FALSE, col='orange');
    abline(h = 5, lwd = 3, lty = 2); ## Bar Plot and Horizontal Line

Our correlation coefficient heatmap indicates that marital status shows multicollinearity with the stock option level variable, with a coefficient above 0.9. By removing marital status from the dataset, we can eliminate preliminary multicollinearity. The new heatmap shows no multicollinearity, which is a positive sign before proceeding with the analysis.

![Heatmap without multticollinearity](https://github.com/dntjdgur/ml-r-employee-attrition/blob/main/images/VIF%20-%20multicollinearity.png)

We will further validate the removal of multicollinearity through VIF testing. The initial VIF test confirms our correlation coefficient verification, except for variables like job level, monthly income, and years in the current role. A horizontal line on the VIF plot illustrates that a value between 1 and 5 is generally considered moderately correlated (Belsley, 1991). Variables exceeding a VIF value of 5, indicating possible multicollinearity, will be excluded from the model. A secondary VIF bar plot shows that none of the variables now exceed a scale level of 5, indicating that all potential multicollinearity has been successfully removed. Consequently, the data quality is now sufficient to demonstrate variable significance and goodness of fit.

![VIF - Without multicollinearity](https://github.com/dntjdgur/ml-r-employee-attrition/blob/main/images/VIF.png)

![VIF - Bar Chart without multitcollinearity](https://github.com/dntjdgur/ml-r-employee-attrition/blob/main/images/VIF%20-%20Bar.png)

### Evaluation
In the evaluation phase, the model's output will identify the factors most contributing to employee attrition, providing actionable insights for business strategy development. The predictive accuracy will be assessed by comparing the results from the training and testing models.

The predictions will be validated using the ROC curve, which provides the AUC (Area Under the Curve) value. An AUC between 0.7 and 0.8 is considered acceptable, with higher values indicating better predictive accuracy (David W., 2013, p.177).

To perform a goodness-of-fit test, we will illustrate a ROC curve and interpret the AUC value. Using the final tuned model data, the ROC curve (shown in the appendix) yields an AUC of 0.8233363, indicating good predictive performance. This means the model can accurately predict outcomes about 8 times out of 10. Using this model, we established a prediction value table, illustrating true positive and negative predictions. The model predicted a total of 783 true positive and negative cases out of 898 training data records, indicating an 87% accuracy rate. This promising result suggests the model is reliable for predicting employee attrition outcomes.

#### Dependencies
    library(pROC); ## Package to draw ROC Curve

#### Model Evaluations
    test_probability = predict(Model, newdata=df, type='response');
    test_ROC = roc(df_tuned3$Attrition ~ test_probability, plot=TRUE, print.auc=TRUE); ## Build ROC Curve
    summary(Model); ## Summary Statistic
    as.numeric(test_ROC$auc); ## Obtain AUC Value

![Model ROC Curve](https://github.com/dntjdgur/ml-r-employee-attrition/blob/main/images/roc.png)

The summary statistics reveal which variables are significant and which are not. The results indicate that department, distance from home, environment satisfaction, gender, job involvement, job satisfaction, number of companies worked for, overtime, relationship satisfaction, stock option level, total working years, work-life balance, years since last promotion, and years with the current manager are significant. The insignificant variables will be eliminated, and the secondary summary statistics now show that all remaining variables are significant.

![Fine-tuned model summary statistics](https://github.com/dntjdgur/ml-r-employee-attrition/blob/main/images/summary%20statistics%20-%20fine%20tuned.png)

#### Model Prediction
    prediction = ifelse(test = trainingModel$fitted.values > 0.5, yes = 1, no = 0); ## Prediction values
    table(trainingModel$y, prediction); ## Build a table for True Positive and Negative Case values.

    prediction
        0    1
    0 733   17
    1  98   50 
    
However, there are concerns about the data's accuracy. Although provided by GE's HR department, there could be false responses in rating variables. If the survey was not conducted in a strictly confidential environment, employees might not feel comfortable being honest, especially when HR is overseeing the research. Fear of potential negative consequences might lead employees to submit inaccurate responses. If this is the case, the model's predictions may mislead the analysis.

### Deployment
Before deploying the model, it is crucial to ensure the data format is compatible with the production environment. The deployment phase should be meticulously documented and planned to facilitate future improvements and iterations of the model.

Deploying the model requires thorough evaluation and interpretation of results from a business perspective. The final report should include clear visualizations to help executives easily interpret the findings. Visualizations must be well-labeled, and the report should be logically structured to enhance persuasiveness. The conclusion should summarize the key results and confirm that the primary research objectives have been met.

## References
Changing values when converting column type to numeric. (2011, June 13). [Stack Overflow.](https://stackoverflow.com/questions/6328771/changing-values-when-converting-column-type-to-numeric)

Alice, M. (n.d.). [How to Perform a Logistic Regression in R | DataScience+.](https://datascienceplus.com/perform-logistic-regression-in-r/) 

Johnson, D. (2022, November 19). [Factor in R: Categorical Variable & Continuous Variables. Guru99. ](https://www.guru99.com/r-factor-categorical-continuous.html)

Bhalla, D. (n.d.). R : [Keep / Drop Columns from Data Frame. ListenData.](https://www.listendata.com/2015/06/r-keep-drop-columns-from-data-frame.html)

Correlation of one variable to all the other in R. (2017, August 26). [Stack Overflow.](https://stackoverflow.com/questions/45892274/correlation-of-one-variable-to-all-the-other-in-r)

Belsley, D. A. (1991). Conditioning Diagnostics: Collinearity and Weak Data in Regression (1st ed.). Wiley-Interscience.

[VIF function - RDocumentation. (n.d.).] (https://www.rdocumentation.org/packages/regclass/versions/1.6/topics/VIF)

Shrestha, N. (2020). [Detecting Multicollinearity in Regression Analysis. American Journal of Applied Mathematics and Statistics, 8(2), 39–42.] (https://doi.org/10.12691/ajams-8-2-1)
