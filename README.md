java c
ST309 –   Exercise   6 
This   counts   for   10%   of   the   final   assessment   of   the   course.
The marks in   brackets reflect marks for each question.Please   submit   your   solutions   in   a   pdf ﬁle   to   Moodle   by   the   noon   (UK   time)   of Friday,   13   December   2024. Late submission entails   penalties:   5 marks (out of   maximum 100) will be deducted for every   half-day (12 hours)   within the ﬁrst 24 hours after the deadline, and 5 marks will be deducted for each further 24 hours.   Submissions   are not accepted after 5pm   on   Wednesday,   18 December 2024.
You   should only submit your   own   work,   and   cannot   use   materials   from the   past   and/or your   classmates.   Plagiarism is   a   very   serious   ofense   that   is   quite   easy   to   detect.    It   will   result   in   instant   failure   (mark   0).
This   exercise   is   on   credit   card   fraud   detection   based   on   a   data   set   downloaded   from   Kaggle Datasets at
https://www.kaggle.com/mlg-ulb/creditcardfraud 
Background information   on   the   data   is   available   at 
https://www.kaggle.com/mlg-ulb/creditcardfraud/home 
Previous   attempts   can   be   found   at https://www.kaggle.com/janiobachmann/credit-fraud-dealing-with-imbalanced-datasets/versions (All   those   analysis   was   done   using   Python.    But   you   should   be   able   to   follow   the   ideas,   understand   most   the   results.   Especially   some   initial   data   exploration   is   easy   to   follow.)The   dataset   contains   284,807   credit   card   transactions   in   two   days   in   September   2013   by   European   card-   holders,   of which   492   are   frauds.    So   the   data   is   highly   unbalanced:    the   positive   cases   (i.e.    frauds)   account   for   merely   0.172%   of all   transactions.Due   to   the   conﬁdentiality   issues, the   original   features   for   each transaction   are   masked   via   a   linear   transfor-   mation.      The   28   transformed   features   are   presented   as   V1, V2, · · · , V28.      According   to   the    above   webpage,   those 28 features are the principal   components   of the original   features.    No further   information   on those   features   is   provided.   In   addition   to   those   28   variables, there   are   3   untransformed variables:
•   Time:   number   of seconds   elapsed   between   each   transaction   and   the   ﬁrst   transaction   in   the   dataset
•   Amount:   the   amount   of the   transaction
•      Class:   binary   label   with   value   1   for   ‘fraud’   and   0   otherwise.
The   whole   dataset   has   284,807 rows   and   31   columns.    The   task   is   to   build   up   efective   algorithms   for   detecting   fraudulent   credit   card transactions.The      data   is      extremely      imbalanced   with      only   0.172%      ‘positives’    (i.e.       frauds).       Hence    the      information   on   frauds   is   overwhelmed   by   that   on   true   and   genuine   transactions.      This   imbalance   leads   the   ﬁtted   models   using   the   whole   data   predominately   led   by   the   information   on   ‘negatives’,   and   the   signal   on   ‘positives’   is   too   weak   to   be   picked   up.      To   balance   the   information   used   in   building   classiﬁers,   we   have   created   a   more   balanced   but,   unfortunately, much smaller training data with   24.62% positive   cases,   and   also   a   testing   data   set   which   is   about   equally   imbalanced   as   the   whole   data   set.
•      creditCardTrain.csv:   of size   1592×31, consisting of 1200 randomly selected non-fraudulent transactions   plus   392   randomly   selected   fraud   transactions.   The   true   positive   rate   is   about   24.62%.
•      creditCardTest   .csv:   of size   57889   × 31,   consisting   of   57789   randomly   selected   non-fraudulent   transac-   tions   plus      100   remaining   fraud   transactions.      It   has   no   overlaps   with      creditCardTrain   .csv.    The   true   positive   rate   is   0.173%.
The   two   data   sets   are   placed   on   the   course   Moodle   page.    For   your   information,   I   attach   below   the   codes   for   constructing   those   two   data   sets.
> library(readr); library(dplyr)
> CC=read_csv("creditcard   .csv")    # read_csv is a much faster version of read   .csv
> CC1=CC[CC$Class==1,]    # extract   all   frauds
> dim(CC1)
[1]   492      31
> train1=sample(1:492, 392)
> CC1train=CC1[train1,]
> CC1test=CC1[-train1,]
> CC0=CC[CC$Class==0,]    # e代 写ST309 – Exercise 6Python
代做程序编程语言xtract all genuine transactions
> dim(CC0)
[1]   284315               31
> train0=sample(1:284315, 58988)
> Dtrain=bind_rows(CC1train, CC0[train0[1:1200],])      # bind   the   rows   from   two   data   together
> dim(Dtrain)
[1]   1592         31
> Dtrain=arrange(Dtrain, Time)    # re-arrange rows according to ascending order of Time
> write.csv(Dtrain, row.names=F, "creditCardTrain.csv")
> Dtest=bind_rows(CC1test, CC0[train0[1200:58988],])
> dim(Dtest)
[1]   57889            31
> Dtest=arrange(Dtest, Time)
> write.csv(Dtest, row.names=F, "creditCardTest.csv")
> rm(CC, CC0, CC1, CC1test, CC1train, Dtest, Dtrain, train0, train1) # remove   those   objects
Your analysis should be based on creditCardTrain   .csv.   creditCardTest   .csv   represents the true reality,   and should be used only to test the   performance of your models.
1.   Carry   out   some   exploratory   data   analysis ﬁrst.   You   may   like   to   address   the   issues   such   as
• are there any missing values and outliers?                        [5 marks]
• should you apply any transformations to any variables, for example, log(Amount + 1)?                       [10 marks]
• is Time relevant to detecting frauds?                       [5 marks]
2.   Suppose that the credit card company charges 2%   fees   for   each transaction   (deducted   from   the   payment   to   payee).
(a)   Estimate   the   expected   potential   loss   of   a   fraudulent   transaction.                                                                                                       [5   marks]
(b)   Estimate   the   expected   proﬁt   from   a   genuine   transaction.                                                                                                                                     [5   marks]
(c)   Let   α   denote   the   probability   that   a   transaction   is   fraud.   What   is   the   minimum   value   of   α to   declare ‘Fraud’ in   order   to   ensure   that   the   expected   proﬁt   from   a   single   transaction   is   non-negative? [5   marks]A   simple   illustration   on   how   a   credit   card   works:   Suppose you purchase an item from a shop for   £100 payed   out   of   your   credit   card,   the   credit   card   company   will   pay   £98   to   the   shop   at   the   time.    By the end of   the month, you pay back ££100 to the credit   card company.   So   the   company make a proﬁt of   £2.   But   if   the   purchase   was   not   made   by   you   (i.e.   a   fraud), you   will   not   pay   anything   to   the   credit   card company.   The   company   will   make   a   loss   of   £98.
3.   Let   the   proﬁt   matrix   be
                       non-Fraud            Fraud
