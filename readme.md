# Prosper Loan Data exploration
## by AM Watson


## Dataset

The file contains data on 113,937 loans (rows) with 81 variables (columns). Variables include loan amount, interest rate, information about borrower's financial history, and current loan status.

The data was last updated in 2014. I decided to examine only the loans made from 2005 through 2008 to make sure that I was only examining loans that should, according to their original terms, have been completed by 2014. (The loans originating from 2005 through 2008 were at most 3-year loans.) In the date range I examined, there were 29,056 loans. 

Unbeknownst to me, by selecting loans from 2005 through 2008, I was restricting my research to the era before the SEC found Prosper in violation of SEC laws, forcing Prosper to drastically change its business practices, which resulted in Prosper greatly reducing their default rates. Hence, I only looked at the data during the era when Prosper's loans had a quite high rate of defaults.

Prosper Loans is not a normal lender. They are the nation's first peer-to-peer lending marketplace, meaning they facilitate and to some degree officiate between would-be lenders and would-be borrowers. From 2006 through 2009, Prosper allowed the lenders and borrowers to set their own rates. After the SEC crackdown, Prosper adopted a new model and now an algorithm decides on the rate and lenders and borrowers just have to decide whether or not to accept that rate.

The data and data dictionary came from Udacity. 


## Summary of Findings - Methods and graphs

My goal was to find variables that could reasonably predict how successful a loan would be. In order to keep the project to a reasonable size, I decided to focus only on characteristics of the borrowers for my possible predictor variables (ie: I would use variables like a borrower's monthly income, but not variables like a loan's interest rate).

