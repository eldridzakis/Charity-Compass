# Charity Compass
## A tool to scale nonprofit evaluation

### Introduction
Charity Navigator is a nonprofit that evaluates nonprofits. Part of their mission is to help donors make informed decisions, which is reflected in the 25 million dollars donated via their portal each year. Nonprofits benefit from the service they offer, and Charity Navigator would like to expand its reach and rate more charities. Over the past three weeks I worked on a data science consultancy project to help Charity Navigator achieve this goal.

Until recently, the addition of new charities has been slow as obtaining tax information on nonprofits took time. Last year that changed when the Internal Revenue Service released machine-readable IRS 990 tax returns from nonprofits, and the potential for rapid expansion of their rating system became apparent.  The vision was to use the financial health score, a score they calculate from these tax returns, to gauge how well their rating system scales to new unrated charities.

 
Figure 1 – A scatter plot of around 100,000 charities from the 2014 tax returns against total revenue and public support with corresponding histograms (top and bottom respectively). The dashed lines indicate two of Charity Navigators eligibility criteria while the yellow box illustrates the space in which Charity Navigator currently operate.



### Background
To understand the narrative, we need to cover a little ground; firstly, what criteria must a nonprofit meet to qualify for evaluation? Second, what metrics are used to calculate the financial health score?

Qualifying Nonprofits 
Charity navigator only rates qualifying nonprofits- that is, organizations that meet a set of criteria. One criterion is that the total revenue of the nonprofit must be greater than 1 million USD. Another is that at least 40% of the revenue must come from the public. Figure 1 shows the distribution of 100,000 charities in this space, with each point being an individual nonprofit. Looking at the sparsity of points in the scatter plot to the right of the vertical dashed line, charity navigator’s criteria of a revenue greater than 1 million USD means that they only rate large organizations. The shaded area on the scatter plot signifies the space in which Charity Navigator operates. In terms of growth, the organization wants to first find further nonprofits in its current space, then investigate extending the rating system to smaller charities, as those are the organizations that have the most to gain. It is unclear if their rating system which has been developed for larger charities would unintentionally penalize smaller charities. 

Financial Health Score
The financial health score is a critical component of the organization’s nonprofit evaluation and can be calculated from the IRS 990 tax returns. It comprises seven metrics, each of which receive a score for a given range of a continuous variable. As an example, we can look at metric 4- fundraising efficiency, defined as the money a charity spends to raise $1. As can be seen in the table below, 10 points are given for the most efficient fundraisers and zero points for ineffective fundraisers. The scores for each metric are summed together and then thirty points are added to a give a financial health score that varies from 30-100.

 
Table 1 – Showing how fundraising efficiency is scored. Similar scoring processes are applied to the other financial health metrics.

### Analysis
One assumption I’ve made in my approach is that to extend the current rating system to new charities, one would like the rating system to be as reliable as it is with the current charities it rates. My approach was to train a machine learning algorithm on the current rated nonprofits to predict the financial health score from the same metrics prior to their scoring. I then used this trained model to predict scores on currently unrated charities using the discrepancy between my predicted financial health score and the score calculated by Charity Navigator to measure the reliability of the score. If the agreement of the trained model for currently rated charities is comparable to a new subset of unrated charities, they can be confident that their financial health score can be extended to the new subset.


### Model Selection
In terms of model selection, one would like a model which can reproduce the action of binning the continuous metric variables into discrete scoring bins. It would also be advantageous for a model to perform badly when new charities are very different from the currently rated charities. For this reason, I used Extremely Randomized Trees regressor. 

 
Figure 3 – The predicted financial health score plotted against the current financial Health Score plotted with the corresponding histograms (right and top respectively). The dashed line is x=y. Red points are points which fall more than 3 standard deviations from the mean residual.

### Results
At the time of this analysis I had access to the 2014 tax returns, therefore I chose the 5600 organizations rated in that year for my model training and evaluation. After training on the training set (~4000 organizations), I used my model to predict the financial health score of the organizations in my test set of 1700 organizations. The results are shown in figure 3. The explained variance is close to 0.9, with a mean absolute error of 2 financial health points. The outliers (red points), make up 5% of the observations. The histograms for both the current and predicted show similar distributions, illustrating the ability of the model to reproduce the scoring system well.

Of the unrated charities available in the 2014 tax returns, 4200 were found to meet the eligibility criteria. The trained model was then applied to these organizations and the results are plotted in figure 4. The explained variance is close to 0.9 and the number of outliers is comparable to that for the test set (figure 3).  Both histograms show left skewed distributions with small peaks at a score of 100. Although in general it appears that the rating of these 4000 additional charities is in line with their current rating system, they may want to inspect a few of the outliers before including them, for example a charity with a predicted health score of 56 and actual of 42. 


Figure 4 – The model prediction for unrated eligible charities
This same trained model was applied to nonprofits with a revenue less than 1 million to investigate potential bias in the rating scoring system towards larger nonprofits. Figure 5 shows the results from fitting the model to 3400 charities with revenue from 0.1-0.5 million USD. The explained variance is again high, although the mean absolute error is 10% bigger, while the number of outliers is around 5%. The histograms show good agreement in the distribution of the scores. There are charities with discrepancies greater than 10 points, which would probably warrant further investigation. However, in general, a potential bias in the scoring system from developing the scoring system on larger charities is largely absent.

### Conclusions
Charity Navigator’s financial health rating score can be broadened reliably to an additional 4000 unrated nonprofits that meet their current eligibility criteria. This would result in a 40% increase in the total number of charities rated. 
Then extension to rating smaller charities appears feasible. The analysis suggests no bias towards larger organizations.
These findings have been well received by Charity Navigator and they intend to incorporate the currently eligible unrated charities in the coming months, followed by the	 possible inclusion of smaller charities by the end of the year. The next step will be to analyze the 2017 tax returns to produce the final recommendations.
 
Figure 5 – The model applied to charities with revenue from 0.1-0.5 million USD.