No                                    B                           −C
Yes                               −1                               0
where   C   and   B      are   calculated, respectively,   in   2(a)   and   2(b)   tomer’s unhappiness when a genuine transaction is denied.
(a)   Construct   a   decision   tree   for   detecting   frauds.
(b)   Find   the   value   of   the   cutting-of   probability, denoted   by α(^), which   maximizes   the   expected   proﬁt. [10   marks]
(c)   Test the performance of   your decision tree on the testing data set,   with   the cutting-of   probability   0.5   andα(^)   respectively.   Now you should calculate the true proﬁts or losses according to the real amount   of each transaction in the testing data sets.                                                                               [10   marks]
(d)   Construct a logistic regression model for detecting frauds.   You may use   the   same   predictor   selected   in the tree model   above.                   [10   marks]
(e)   Plot   the   ROC   curves   with   the   testing   data   for   both   the   tree   and   the   logistic   regression   classiﬁers constructed above, and compare them using the ‘area under   curve’   .                                                                                           [15   marks]
4.   In your opinion, what are   the pros and cons of   the above analysis?   Do   you   have   any   suggestions   for   further   improvement?                                               [10   marks]
Note.   The   strategy   to   build   classiﬁers   using   a   subset   with   a   much   higher   positive   rate   was   merged   after   some initial and less successful attempts.   This learning process also re丑ects one important principle of   data analytics:
Iteration   is   the   law   of   learning!

         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