I created a new variable called NewLoanStatus (NLS for short in discussions) that divided loans into those that had been completed successfully, and those that had not been completed successfully (that had either defaulted or been written off; the five cancelled loans in this data set I didn't count as either Successful or Unsuccessful).

Soon I decided that this variable was not adequately detail-rich because, for example, it failed to discriminate between loans that had defaulted after paying back all of the principle and some of the interest and loans that had defaulted after paying back nothing. So I created another variable called ROI that measured the return on income (by subtracting the total spent by Prosper from the total made by Prosper on each loan).

I compared the two possible metrics for measuring the loans' success (NLS and ROI) against possible predictor variables: IncomeRange, StatedMonthlyIncome, CreditGrade, CreditScoreRangeUpper (and Lower),AmountDelinquent, and DebtToIncomeRatio. 

I began with graphs and ended by running spearman and pearson tests on loans_to_2008 (the data frame with loans originating from 2005 through 2008), and also on typical_incomes (a subset of loans_to_2008 that limited the StatedMonthlyIncome to $300 through $30,0000), and finally on typ_succ and typ_unsucc (subsets of typical_incomes, breaking the data into Successful Loans (according to the standards of my NewLoanStatus metric). The spearman and pearson tests I ran on ROI versus the StatedMonthlyIncome (SMI) and CreditScoreRangeUpper (CSRU) variables. I chose ROI as my main metric because it contained more information than NewLoanStatus. I chose StatedMonthlyIncome and CreditScoreRangeUpper as my main metrics because they were more correlated to ROI and SMI than either AmountDelinquent or DebtToIncomeRatio, and because, being numeric variables, they were easier to work with than CreditGrade or IncomeRange. 

For my final graph, I narrowed the loans_to_2008 dataframe down to typical_incomes (throwing out the very most extreme values) and broke the data into two graphs side-by-side: Succesful Loans and Unsuccessful loans. I then graphed ROI versus StatedMonthlyIncome (with a log transformation on SMI because of the wide range of values), and added color shading for CSRU. The Successful Loans graph looked like the top of a mushroom floating from ROI 0 up to about ROI .75. The Unsuccessful Loans graph looked like a sharpened spike that went from ROI -1 all the way up to the same height as the top of the mushroom (about ROI .75). The shading gets a little darker (indicating higher credit ratings) towards the right on both graphs (higher incomes). The shading is most apparent on the very bottom of the Successful Loans graph, and in a band at about ROI 10% on that graph. In general, the Successful Loans graph shows more shading on the bottom area of the graph (lower ROIs) and towards the right side of the graph (higher incomes). I'm not sure what the darkening around the bottom of the graph signifies. It is perhaps possible that this darkening has more to do with a concentration of points than with an increase in credit scores. It is hard to see a corresponding trend in the Unsuccessful loans graph, although I can perhaps discern a slight darkening as the eye moves upward (towards higher ROIs) on that graph (as well as to the right (higher incomes)). 

There is a very close but not completely perfect overlap between Positive ROIs and Successful Loans. Almost all the Negative ROIs are Unsuccessful Loans, but (as mentioned earlier), for some reason Unsuccessful Loans occur at just about every ROI (even very high ones). 

Why do both the mushroom-top of the Succesful Loans and the pike-tip of the Unsuccessful Loans reach their zeniths of about ROI .75 at about StatedMontlyIncome $6000? Is this the income range that paid the highest interest rate and that also paid off their loans? But surely there were people at lower income levels that also paid off their loans. Maybe it has to do with the amount of money people at this income level are allowed to borrow, paired with the interest rates they are required to pay?

Why do the Succesful Loans represent many more income ranges (both higher and lower) than the Unsuccessful Loans? Zooming in on the loans_to_2008 version of ROI versus StatedMonthlyIncome (with CSRU color-coded), we see that there are a wider variety of values in the negative ROI portion of the graph. But it remains true that on the whole, the positive loans come from a wider variety of incomes than the negative values. I don't know why.

Why do Unsuccessful Loans represent pretty much all the same (positive) ROIs as Successful Loans (along with a majority of negative ROIs)? I guess because some people get very close to paying off their loan before finally defaulting or getting written off. Especially since these loans were all supposed to be completed by 2011 or (often much) earlier. Some borrowers probably kept paying a little bit for a long time (perhaps past the normal time limit for the loan) before finally quitting on the obligation. 

As CreditScoreRangeUpper (which I selected for no particular reason over the functionally equivalent CreditScoreRangeLower) is the best predictor variable I found for ROI (see below), it is perhaps worthwhile to here describe the graph of ROI versus CreditScoreRange. No transformations were necessary. The points don't appear until about CSRU 450. There's a wall of negative ROI values (ROI 0 down to ROI -1) from those left-side beginnings through to about CSRU 850, with a couple small bites (where some of the negative values aren't represented by CSRU scores) on either end of that wall of ROI 0 to ROI -100%. On top of the wall of negative ROIs, the positive ROIs are a roof that slopes up from the left side (around CSRU 450) up to a height of about ROI 80% at about CSRU 600; the roof then slopes gradually down until about ROI 10% at around CSRU 900. The roof overhangs the rectangle wall of ROI 0 down to ROI -100% on the right side (recall that the wall ends at about CSRU 850).

What does this picture of a wall of negative ROIs across pretty much all CSRUs with a gently sloping roof of positive ROIs that reaches its peak at about CSRU 600 tell us? It seems that CSRU is the credit range with the highest possible payoff. Probably related to the relatively high rates that people with that (not spectacularly good) credit rating are charged. Perhaps these borrowers are also allowed to borrow a little more money than people at lower credit ranges? (otherwise wouldn't there be the occassional borrower with CSRU 500 paying off a loan of the same size as a borrower with CSRU 600, and wouldn't that borrower end up paying as much or more than borrowers with CSRU 600?)

## Key Findings from the correlation tests

CreditScoreRangeLower (CSRL) seemed functionally equivalent to CSRU (I ran correlation tests against them and ROI and NLS and got identical scores). 

The final correlation tests I ran included p-values. 
There were statistically significant correlations in almost every permutation, but the only correlations greater than |.1| are as follows:

loans_to_2008 versus ROI: ROI/NLS .72 (spearman) | ROI/CSRU .12 (pearson) | 
loans_to_2008 verus NLS: NLS/CG .20 (spearman) | NLS/CSRU .25 (spearman) | NLS/AM -.15 (spearman) 
loans_to_2008 versus CSRU: SMI/CSRU .20 (pearson) and .24 (spearman) | CG/CSRU .99 (spearman)
(Note: I didn't find p-values for these, although I did for the below.)

typical_incomes versus ROI: ROI/CSRU .14 (p) 
typical_incomes SMI/CSRU .26 (p) & .27 (s)

typ_succ versus ROI: ROI/SMI -.14 p & -.12 s | ROI/CSRU -.56 p & -.57 s | ROI/DTIR .08 p & .26 s | ROI/AD .11 p & .27 s
typ_succ SMI/CSRU .25 p & .26 s

typ_unsucc versus ROI: ROI/SMI .03 p & .07 s | ROI/CSRU .07 p & .1 s 
typ_unsucc SMI/CSRU .25 p & .34 s

As you can see, the best predictor variable for ROI was CSRU, and this improved a little when we threw the outliers out of StatedMonthlyIncome (from a .12 spearman to a .14). The correspondence between ROI and CSRU went up to the very significant -.56 when we focused just on the Successful Loans subset. In practice, of course, you can't say ahead of time whether or not a loan will be Successful. And the fact that the relationship has flipped from a positive to a negative one shows that the fundamental nature of the relationship between the two variables changes when we zoom in on only the Successful Loans.

CSRU was a pretty good predictor of NewLoanSatus. (loans_to_2008: NLS/CSRU .25 (spearman)) 
Unfortunately, I didn't create the numeric version of NLS (which is needed to properly run a correlation test on ordinal variables), and so in the name of time, I've decided to not run any tests on NewLoanStatus in the typical_incomes dataframe (because I'd have to redo the numericization and etc).

ROI and NLS were very good predictors of each other (.72 spearman score in loans_to_2008).

CSRU and SMI were good predictors of each other. (.20 pearson, .24 spearman in loans_to_2008; .26 pearson, .27 spearman in typical_incomes). They actually became worse predictors of each other when we focus on the successful loans, and they also go from being positively correlated to being negatively correlated.

CreditGrade and CreditScoreRangeUpper were incredibly good predictors of each other (.99 spearman). I imagine the one is created out of the other, so a close association is not surprising, but .99 is a really really close association.

The correlation between ROI and DebtToIncomeRatio was only above |.1| in the Successful loans subset. Ditto for ROI and AmountDelinquent. 

## The counts within my two primary metrics

Within loans_to_2008, 63% of the loans were Successful. There were only 5 loans that were neither Successful nor Unsuccessful (5 Cancelled loans) in this dataset, so we can say that there were about 63% Succesful and 47% Unsuccessful Loans.

Within loans_to_2008, the most frequent ROIs were 10%, 20% and 30% (each had counts between 5000 and 6000); the counts dropped off quite about at ROI 40% (down to about 3000); ROI 50% represented about half as many loans as ROI 40%; and ROIs 60%, 70% and 80% stepped down and down (there were very few loans with ROIs at 80%). On the negative side, the ROIs started at a count of about 500 (for ROI -10%), and then increased gradually until the last few negative ROIs (-80%, -90%, -100%) each represented about 1000 loans.


## Key Insights for Presentation

For the presentation, I focused on my two metrics for measuring the success or failure of a loan (NewLoanStatus and ROI) -- their frequency distributions, how they related to each other, and how they related to the two best predictor variables -- StatedMonthlyIncome and CreditScoreRangeUpper. The graphs I selected were NLS and ROI frequency distributions; NLS versus CSRU; ROI versus SMI; ROI versus CSRU; and ROI versus SMI broken with CSRU color coding, taken from the typical_incomes data subset and broken into a Successful Loans and an Unsuccessful Loans graph. I augmented the graphs with data from the mathematical correlation tests. The key findings highlighted: the best predictor value I found was CSRU, and it was a better predictor of NLS than of ROI; SMI was the second best predictor, and it was a much better predictor of NLS than ROI. The highest correlations between ROI and CSRU were found in the typical_incomes data frame in the Successful Loans subset.

Two metrics for measuring a loan's success
Distribution of NLS
Distribution of ROI
Correlation Scores of Predictor Variables
Graphs of the most successful predictor variable versus ROI and NLS
The impact of refining the dataset on correlation scores
The impact of breaking the dataset into Successful and Unsuccessful subsets - Graphical
The impact of breaking the dataset into Successful and Unsuccessful subsets - Mathematical




loans_to_2008 versus ROI: ROI/NLS .72 (spearman) | ROI/CSRU .12 (pearson) | 
loans_to_2008 verus NLS: NLS/CG .20 (spearman) | NLS/CSGU .25 (spearman) | NLS/AM -.15 (spearman) 
loans_to_2008 versus CSRU: SMI/CSRU .20 (pearson) and .24 (spearman) | CG/CSRU .99 (spearman)
(Note: I didn't find p-values for these, although I did for the below)

typical_incomes versus ROI: ROI/SMI .02 p * | ROI/CSRU .14 (p) & -.04 (s) | ROI/DTIR -.02 (p) & .03 (s) ROI/AD -.042 (p) *
typical_incomes SMI/CSRU .26 (p) & .27 (s)
typical_incomes ROI/NLS -.04 (s)

typ_succ versus ROI: ROI/SMI -.14 p & -.12 s | ROI/CSRU -.56 p & -.57 s | ROI/DTIR .08 p & .26 s | ROI/AD .11 p & .27 s
typ_succ SMI/CSRU .25 p & .26 s

typ_unsucc versus ROI: ROI/SMI .03 p & .07 s | ROI/CSRU .07 p & .1 s | ROI/DTIR * .045 s | ROI/AD -.06 p & -.04 s
typ_unsucc SMI/CSRU .25 p & .34 s

(A * means that the correlation was statistically insignifiant (a p-value greater than .05)