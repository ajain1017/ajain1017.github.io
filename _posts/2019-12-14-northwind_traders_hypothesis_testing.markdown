---
layout: post
title:      "Northwind Traders Hypothesis Testing"
date:       2019-12-15 00:15:03 +0000
permalink:  northwind_traders_hypothesis_testing
---


The goal was to query an SQL database and generate analytical inferences that could be of value to the fictional Northwind Traders Vendor Company. I will summarize my approach to this project and what insight I may have found through the process.

At first, we were given the task to answer whether discount levels had a significant effect on quantity ordered by a customer. Before answering thsi question, I decided to work through the database and be familiar with the different the content. I immediately saw a few features that struck my eye revolving around employee demographic (whether age, location or experience), types of product and shipping details. I came up with these following questions:

Is there a statistically significant difference in the level(s) of discount offered by UK employees and USA employees? Does the age of the employee have a statistically significant effect on number of orders processed? Does the age of the employee have a statistically significant effect on the quantity of a product in an order? For each category type of product, does the season in which the order was placed have a statistically significant effect on the quantity of a product in an order? Does shipping company used have a statistically significant effect on the freight cost of shipping? Does shipping company have a statistically significant effect on processing time? yada yada yada

Testing for differences in group means when changing one variable is fairly easy, but finding a way to connect these issues togetehr to improve a business was harder. My mistake was not realizing a goal I wanted to achieve first and writing questions to help achieve that goal, rather I did the opposite. Regardless, I noticed one thing with all my questions, they all were concerned with finding ways to increase quantity ordered -> increasig revenue or finding new ways to be efficient in day to day operations -> lowering cost.

To answer the questions set forth, I found it best to use a one-way ANOVA test, followed by a confirmation test using Tukey Post-HOC HSD to find a significant difference in mean.

We conclude with 5.39 more units for the group with discounts applied than the group without discounts. Our effect size is also at 0.29, letting us know the difference in mean is not trivial. Therefore, quantity is in fact increased when a discount is applied. From our Tukey test, we also concluded that the means of groups with discount levels 0.05, 0.15, 0.20 and 0.25 differ from the mean of the group without discounts, except for the 10% level. However, most of our difference in means and effect sizes are relatively the same. So, since 15% discount has the largest difference in mean and largest effect size, I would suggest not offer discount levels above 15%.

On average, both UK and USA employees sell similar quantities per order of about 24 units, but US employees offer a number of low discount levels from 6% and below, whereas UK employees only offer 5%, 10%, 15%, 20% and 25% discounts. We concluded that US employees do in fact offer discounts at lower prices than UK employees with a mean difference of 1.3%. Discount levels of 1-6%, 15% and 20% have difference in means from the group with zero discount. Thus, it would be best for US employees to offer those levels to their customers in order to increase quantity. We again see that 15% discount has the largest difference in mean and largest effect size. Therefore, I would suggest not offering discount levels above 15%. For UK employees to increase quantity ordered, offering 25% would be best . That group had an effect size of 0.37, which shows strong relationship between Quantity and the 25% level compared to the other levels shown before.

That is a taste of some of the results I was able to obtain. I would go over all my results, but I'd rather discuss a few reasons why I found it useful to use ANOVA testing for all my tests, and the importance of using a Tukey Post-HOC HSD test afterwards.

We know that when you want to test if multiple means are different, you’ll perform ANOVA as it provides overall results for your data so you know if collectively, the difference in means is statistically significant or not.We also get a simple Null Hypothesis of all group means are equal Alternative Hypothesis that not all group means are equal. The downfall with ANOVA test is however, that our results do not distinguish between which group means are different from other groups. Luckily, our Tukey Post-HOC (which means 'after this' in Latin) HSD test compares group means.
With ANOVA tests and 2-sample t-tests, we find that experiment-wise error rate (probability of a type I error (false positive) over the total family of comparisons) increases with more groups. Using the Tukey test, we eliminate that error and get a 95% simultaneous confidence level over all intervals in our set of comparisons and we are then 95% confident we have a true value of difference in means between groups.

-Anu
