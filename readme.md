# Prosper Loan Data exploration
## by AM Watson


## Dataset

The file contains data on 113,937 loans (rows) with 81 variables (columns). Variables include loan amount, interest rate, information about borrower's financial history, and current loan status.

The data was last updated in 2014. I decided to examine only the loans made from 2005 through 2008 to make sure that I was only examining loans that should, according to their original terms, have been completed by 2014. (The loans originating from 2005 through 2008 were at most 3-year loans.) In the date range I examined, there were 29,056 loans. 

Unbeknownst to me, by selecting loans from 2005 through 2008, I was restricting my research to the era before the SEC found Prosper in violation of SEC laws, forcing Prosper to drastically change its business practices, which resulted in Prosper greatly reducing their default rates. Hence, I only looked at the data during the era when Prosper's loans had a quite high rate of defaults.

Prosper Loans is not a normal lender. They are the nation's first peer-to-peer lending marketplace, meaning they facilitate and to some degree officiate between would-be lenders and would-be borrowers. From 2006 through 2009, Prosper allowed the lenders and borrowers to set their own rates. After the SEC crackdown, Prosper adopted a new model and now an algorithm decides on the rate and lenders and borrowers just have to decide whether or not to accept that rate.

The data is here: https://www.google.com/url?q=https://s3.amazonaws.com/udacity-hosted-downloads/ud651/prosperLoanData.csv&sa=D&ust=1581581520570000

## Summary of Findings - Methods and graphs

My goal was to find variables that could reasonably predict how successful a loan would be. In order to keep the project to a reasonable size, I decided to focus only on characteristics of the borrower (ie: I would use variables like a borrower's monthly income, but not variables like a loan's interest rate).

