---
title: "Case: Flight Delay"
author: "Pavani Pragada U1448226"
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, message = FALSE, warning = FALSE)
```

<!-- Note:   -->
<!-- These instructions are commented out and will not display when you knit your RMarkdown document. -->

<!-- - Change the information in the yaml header above:  title, author, data. -->
<!-- - Make sure output is html_document. -->
<!-- - Once you are finished coding, **run each chunk individually to make sure there are no errors**.  (If necessary fix your code.) Once your code is error-free, click "knit" on the menu above. Your document should compile to HTML, provided that you have output set to "html_document." -->
<!-- - In the code chunk above ("setup") echo is set to TRUE.  This means that the code in your chunks will be displayed, along with the results, in your compiled document. -->


## Load packages and data
```{r}
# Load packages
library(tidyverse) 

# Load data
d <- read_csv("flight_delay_clean.csv")

# Inspect data
glimpse(d)
summary(d)

```

The dataset has been cleaned for you and is ready for analysis.

## Questions

Lightly comment your code and use pipes for readability.

Most of the questions require some writing.  Please place your text immediately after the ">" in each question, which will make it into a block quote.

Comment briefly on each of the questions, as directed.  Only the the final question requires a lengthier response.

### Q1

Compute the mean, median, 90th percentile, and standard deviation of arrival delay minutes for RegionEx flights. Do the same for MDA flights. Which measure of central tendency would be most appropriate for comparing airline performance?

```{r}
d%>%
  group_by(airline)%>%
  summarise(mean = mean(delay),
            median = median(delay),
            perc_90 = quantile(delay,probs=c(.9)),
            sd = sd (delay))
  
```

> As mentioned by FAA, it is true that the RegionEx's average delay time is higher than MDA's average delay time.But the median for RegionEx is lower than the MDA's median indicating that the distribution is not normal but skewed. Therefore, mean is not the correct factor to measure airline performance and median should be used to measure the performance.
Also, Standard deviation of RegionEx is extremely higher than MDA's(27.6 vs 6.33) - wider distribution of data points, depicting that the data is spread out over a large range of values.


### Q2

Inspect the distribution of RegionEx's arrival delays by constructing a histogram of the number of arrival delay minutes of RegionEx's flights. Do the same for MDA's flights. How do these two distributions compare? Hint:  use facet_wrap().

```{r}
# Constructing histogram
ggplot(data = d, aes(x = delay)) + 
  geom_histogram() + 
  facet_wrap(~airline) +
  labs(title = "Delays in Arrival ~ Airline")
```

> Due to high value outliers in RegionEx, the mean is observed higher than the MDA which is making its distrubution skewed more to the right and MDA's distribution is normal.Also, RegionEx is having higher data points than MDA which means more number of flights performed by RegionEx.

### Q3

So far we have considered airline performance in terms of average delay in minutes.  However, the performance metrics, as noted in the case description, also include the percentage of delayed flights.  Let's verify that MDA's COO is correct: does RegionEx have a higher percentage of delayed flights?

Here is code to answer that question:

```{r}
# Create a summary table of percent delayed by airline.
d %>% 
  group_by(airline) %>% 
  summarize(n = n(),
            percent_delay = (mean(delay_indicator) * 100) %>% round(1)) 

```

Note that because `delay_indicator` is numeric (a binary 0/1 variable) calculating the mean of the vector returns the proportion of 1s, which, multiplied by 100, is equivalent to the *percentage* of delayed flights.

Write your own code to create a table summarizing the percentage of delayed flights by airline *and route.*  

```{r}
d %>% 
  group_by(airline,route_code) %>% 
  summarize(n = n(),
            percent_delay = (mean(delay_indicator) * 100) %>% round(1)) 
```

These tables contain conflicting information. How should you answer the question of whether RegionEx has a higher percentage of delayed flights?  Is the the COO correct?  And, if not, why not?

> Considering the flight route details, it is clear that the flight delay time for both airlines varies only on the route MSY-DFW, whereas the other route flight delay percentage is the same for both the airlines.MDA is having more delay time via MSY-DFW route when compared to RegionEx(30 vs 28.9).Only because RegionEX has 3 times more number of flights than MDA through this route, it has more overall delay percentage than MDA(MDA - 25.8% and RegionEX - 26.2%).
MDA's COO should be happy with this performance.

### Q4

Compare the scheduled flight durations for the two airlines on each of their four routes. Also compare the actual flight durations for the two airlines. What do you notice? If the two airlines had the same scheduled duration, what impact would this have on their delay records?

```{r}
d %>% 
  group_by(airline,route_code) %>% 
  summarize(n = n(),
            scheduled_flight_length = mean(scheduled_flight_length),
            actual_flight_length = mean(actual_flight_length)) 
```

> Comparing the scheduled flight durations,MDA is scheduling 5 minutes more than RegionEx for MSY/PNS and 10 minutes more for MSY/DFW.
RegionEX completes its flight route in less time than MDA for all the routes. If the two airlines had the same scheduled duration, MDA flight delay percentage would increase.Also,MDA schedules more time to complete each route, making it easier to avoid delays despite their longer actual flight lengths.

### Q5

Does the data support the claim that the on‐time performance of RegionEx is worse than that of MDA? Write a paragraph in which you argue a position. In your answer, please incorporate quantitative evidence from the earlier questions.

> Based on the quantitive inferences from previous questions, the on-time performance of RegionEx is better than that of MDA when individual routes are considered.RegionEx's flight delay percentage is higher than MDA's (26.2 vs 25.8) because RegionEX has three times more flights than MDA through MSY/DFW route.RegionEx has a lower delay percentage on this route (28.9 vs 30.0) than MDA, but the higher number of flights (90 vs 30) raises RegionEx's overall average delay percentage. If the number of flights by RegionEx and MDA through the MSY/DFW route are same, then the flight delay percentage of both airlines would be more or less the same or even better for RegionEx.
In addition, when the scheduled and actual flight duration is considered for all the routes, RegionEx flies each route in less time than MDA. MDA is on the safer side because of its higher scheduled flight duration. If both airlines had the same scheduled flight duration, MDA performance would drop drastically than RegionEX.
To address this issue or remove the negative perception of RegionEx from a customer perspective, RegionEx should increase its scheduled flight duration and match it with MDA or have same number of flights as MDA in MSY/DFW route.This will demonstrate that RegionEx outperforms MDA and will improve customers' perception of RegionEx.

