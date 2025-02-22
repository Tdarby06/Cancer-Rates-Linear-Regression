## Cancer Rates: A Linear Regression Project

This project intends to create a model that could help explain what factors might lead to cancer deaths in 3,047 counties across America. To do this, we will look at a dataset which contains data for counties in the primary 50 states in the United States. For every county, the dataset includes variables such as cancer diagnosis rates, population information, income, age, education levels, family sizes, marriage rates, insurance coverage, and employment rates. To do this we will look to out infatuation, and domain experience in the health field to find variables that can help explain the death rate.

This report will walk through the process that was taken to make an effective and explanatory model. To do this the dataset must first be analyzed. Then variables will be selected as predictors and used in an initial model. The model will be examined to determine what predictors should stay and what should be removed. The model will be checked to make sure it meets all assumptions and can be used to make statistical inferences. The model will be finalized and inferences will be conducted, assuming the model assumptions are met.

Overall the target variable `Target_deathRate` represents "Mean per capita (100,000) cancer mortalities"

![image](https://github.com/user-attachments/assets/c87162a5-1112-4bd2-931d-2126ac223511)

From the histogram we can see that `deathRates` is centered around 175 per 100k for each county. 

From here variables are selected in order to see if they have an effect on `deathRate`

**Selected Variable Analysis**
<details>
  <summary>Click to expand!</summary>

  - `avgAnnCount` - "Mean number of reported cases of cancer diagnosed annually"
      - ![image](https://github.com/user-attachments/assets/b67d8373-d82d-4d64-86f0-5c179acee832)
      - There does not appear to be a positive or negative relationship, the data looks to be centered around 200 for
the deathrate with values ± 100 on the y-axis.

  - `IncidenceRate` - "Mean per capita (100,000) cancer diagnoses"
      - ![image](https://github.com/user-attachments/assets/15f102a6-15bf-46d2-9ef1-42d89a765571)
      - There appears to be a strong positive relationship between IncidenceRate and Deathrate
          - We also see a few outliers. We will investigate those later. 
   
  - `MedianIncome` - "Median income per county"
      - ![image](https://github.com/user-attachments/assets/8f6d041b-7060-44fd-b2da-b4ff7ee32e74)
      - There looks to be a moderate negative relationship btween MedianIncome and Deathrate.
 
  - `MedianAge` - "Median age of county residents"
      - ![image](https://github.com/user-attachments/assets/bc8f8123-dbbf-4deb-97e0-f151fd9dc36e)
      - It is difficult to see any relationship between MedianAge and Deathrate with the values in Median Age, as
there are 20 or more that seem to be incorrect (median ages of individuals 350 years+)
      - ![image](https://github.com/user-attachments/assets/d607098b-82be-46a6-a64d-b2876051a59c)
      - With a better look at the data between MedianAge and Deathrate, there does not appear to be a relationship.
 
  - `studyPerCap` - "Per capita number of cancer-related clinical trials per county"
      - ![image](https://github.com/user-attachments/assets/d607098b-82be-46a6-a64d-b2876051a59c)
      - 
 
  - `PctHS25_Over` - "Percent of county residents ages 25 and over highest education attained: high school diploma"
  - `PctPrivateCoverage` - "Percent of county residents with private health coverage"
  - `PctPublicCoverage` - "Percent of county residents with private health coverage alone (no public assistance)"
</details>

**Simple Linear Model**

<details>
  <summary>Click to expand!</summary>

  Variables going in to model:
- avgAnnCount
- IncidenceRate
- MedianIncome
- MedianAge
- studyPerCap
- PctHS25_Over
- PctPrivateCoverage
- PctPublicCoverage

model1 summary
##
## Call:
## lm(formula = TARGET_deathRate ~ avgAnnCount + incidenceRate +
## medIncome + MedianAge + studyPerCap + PctHS25_Over + PctPrivateCoverage +
## PctPublicCoverage, data = df)
##
## Residuals:
## Min 1Q Median 3Q Max
## -117.082 -11.968 0.441 11.655 140.421
##
## Coefficients:
## Estimate Std. Error t value Pr(>|t|)
## (Intercept) 1.122e+02 6.283e+00 17.855 < 2e-16 ***
## avgAnnCount -8.341e-04 2.804e-04 -2.975 0.002957 **
## incidenceRate 2.346e-01 7.000e-03 33.515 < 2e-16 ***
## medIncome -1.991e-04 5.483e-05 -3.630 0.000288 ***
## MedianAge -7.115e-03 8.179e-03 -0.870 0.384405
## studyPerCap -7.880e-05 7.070e-04 -0.111 0.911259
## PctHS25_Over 9.206e-01 6.427e-02 14.325 < 2e-16 ***
## PctPrivateCoverage -8.782e-01 5.767e-02 -15.227 < 2e-16 ***
## PctPublicCoverage -1.106e-01 8.054e-02 -1.373 0.169935
## ---
## Signif. codes: 0'***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 20.38 on 3038 degrees of freedom
## Multiple R-squared: 0.4623, Adjusted R-squared: 0.4609
## F-statistic: 326.5 on 8 and 3038 DF, p-value: < 2.2e-16

</details>
