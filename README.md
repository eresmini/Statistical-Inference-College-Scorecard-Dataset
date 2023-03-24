# Statistical-Inference-College-Scorecard-Dataset
  
Project completed in December, 2020.  
  
## Question of Interest
*Is there a difference in student loan debt between for-profit private insitutions and non-profit private
institutions?*  

Attending college/university is becoming increasingly expensive. Many people have no choice but to take out student loans, which then plagues them for years even after they’ve left school. Upon seeing the College Scorecard Dataset, I found myself interested in the differences between non-profit and for-profit private institutions. For-profit institutions market themselves as schools that can prepare you to directly enter the workforce upon graduation. However, for-profit institutions are often accused of overcharging students, and encouraging them to on unrealistic amounts of debt while not adequately preparing them for the workforce.  

Columns used: GRAD_DEBT_MDN, CONTROL, PREDDEG  

This project will use statistical inference.

## Preprocessing
The entire dataset is not needed for this test. Therefore, we will first filter to only include rows whose value in the CONTROL column is 2 or 3. Then, using, the mutate function, make a new column of the same values, but now the values 2 and 3 are renamed “NonProfit” and “ForProfit”, respectively. Next, mutate another column copying the values from the PREDDEG dataset, and recode the values 0-4 to their corresponding degree (as seen below). Finally, rename the column GRAD_DEBT_MDN to a more readable name, and select that and the two new mutated columns to form the new dataset.

``` Ruby
For_Non <- college %>%  

  filter(CONTROL == 2 | CONTROL == 3) %>%
  mutate(Ownership_Private = if_else(CONTROL == 2, "NonProfit", "ForProfit")) %>%
    mutate(Predominant_Degree = recode(PREDDEG,
      ‘0‘ = "Not_Classified",
      ‘1‘ = "Certificate",
      ‘2‘ = "Associates",
      ‘3‘ = "Bachelors",
      ‘4‘ = "Graduate_Only"
  )
) %>%
rename(Median_Debt_Grads = GRAD_DEBT_MDN) %>%  

select(Ownership_Private, Predominant_Degree, Median_Debt_Grads)
```

