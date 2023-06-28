---
title: "Results and Conclusion"
date: 2023-03-08T14:37:54+08:00
menu: main
weight: -97
---

## Results

![Event detection using the PELT Algorithm](pics/data_modeling.png)
The graphs show the result of applying  the Linearly penalized segmentation algorithm [1] (PELT) to a time-series data that records the number of misinformation/disinformation tweets about our chosen topic from the beginning of year 2016 to the end of year 2022.  We have tested different parameters for the algorithm and this is the configuration that best approximates the start and end period of our hypothesis. Unfortunately, the algorithm can only predict the start period of the hypothesis and not the end period of the hypothesis. Hence, we reject our hypothesis.

We note that the algorithm is able to detect the last lowest peak of the time-series data (June 2022) which is the month when BBM took his oath of office as 17th president of the Philippines. Hence, it is possible that we have made a mistake in choosing the end period of our hypothesis. Additionally, we observe that the highest peak occurred in October 2018 and the relevant event during that month is when BBM requests the inhibition of Associate Justice Caguioa in BBM's poll protest case [3].

With this, we can see that it was no longer an attempt to invalidate the Vice Presidential win of Robredo back in 2016 because they already lost the case. This time, the camp opposed to Robredo exhausted all means to defame the Robredo in their attempt to win the 2022 Presidency for Bongbong Marcos. They were successful in embedding a false narrative into the minds of their supporters.

Although we rejected our hypothesis, we were still able to correctly identify the starting date for the time period near the 2022 elections that saw the resurgence of tweets accusing Robredo of cheating in the 2016 elections. Since we focused mainly on the resurgence during the 2022 elections, we suggest that future researchers can identify time periods prior to the 2022 election that saw high volume of accusation tweets and identify the events that possible triggered them.

## Conclusion

Driven by the desire to pursue knowledge and social justice, we embarked on a journey to analyze the tweets accusing former Vice President Leni Robredo of cheating during the 2016 Philippine National Elections. A time-series analysis was done on tweets collected using a twitter scraper so that the time period that triggered the resurgence of the accusation tweets can be identified during the period of the 2022 Philippine National Elections. Using the PELT algorithm from *rupture*, we were able to correctly identify Robredo's announcement of candidacy for the 2022 elections as the starting period for the resurgence of the said tweets. However, we incorrectly hypothesized that the end date is the first SONA of Bongbong Marcos. The more accurate end of the time period is actually the time when Marcos took oath as the 17th president of the Republic of the Philippines. With this result, we were able to understand the desperation of the Marcos camp in winning the election. They were ready to undermine truth and the rule of law just for them to take control of the country that their family plundered before. With this, we stand strong and persevere in upholding the truth and our love for the Filipino people by continuing our research on their various crimes against the Filipino people. With the help of Data Science, we shall expose to the blinded masses the truth!

## References
[1] Killick, R., Fearnhead, P., & Eckley, I. (2012). Optimal detection of changepoints with a linear computational cost. Journal of the American Statistical Association, 107(500), 1590–1598.

[2] “Fact-check: 2016 vice presidency was not ‘stolen’ from Marcos,” #PressOnePH, https://pressone.ph/fact-check-2016-vice-presidency-was-not-stolen-from-marcos/ (accessed Jun. 7, 2023).

[3]“Marcos Seeks Inhibition of Associate Justice Caguioa in Poll Protest Case.” Cnn, 6 Aug. 2018, www.cnnphilippines.com/news/2018/08/06/Marcos-wants-Associate-Justice-Caguioa-out-election-case.html.'

[4] P. Pasion, “Timeline: Marcos-Robredo election case,” RAPPLER, https://www.rappler.com/newsbreak/iq/timeline-bongbong-marcos-leni-robredo-election-case/ (accessed Jun. 27, 2023). 