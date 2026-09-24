## Predictive Modeling of NHANES Data

This notebook applies classification and regression models to public health survey data from the CDC's [National Health and Nutrition Examination Survey (NHANES) 2017–2018](https://wwwn.cdc.gov/nchs/nhanes/continuousnhanes/default.aspx?BeginYear=2017). It asks two questions:
1. Can demographic and activity data predict whether someone has been **diagnosed with diabetes**?
2. How well do these factors explain **BMI**?

### Data
Four NHANES 2017–2018 files are downloaded directly from the CDC in SAS transport (.XPT) format and merged on the participant ID (`SEQN`):

| File | Contents | Variables used |
|---|---|---|
| `DEMO_J` | Demographics | Age (`RIDAGEYR`), Gender (`RIAGENDR`), Education (`DMDEDUC2`) |
| `PAQ_J` | Physical activity | Vigorous recreational activity minutes (`PAD660`), Sedentary minutes per day (`PAD680`) |
| `BMX_J` | Body measures | BMI (`BMXBMI`) |
| `DIQ_J` | Diabetes | Doctor-diagnosed diabetes (`DIQ010`) |

Rows with missing values are dropped. The diabetes variable is limited to Yes/No answers ("borderline" and "don't know" are removed) and recoded as 1 = diabetes, 0 = no diabetes.

### Workflow
1. Download, load, and merge the NHANES files
2. Select and rename variables, then handle missing values
3. **Classification:** logistic regression predicting diabetes diagnosis (80/20 train-test split)
4. **Regression:** ordinary least squares (OLS) linear regression predicting BMI (statsmodels), with a variance inflation factor (VIF) check for multicollinearity
5. **Comparison:** both models are fitted twice, first with *physical activity* and then with *sedentary time* as the activity predictor

### Results

**Classification: diabetes diagnosis (logistic regression)**

| Activity predictor | n | Accuracy | Diabetes cases caught (recall) |
|---|---|---|---|
| Physical activity | 1,174 | 92.8% | 1 of 18 (0.06) |
| Sedentary time | 5,001 | 82.5% | 12 of 173 (0.07) |

The high accuracy is misleading. Very few participants have diabetes (7–16%), so both models mostly predict "no diabetes" and miss nearly every true case.

**Regression: BMI (OLS)**

| Activity predictor | R² | Significant predictors (p < 0.05) |
|---|---|---|
| Physical activity | 0.027 | Diabetes diagnosis (+3.86 BMI) |
| Sedentary time | 0.032 | Diabetes diagnosis (+3.45), gender, age |

All VIFs are below 2, so multicollinearity is not a concern. Both models are statistically significant but explain only about 3% of the variation in BMI. Neither physical activity nor sedentary time was a significant predictor.

### Possible improvements
- Add clinical predictors such as waist circumference, fasting glucose, HbA1c, blood pressure and dietary intake
- Address class imbalance with class weighting or oversampling to improve recall
- Try interaction terms and non-linear models such as random forests

### Tech stack
Python · pandas · scikit-learn · statsmodels · patsy · Google Colab

### How to run
Open the notebook in Google Colab or any Jupyter Notebook program using the badge at the top. The data downloads automatically from the CDC website, so no local files are needed.

### Data source & citation
Centers for Disease Control and Prevention (CDC), National Center for Health Statistics (NCHS). *National Health and Nutrition Examination Survey Data, 2017–2018.* Hyattsville, MD: U.S. Department of Health and Human Services. https://wwwn.cdc.gov/nchs/nhanes/continuousnhanes/default.aspx?BeginYear=2017
