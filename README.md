# Employee Attrition Predictive Analysis using R

## Project Purpose
The objective of this project is to analyze an open-source dataset from General Electric (GE) concerning employee attrition, focusing on their demographics. The goal is to predict attrition rates based on employees' demographic details.

## Problem Statement
General Electric's HR reports indicate that many high-potential employees are leaving the company for other opportunities. Middle managers have corroborated these reports, noting an increase in job postings. To maintain its competitive edge, GE plans to use a predictive model developed during the pilot stage to identify employees likely to leave and implement measures to retain high-potential employees.

## Data Source
Data source can be achieved from [Here](https://https://github.com/dntjdgur/ml-r-employee-attrition/tree/main/data)

## Preliminary Analysis Using CRISP-DM
### Business Understanding
General Electric is confronting a critical challenge: the departure of numerous high-potential employees to other opportunities. The underlying causes of this attrition are not well understood, making it difficult for the company to develop effective strategies to reduce future turnover.

In the interim, productivity in the affected departments likely declines as remaining employees cover the responsibilities of their departing colleagues. This situation can adversely affect office morale, causing employees to question the reasons behind the attrition of high-performing individuals. Such uncertainty can create a negative atmosphere and potentially lead to further attrition.

It is essential for GE to identify the factors contributing to employee attrition and predict which employees are at risk of leaving. This analysis seeks to provide predictive insights into employee attrition while identifying key causes. The central research question is: "What factors are causing employees to leave the company, and can addressing these factors reduce the attrition rate?" Answering this question will help GE retain high-potential employees and develop strategies to attract more talent.

### Data Understanding & Preparation
The dataset includes 35 columns and 1,270 rows, detailing each employee's information. The 'attrition' column indicates whether an employee has left the company.

	dim(attrition);
    [1] 1270   35
    anyNA(attrition);
    [1] FALSE
    summary(attrition);

During the data preparation phase, variables such as employee count, employee number, and over-18 status will be excluded from the analysis, as they are constants and unlikely to influence the outcomes. The data will be cleaned by identifying and removing any null values.

Descriptive statistics will then be used to detect outliers, which could affect model accuracy. Outliers will be identified using R's summary() function and visually via histograms. These outliers will be removed to ensure model accuracy. Additionally, some data formats will be adjusted; for instance, 'attrition' and 'overtime' variables will be converted to Boolean values to facilitate logistic regression analysis. Categorical variables, such as education field, will be encoded numerically as per documented mappings.

The data will then be split into training and testing sets using a 70/30 partition, resulting in two datasets named trainingData and testingData.

### Modeling
Logistic regression will be employed for the analysis, as it is well-suited for binary response variables such as employee attrition (Yes/No). The model will use attrition as the dependent variable and the other prepared variables as independent variables.

Initially, the training data will be used to fit the logistic regression model, with the training process refining the model by excluding insignificant variables. The model's accuracy will be evaluated using goodness-of-fit measures such as the likelihood ratio test. According to research, if the p-value for the overall model fit statistic is less than 0.05, we reject the null hypothesis, indicating a good fit (Evaluating Logistic Regression Models, 2015). The goodness of fit will be assessed by comparing the refined model to the initial model with all variables included.

### Evaluation
In the evaluation phase, the model's output will identify the factors most contributing to employee attrition, providing actionable insights for business strategy development. The predictive accuracy will be assessed by comparing the results from the training and testing models.

The predictions will be validated using the ROC curve, which provides the AUC (Area Under the Curve) value. An AUC between 0.7 and 0.8 is considered acceptable, with higher values indicating better predictive accuracy (David W., 2013, p.177).

### Deployment
Before deploying the model, it is crucial to ensure the data format is compatible with the production environment. The deployment phase should be meticulously documented and planned to facilitate future improvements and iterations of the model.

Deploying the model requires thorough evaluation and interpretation of results from a business perspective. The final report should include clear visualizations to help executives easily interpret the findings. Visualizations must be well-labeled, and the report should be logically structured to enhance persuasiveness. The conclusion should summarize the key results and confirm that the primary research objectives have been met.

## References
[Changing values when converting column type to numeric. (2011, June 13). Stack Overflow.](https://stackoverflow.com/questions/6328771/changing-values-when-converting-column-type-to-numeric)

Alice, M. (n.d.). [[How to Perform a Logistic Regression in R | DataScience+.](https://datascienceplus.com/perform-logistic-regression-in-r/) 

Johnson, D. (2022, November 19). [Factor in R: Categorical Variable & Continuous Variables. Guru99. ](https://www.guru99.com/r-factor-categorical-continuous.html)

Bhalla, D. (n.d.). R : [Keep / Drop Columns from Data Frame. ListenData.](https://www.listendata.com/2015/06/r-keep-drop-columns-from-data-frame.html)

[Correlation of one variable to all the other in R. (2017, August 26). Stack Overflow.](https://stackoverflow.com/questions/45892274/correlation-of-one-variable-to-all-the-other-in-r)