## Visualization
#### Histogram
The histograms below show the distribution of median student debt for graduated students. With
the histogram, we can determine the general shape of the distribution, and identify peak(s). There are spearate Non-Profit and For-Profit distributions for comparison, as well as facet-wrapping over the degree type.
![image](https://user-images.githubusercontent.com/70169642/227533430-db34d425-4833-4806-8c8a-a4f52b609735.png)

The first clear observation that can be made is the fact that each distribution shape for each type of degree is different. This likely contributes to the fact that different programs require different time commitments, and therefore students may accumulate more or less debt simply due to the amount of time in school.  

#### Modify Dataset:
For the rest of this analysis, we will be focusing on the median debt for bachelor’s programs. The graph in the previous section shows that the For-Profit distribution for bachelor’s degrees is strongly left-skewed, while the Non-Profit distribution is roughly symmetrical. For-Profit median debt has a peak around $30,000 while Non-Profit median debt peaks earlier around $25,000, so already we can see that For-Profit schools may result in higher loan debt.

``` Ruby
For_Non <- For_Non %>%
  filter(Predominant_Degree == "Bachelors")
```
#### Frequency Polygon
Now that we are only looking at median student loan debt from bachelor programs, below we have a frequency polygon to compare the distributions between Non-Profit and For-Profit. Frequency polygons show similar information as histograms, but the use of lines eliminates some of the overlap that can make histograms difficult to read, as well as giving a more accurate presentation of the shape of the distribution.
![image](https://user-images.githubusercontent.com/70169642/227534950-84820d72-e91e-4e55-b1f2-dd1923d67407.png)

The For-Profit distribution has several peaks, while the Non-Profit distribution has few peaks as well as one main peak around $27,000. As we saw from the histograms, the distributions do not follow a similar pattern to each other, however both have thin tails on both sides of their peak(s).

#### Boxplot
Next, we have boxplots, which can give a clearer picture of the spread of the distribution, as well as the presence of outliers, if any.  
Starting with the For-Profit distribution, we immediately see that the median sits just above $30,000 and the interquartile range spans from approximately $26,000 - $35,000. Additionally, by evaluating the position of the median inside the box, we see the distribution is left-skewed. There are both lower and upper extreme values, however there are more low-value outliers. The Non-Profit distribution has its median just around $25,000, and the interquartile range spans approximately from $22,000 to $27,000. The distribution skews slightly to the left, but could still be considered roughly symmetric. There are many outliers present, both lower extreme and upper extreme values.
![image](https://user-images.githubusercontent.com/70169642/227535284-e77d3daf-e951-4b92-9808-1331181cd592.png)

#### Violin Plot
The violin plot below appears similar to the boxplot, but now we will be able to see density. In other words, seeing where lower counts and higher counts of median debt lie on the distribution (making this closer to a histogram, actually). The Non-Profit distribution has the highest density around $27,000. Also, we see very thin tails on both sides of the peak (another way to see the outliers from the boxplot). There is a quick increase in density going to the peak, and then a stark drop after the peak, which causes the thin tails. The For-Profit distribution has its highest density around $32,000. This distribution has a more gradual rise in and fall in density compared to Non-Profit, but still also has thin tails on both sides.
![image](https://user-images.githubusercontent.com/70169642/227535525-7baf6a05-8a58-48e8-9b8b-ad598b454041.png)

## Summary Statistics
To generate summary statistics, we will need to produce the sum, standard deviation, mean, median, interquartile range (IQR), minimum, and maximum of each variable. First, we want to see how many total rows of data we are handling. Since the original data was filtered by Predominant_Degree, we use group For_Non by that variable and use the summarise() function with n() to see our grand sum of 1,504.
![image](https://user-images.githubusercontent.com/70169642/227536454-20863481-9027-4c52-a0c7-2e2d079b7bea.png)

Next, we look at median debt, grouped by private and public ownership. The number of For-Profit rows is 244, and the number of Non-Profit rows is 1,260. When analyzing the median debt by ownership, we see that the For-Profit data has a higher mean, median, and standard deviation than the Non-Profit data. The larger standard deviation suggests more variation in the data compared to Non-Profit. Additionally, the For-Profit IQR, 6353.125, has a wider range than the Non-Profit IQR, 5000. Interestingly, the overall range of the For-Profit data (max - min), 43888.5, is actually smaller than Non-Profit’s, 50000. However, remember that the total sum For-Profit data rows is less than Non-Profit (1260, compared to 244). This may explain why the Non-Profit data has a larger overall range, but less variation than the For-Profit data.
![image](https://user-images.githubusercontent.com/70169642/227536143-71ccc626-0b7f-4bfe-bc69-f5ea87a24506.png)

## Data Analysis
**Hypothesis Test:**  
*Null Hypothesis*: There is no difference in median student loan debt between bachelor’s programs of non-profit private institutions and for-profit private institutions.  
*Alternative Hypothesis*: There is a difference in median student loan debt between bachelor’s programs of non-profit private institutions and for-profit private institutions.  
  

The hypothesis test will be two-sided, and will use a significance level (α) of 0.05.  
*Test Statistic*: -5,481  
  
  
First, we are going to find the observed test statistic. Above, we saw the test statistic calculated by hand is -5,481, which was found by subtracting the medians of NonProfit and ForProfit. In the code chunk below, we use to specify() function to say which variable is the response, and which is the explanatory. Since we are looking at how the two different types of private institutions effect median loan debt for students who graduated, Median_Debt_Grads is the response variable and Ownership_Private is the explanatory. Then, with the addition of the calculate() function, we will be given our test statistic. Since we are asking about median student loan debt, inside the function we write stat = “diff in medians”, and for this test the order will be NonProfit, ForProfit (meaning the difference is always NonProfit minus (-) ForProfit). Name this new data frame (ForNon_stat, for example, as shown below), and run the code.

``` Ruby
ForNon_stat <- For_Non %>%
specify(Median_Debt_Grads ~ Ownership_Private) %>%
  calculate(
    stat = "diff in medians", order = vctrs::vec_c("NonProfit", "ForProfit"))
ForNon_stat
```
![image](https://user-images.githubusercontent.com/70169642/227537387-4c3dd85c-5a9a-44fc-8ae4-38f783788e9a.png)

The test statistic is *-5481*, identical to the number calculated by hand. Now, we run the hypothesis test! First, we again use specify() with Median_Debt_Grads as the
response variable and Ownership_Private as the explanatory variable. Next, use the hypothesize() function and type null = “independence” to show that we are using
two samples. Third, within the generate() we specify how many times (reps) to repeat the test. We will use 10,000. Also, for “type”, we choose permute, which will sample without replacement. Finally, with again use the calculate() function, filling in with stat = “diff in medians”, and the
order NonProfit, ForProfit. Name this new data frame (ForNon_null, for example, as shown below), and run the code.

``` Ruby
ForNon_null <- For_Non %>%
specify(Median_Debt_Grads ~ Ownership_Private) %>%
hypothesize(null = "independence") %>%
generate(reps = 10000, type = "permute") %>%
  calculate(
    stat = "diff in medians", order = vctrs::vec_c("NonProfit", "ForProfit"))
```
Now, we obtain the p-value. In short, the p-value is the evidence against the null hypothesis. To see this value, we use get_p_value() with obs_stat set to ForNon_stat, and remember to mark the direction as “two_sided” since that is the type of hypothesis test that is being run.

```Ruby
ForNon_null %>%
get_p_value(obs_stat = ForNon_stat, direction = "two_sided")
```
![image](https://user-images.githubusercontent.com/70169642/227537951-e96bf649-8558-4460-a4b6-17d45aae95a2.png)

We obtain a p-value of 0. This means the probability of obtaining our test statistic (-5417.184), assuming the null hypothesis is true, is 0. The value is less than the established significance level, α = 0.05, which means we can reject the null hypothesis.  
To visualise the p-value, we use visualize() with the ForNon_null data frame, then shade_p_value()
with ForNon_stat. Below, we see how far away the test statistic is from the null distribution.
![image](https://user-images.githubusercontent.com/70169642/227538226-89170a8c-0074-42e9-9754-c7b957a7a6b7.png)
  
From the hypothesis test, we reject the null hypothesis. There is sufficient evidence to conclude
that there is a difference in median student loan debt between bachelor’s programs of non-profit
private institutions and for-profit private institutions.

## Conclusion
As previously stated, sufficient evidence was gathered to reject the null hypothesis. There is reason to believe that there is a difference in median student loan debt between bachelor’s programs of non-profit private institutions and for-profit private institutions. From the Visualization section, we saw the difference shapes of each category’s distribution, and then with the summary statistics we saw that For-Profit had a higher mean and median than Non-Profit, as well as more variation. Finally, with the hypothesis test, we saw it would be highly unlikely for the median student loan debt between for-profit and non-profit institutions to be the same.  
This test did not make a statement about non-profit and for-profit institutions in all regards. We only focused on bachelor’s programs. As seen from the histograms in the Visualization section, the different degree programs have different distributions, and some program comparisons may show similar test results while others show the opposite, or one could choose to simply analyze institutions as a whole instead of breaking them down by degree. There are many approaches to determining if there really is a difference between non-profit and for-profit private institutions, and this hypothesis test only gave a possible answer to one part.
