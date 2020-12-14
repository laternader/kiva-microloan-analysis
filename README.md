# It's Kiva Time

##### Presented by James Salisbury, Sid Carter, and Nader Esmael

![Header](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Images/kiva-header.jpg)

## Table of Contents

README:
- [Problem Statement](#problem-statement)
- [Data](#data)
  - [Data Dictionary](#data-dictionary)
- [EDA](#eda)
- [Conclusions](#conclusions)
- [Next Steps](#next-steps)

Directories:
+ [Code](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/tree/master/Code)
  + [Data Cleaning Notebook](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Code/01_Data_Cleaning.ipynb)
  + [EDA Notebook](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Code/02_EDA.ipynb)
  + [Final Models](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/tree/master/Code/Final_Models)
    + Folder contains all notebooks that contains models that we considered as our final or best-result producing models
+ [Data](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/tree/master/Data)
  + `loans_cleaned.csv` - cleaned data
  + `loans.csv` - original data
+ [Images](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/tree/master/Images)
---

## Problem Statement

---
Microlending is a form of peer-to-peer financing that has seen continued expansion over the past couple of decades. The first modern microcredit institution was founded in 1983 with many others coming about since. Microloans are most commonly granted to impoverished borrowers who typically lack collateral. While microloans are mostly implemented in impoverished countries through field agents and local banks, some organizations have expanded and started offering lending services all over the world.

[Kiva.org](https://www.kiva.org/), an internet-based microlending platform, was founded in 2005 in San Francisco with a mission to expand financial access to help underserved communities thrive. Since Kiva's inception, they have processed almost 2 million loans via their platform in over 75 countries. Similar to other microcredit organizations, Kiva promotes a 95.8% repayment rate and over 1.9 million lenders.

While most of the loans processed through Kiva's site are $500 or less, there are many factors which may affect the time to funding besides just the loan amount. Kiva has reached out to us to help them build a predictive model which will ultimately be implemented as a tool for borrowers. We have been tasked to build a predictor using a variety of input factors (such as loan amount, country, activity/sector, borrower's gender, etc.) that will help borrowers estimate the total time required to fund a loan.

## Data

---
*Python & Jupyter Notebooks was used in dealing with the data. The data was a snapshot extracted from the Kiva Developer page [source](https://www.kiva.org/build/data-snapshots). It was provided as a csv file.*

The data was then cleaned by making the column names lower-cased and by selecting 13 out of the 34 columns given. These are provided in the data dictionary below. We then dropped any rows with nulls in the `raised_time` and `lender_term` column and filtered out loans that were NOT funded.

We calculated `time_to_fund` and `hours_to_fund` by converting `posted_time` and `raised_time` to proper date times and subtracting the difference. We were able to extract `loan_year` and `day_posted`. We also filtered out any `hours_to_fund` that were less than 0. We assumed these loans were still in the process of being funded or were unsuccessful.

We made `image_incl` and `video_incl` binary columns. Converted `borrower_genders` to either fit as F for one female, M for one male, or G for group of people. Finally, we only looked at countries that had, historically, requested more than 5 loans.

### Data Dictionary

|Feature|Type|Dataset|Description|
|---|---|---|---|
|**Loan ID**|*integer*|Kiva Snapshot|Unique ID for each loan|
|**Status**|*string*|Kiva Snapshot|Loan status indicating whether it has been funded|
|**Posted Time**|*datetime*|Kiva Snapshot|The date/time the loan was posted on ([kiva.org](https://www.kiva.org/build/data-snapshots))|
|**Raised Time**|*datetime*|Kiva Snapshot|The date/time the loan was fully funded|
|**Loan Amount**|*float*|Kiva Snapshot|Total amount of loan, in USD|
|**Activity Name**|*string*|Kiva Snapshot|Name of the activity the loan is for|
|**Sector Name**|*string*|Kiva Snapshot|Name of the sector the loan falls under|
|**Country Name**|*string*|Kiva Snapshot|Country that the borrower lives and will use the loan|
|**Lender Term**|*float*|Kiva Snapshot|Number of installments for loan repayment; (monthly repayments are actually 2 less than the lender term)|
|**Repayment Interval**|*string*|Kiva Snapshot|The frequency that the loan repayments are made|
|**Borrower Genders**|*string*|Kiva Snapshot|Gender of the borrower; any loans with more than one individual have been labeled as Group|
|**Time to Fund**|*timedelta*|Calculated|Total time to fund; calculated from subtracting Posted Time from Raised Time|
|**Hours to Fund**|*integer*|Calculated|Time to fund in hours, rounded up|
|**Loan Year**|*integer*|Calculated|Year the loan was posted|
|**Day Posted**|*string*|Calculated|Day of the week the loan was posted, in UTC/GMT|
|**Image Included**|*integer*|Calculated|Whether or not an image was included in the loan request|
|**Video Included**|*integer*|Calculated|Whether or not a video was included in the loan request|

## EDA

---
After preprocessing the data, we wanted to find any trends in the data that could play a role in how quickly or how slow a microloan gets funded. Thanks to really good cleaning, there were no nulls in our preprocessed data. Our target variable was `hours_to_fund`.

We grouped the data by country and loan year to calculate aggregates (sum, mean, count, min, max) for `hours_to_fund` and `loan_amounts`. Once we did that, we noticed that the Philippines was one of the most successful in total number of microloans getting funded (392018). However, that was partly due to them having one of the lowest averages in loan amount (around $334). We think that `loan_amount` might have been a crucial factor in the speed/time at which a microloan was funded. The country might not be an important factor however, the Philippines was doing something right when it came to asking for money and paying it back.

![Philippines is at the top](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Images/country_name_top10s.png)

![Low Loan asking point](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Images/top10_lowest_loanamt_avgs.png)

After taking that trend into account, we also noticed that females had over 40% more success rate in getting funding for a microloan. The same trend was seen in `repayment_interval`.

![females for the win](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Images/Inkedborrower_genders_top10s_LI.jpg)

![repayment](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Images/Inkedrepayment_interval_top10s_LI.jpg)

Since our target variable was `hours_to_fund`, we plotted the distribution. It was a positive skew so majority of loans were being funded under the average amount of hours (around 40 days).

![hrs 2 fund eda](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Images/hrs_2_fund_eda.png)

The skew also matched the `loan_amounts`. Historically, most loans successfully funded were under the average (less than $800).

![loan amt eda](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Images/loan_amt_eda.png)

As we delved deeper into the EDA, we took into account these trends as we began to run our models. It helped eliminate any outliers as well as maintain distributions. One thing that was concerning was the numerical data having little to no correlation with hours to fund. We assumed that at this point that maybe the categorical columns played a larger role, possibly a larger role than `loan_amount`! We feared that `hours_to_fund` may not be a good target variable. It all depended on our models.

![heatmap hrs to fund](https://git.generalassemb.ly/laternader/timmy-needs-a-loan/blob/master/Images/heatmap_hrs_2_fund.png)

## Conclusions

---
|Model|RMSE|R2|
|---|---|---|
|**OLS Linear Regression**|298|0.163|
|**GLM Poisson**|277|N/A|
|**Decision Tree**|280|0.344|
|**Bagging Decision Tree**|282|0.343|
|**Random Forest**|283|0.353|

After running the various parametric and non-parametric models and evaluating their metrics, we came to the conclusion that with the models we tested  our problem statement was not able to be accomplished in a satisfactory manner. The highest R2 score we were able to achieve was 35.3% with our Random Forest model. As suspected, the non-parametric models performed better as a result of the lack of correlation between numeric features. However, even the non-parametric models did not perform up to standards.

We suspect this is a result of the nature of lending. Even though we limited the scope for our modeling to loans less than or equal to $1,000 (as the max loans took much longer to fund than the standard sized loans) there was still a good amount of variance in determining the time to fund that was unable to be correlated and factored in using the features we had. Even though we suspect most of the lenders have no direct connection with the borrowers, there may be other factors at play like marketability of the borrower's post, local efforts to promote/raise funds through the field agents, or even lender influenced factors.

With the current models as they stand, we have concluded that there needs to be significant improvement before any of the models would be able to be implemented in a production environment. This leads to next steps of other efforts to consider as a way to improve the model.


## Next Steps

---
None of the estimation models possessed compelling explanatory power making them useful for advising a prospective borrower or providing an internal estimate about when funding might take place.

Some next steps for consideration:
+ Development of a non-parametric neural network model. The database comprising 1.8 million loans should be sufficient to support exploration of a neural network.
+ Interview Kiva staff to learn whether their lending process is essentially an anonymized web dependent process or whether personal involvement by banking or field staff or ancillary participants influence the funding process.