I created a variable called NewLoanStatus (NLS for short in discussions) that divided loans into those that had been completed successfully, and those that had not been completed successfully (that had either defaulted or been written off; the five cancelled loans in this data set I didn't count as either Successful or Unsuccessful).

Soon I decided that this variable was not adequately detail-rich. For example, it fails to discriminate between loans that had defaulted after paying back all of the principle and some of the interest and loans that had defaulted after paying back nothing. 

So I created another variable called ROI that measures the return on income (by subtracting the total spent by Prosper from the total made by Prosper on each loan).

I chose ROI as my main metric because it contained more information than NewLoanStatus. 

I compared the two possible metrics for measuring the loans' success (NLS and ROI) against possible predictor variables: IncomeRange, StatedMonthlyIncome (SMI for short), CreditGrade (CG for short), CreditScoreRangeUpper (and Lower) (CSRU), AmountDelinquent (AD), and DebtToIncomeRatio (DTIR). 

I used graphs and Spearman and Pearson correlation tests to examine how the primary metrics (NLS and ROI) interacted with the variable possible predictor variables (SMI, CSRU, etc). I investigated loans_to_2008 (the data frame with loans originating from 2005 through 2008), and also typical_incomes (a subset of loans_to_2008 that limited the StatedMonthlyIncome to $300 through $60,0000). 

CreditGrade and CreditScoreRangeUpper are closely related; as are IncomeRange and StatedMonthlyIncome. But CG and IR are categorical variables, while CSRU and SMI are numeric variables, which are easier to work with. So I focused more of my efforts on CSRU and SMI. 

As the project evolved, I became interested in trying to find a better correlation between SMI and ROI. Accordingly, for my final graph, I narrowed the loans_to_2008 dataframe down to typical_incomes (throwing out the very most extreme values) and broke the data into two dataframes -- Succesful Loans (typ_succ) and Unsuccessful Loans (typ_unsucc) -- to form two side-by-side graphs of ROI versus StatedMonthlyIncome. I placed a log transformation on SMI because of the wide range of values. Finally, I added color shading to show how CSRU related to ROI and SMI.  


## Key Findings from the correlation tests

Note: CreditScoreRangeLower (CSRL) seemed functionally equivalent to CSRU (I ran correlation tests against them and ROI and NLS and got identical scores). 

#### loans_to_2008 
ROI/CSRU .12 p & -.04 s (if I removed the CSRUs below 400: .11 p & -.05 s)
ROI/SMI .022 p & .003 s (if I restricted incomes to $300-$30000: .0024/-.004; $1K-$10K: .048/.0096)
ROI/DTIR -.034 p & -.024 s (I removed the outliers and got worse scores: -.022 p & -.029 s) 
ROI/AD -.042 p & .011 s (I removed the outliers and got comprable scores : -.042 & .017)

NLS/CG .20 s | NLS/CSRU .25 s | NLS/AM -.15 s
NLS/SMI .063 p and .046 s ($300-$30K: s down to .058 and p up to .068; both scores went down from this level when I tried $1K-$10K)

ROI/NLS .72 s
SMI/CSRU .20 (pearson) and .24 (spearman) 
CG/CSRU .99 (spearman)
(Note: I didn't find p-values for these, although I did for the below.)

#### typical_incomes 
ROI/CSRU .14 (p) 
SMI/CSRU .26 (p) & .27 (s)

#### typ_succ
ROI/SMI -.14 p & -.12 s | ROI/CSRU -.56 p & -.57 s | ROI/DTIR .08 p & .26 s | ROI/AD .11 p & .27 s
typ_succ SMI/CSRU .25 p & .26 s

typ_unsucc versus ROI: ROI/SMI .03 p & .07 s | ROI/CSRU .07 p & .1 s 
typ_unsucc SMI/CSRU .25 p & .34 s

As you can see, the best predictor variable for ROI was CSRU. The ROI/CSRU scoreimproved a little when we threw the most extreme outliers out of StatedMonthlyIncome (from a .12 spearman to a .14). The correspondence between ROI and CSRU went up to the very significant -.56 when we focused just on the Successful Loans subset within the typical_incomes dataframe. In practice, of course, you can't say ahead of time whether or not a loan will be Successful. And the fact that the relationship has flipped from a positive to a negative one shows that the fundamental nature of the relationship between the two variables changes when we zoom in on only the Successful Loans.

CSRU was a pretty good predictor of NewLoanSatus. (loans_to_2008: NLS/CSRU .25 (spearman)) 

None of the other predictor values received scores above |.05| even after removing outliers.

ROI and NLS were very good predictors of each other (.72 spearman score in loans_to_2008).

CSRU and SMI were good predictors of each other. (.20 pearson, .24 spearman in loans_to_2008; .26 pearson, .27 spearman in typical_incomes). They actually became worse predictors of each other when we focus on the successful loans, and they also go from being positively correlated to being negatively correlated.

CreditGrade and CreditScoreRangeUpper were incredibly good predictors of each other (.99 spearman). I imagine the one is created out of the other, so a close association is not surprising, but .99 is a really really close association.

The correlation between ROI and DebtToIncomeRatio was only above |.1| in the Successful loans subset. Ditto for ROI and AmountDelinquent. 

## The counts within my two primary metrics

Within loans_to_2008, 63% of the loans were Successful. There were only 5 loans that were neither Successful nor Unsuccessful (5 Cancelled loans) in this dataset, so we can say that there were about 63% Succesful and 47% Unsuccessful Loans.

Within loans_to_2008, the most frequent ROIs were 10%, 20% and 30% (each had counts between 5000 and 6000); the counts dropped off quite about at ROI 40% (down to about 3000); ROI 50% represented about half as many loans as ROI 40%; and ROIs 60%, 70% and 80% stepped down and down (there were very few loans with ROIs at 80%). On the negative side, the ROIs started at a count of about 500 (for ROI -10%), and then increased gradually until the last few negative ROIs (-80%, -90%, -100%) each represented about 1000 loans.


## Key Insights for Presentation

For the presentation, I focused on my two metrics for measuring the success or failure of a loan (NewLoanStatus and ROI) -- their frequency distributions, how they related to each other, and how they related to the the best predictor variable, CreditScoreRangeUpper. The graphs I selected were NLS and ROI frequency distributions, ROI versus CSRU (two graphs), and a final, rather complicated graph that I discuss below.

I augmented the graphs with data from the mathematical correlation tests. 

The highlighted correlation findings: the best predictor value I found was CSRU, and it was a better predictor of NLS than of ROI; SMI was the second best predictor, and it was a much better predictor of NLS than ROI. The highest correlations between ROI and CSRU were found in the typical_incomes data frame in the Successful Loans subset.

I concluded a graph of ROI versus with CSRU color coding, taken from the typical_incomes data subset and broken into a Successful Loans and an Unsuccessful Loans graph. This graph grew out of my desier to find a higher correlation between ROI and SMI. Splitting the Successful Loans and Unsuccessful Loans up gave me high correlation scores between ROI and SMI in the Successful Loans subset(-.14 Pearson and -.12 Spearman). The practical predictive value of this manuever is, of course, low. I can't know ahead of time whether or not a loan will be Successful. Also, as previously mentioned, the correlation went from being positive to negative.
