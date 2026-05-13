# # # MATH-244-Final-Project---Team-Movie

# Introduction/Data

For our final project, we will be analyzing a GitHub dataset containing 16 variables related to movies and 591 observations, after our data cleaning, where each observation represents a Hollywood movie from 2007 to 2013. The question we are attempting to answer by analyzing this data is, what variables contribute to making a successful movie? For this project, we will be defining success by the profitability and world gross of a movie. We chose this data and question because of our mutual love of movies, and because we were curious to discover what variables make a movie the most profitable. By investigating this topic, we aim to have a more comprehensive understanding of different features of movies and how they contribute to making a successful movie. 

Our outcome variables are profitability and world gross. Profitability is calculated by (WorldGross/Budget)*100, making it a measurement of how much profit a movie makes. World gross is a measurement of the total amount of money a movie makes in theaters, both domestic (in the United States) and abroad. Profitability will be able to explain how many times over the budget a movie earned in total revenue. This way, we can evaluate whether the budget of a movie is directly related to how much money it will make, and investigate the features of the highly profitable movies that are low-budget and high-return. When the profitability is above 100, it represents that the movie is making a profit. If not, then the movie suffered losses. 

Our chosen explanatory variables are Genre, RottenTomatoes, AudienceScore, TheatersOpenWeek, OpeningWeekend, BOAvgOpenWeekend, and Budget. Genre is a categorical variable classifying the movie's genre. RottenTomatoes is a score given as a percentage by critics for movies released in theaters. AudienceScore is also measured as a percentage and is the audience’s rating of the movie. TheatersOpenWeek measures the number of theaters that showed the movie during its opening weekend. OpeningWeekend measures a movie’s total revenue across all the theaters it was showing in for the first weekend after its release. BOAvgOpenWeekend measures the average revenue across all the theaters it was showing in on opening weekend. Finally, Budget is a measure of how much money was spent in the production of the movie, which can include day-to-day production costs, actors’ salaries, and advertising.


# Table of Summary Statistics


| Variable | Minimum | Median | Mean | Maximum |
| :---- | :---- | :---- | :---- | :---- |
| Movie |  |  |  |  |
| LeadStudio |  |  |  |  |
| Rotten Tomatoes | 0 | 48 | 49.55 | 99 |
| AudienceScore | 19 | 61 | 60.68 | 96 |
| Story |  |  |  |  |
| Genre |  |  |  |  |
| Theaters OpenWeek | 2 | 2875 | 2729 | 4468 |
| Opening Weekend | 0.032 | 14.8 | 23.184 | 174.14 |
| BOAvg Weekend | 151 | 5947 | 8134 | 93230 |
| Domestic Gross | 0.36 | 44.67 | 74.91 | 760.5 |
| ForeignGross | 0.01 | 46.93 | 99.96 | 2021 |
| WorldGross | 1.52 | 91.4 | 174.74 | 2781.5 |
| Budget | 0.5 | 39 | 57.57 | 300 |
| Profitability | 15.25 | 254.32 | 371.73 | 6694.4 |
| OpenProfit | 0.34 | 36.9 | 59.65 | 1368 |
| Year | 2007 | 2009 | 2009 | 2013 |
| BOAvg Weekend(M) | 0.0001504 | 0.0059195 | 0.0081153 | 0.0925000 |


# Codebook


| Variable | Description | Units |
| :---- | :---- | :---- |
| Movie | Movie name |  |
| LeadStudio | Movie production company name |  |
| RottenTomatoes | The Rotten Tomatoes critics’ website ratings of movies | Percent |
| AudienceScore | Scoring for the movie by the audience | Percent |
| Story | The type of story the movie is about  |  |
| Genre | Movie categories |  |
| TheatersOpenWeek | Count of the number of theaters showing the movie for the open week | Count |
| OpeningWeekend | Total box office revenue earned during the opening weekend | Millions |
| BOAvgWeekend | The average box office revenue per theater during the opening weekend | Dollars |
| DomesticGross | The amount of money the movie gross in the United States | Millions |
| ForeignGross | The amount of money the movie gross outside the United States | Millions |
| WorldGross | The amount of money the movie gross worldwide | Millions |
| Budget | Amount of money spent on movie production | Millions |
| Profitability | How much money the movie made relative to its total cost over its entire run | Millions |
| OpenProfit | Profit earned only during the opening weekend | Millions |
| Year | The year when the movie is produced | 2007 \- 2013 |
| BOAvgWeekend(M) | The modified version for BOAvgWeekend, changing the units from dollars to millions | Millions |


# Methodology


We started our initial investigation by cleaning the dataset. First, we had to make sure that all the variables that were in units of money were in millions of dollars. We replaced the BOAvgOpenWeekend column to be in units of millions, and we calculated that column by taking OpeningWeekend and dividing it by TheatersOpenWeek. After we made that change, we deleted missing observations from the dataset so that we could create visualizations and do analysis without the influence of empty entries.

Under the visualization part, the goal is to use plausible plots to illustrate the relationships between our outcome and explanatory variables. We used suitable visualization methods and statistical models to help support our analysis. First, we created scatter plots to examine various relationships with the genre variable. Next, we used side-by-side box plots to visualize the relationship between Rotten Tomatoes and audience score with world gross. We also created another scatter plot to show the relationship between the number of theaters showing a movie on opening weekend, and the revenue the movie made for opening weekend. By using scatterplots, it is possible to demonstrate the distribution of data and try to interpret the underlying reason for the distribution trend. For boxplots, it can be more straightforward to horizontally compare the range, median, and mean across different groups.

Because one of our outcome variables is profitability, we chose to do a clustering analysis on budget and world gross to find patterns related to a movie’s profitability. Budget and world gross contribute to a movie’s profitability, so by using a clustering model, we were able to analyze patterns in the variables that contribute to profitability. We used k-means clustering and grouped the Budget column into five groups all of different budget ranges. To visualize the clustering relationship, we build a box plot to show the relationship between the five clusters and the world gross.

We also chose to use a multilinear regression model to analyze several of our variables together and analyze how they contribute to predicting the outcome variables. After our exploratory data analysis, we chose to analyze audience and Rotten Tomatoes scores, genre, the number of theaters showing the movie opening weekend, and the box office average for opening weekend. We made a regression model with both profitability and world gross as outcomes to test our predictors against both and compare the results. 


# Results


In the first scatterplot between world gross and profitability, we plotted the two outcome variables together. We fitted regression lines for the top five genres based on size, then compared the slopes of the regression lines. The slope of each regression line shows the tendency of how much world gross and profitability affect the total success, which, when the slope is tilted more towards the axis for world gross, then the world gross will contribute more in measuring the final success of the movie, and vice versa. In the faceted scatterplot based on genre, we intentionally sorted the data points out into discrete graphs to see the internal distribution within each genre. This is beneficial for detecting new patterns because it prevents the data points from smaller genres with smaller worldwide gross scale from being clustered together at the bottom left corner. Then, we can compare the slope of the linear regression within each genre and detect that the action movies have the largest slope and largest gross scale, indicating that with the same audience score, action movies in general can make more money compared to other genres of movies. Showing a possible preference for action movies by the audience. 


<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/a24fafaa-fc62-4b91-97e7-eaf0f2b9bcf3" />


<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/9ff394f3-8868-4d82-9cae-3d23117b4ddb" />


From the side-by-side box plot of Rotten Tomatoes and audience score we saw that the Rotten Tomatoes plot had a larger range and IQR, meaning there was more variability in those scores. We also saw in that graph that the median of audience score was associated with a higher world gross than the median of Rotten Tomatoes. In the scatter plot we made for the number of theaters showing a movie on opening weekend and the revenue the movie made during opening weekend, we saw an exponential relationship and fit the graph with an exponential line. There appeared to be a moderately strong relationship between the variables, suggesting a relationship between the number of theaters showing a movie and the revenue it makes on opening weekend. That relationship makes sense, the more theaters a movie is showing in on opening weekend gives that movie more of an opportunity to earn revenue. 


<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/8836291c-1a5f-4dcf-b422-08095ebbf6c6" />


<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/22c786e9-04e0-4055-a4b5-f8b9769ffad6" />


From our clustering analysis, we were able to find that a larger budget is more likely to gain a higher world gross, and that it is very unlikely to gain a high world gross with a small budget. The mean for each budget group is discrete and shows distributions supporting the conclusion. Across all the clusters, there is a common trend that the mean of each group is greater than the median. This can be due to the outliers with great values within each group, and influencing the less robust mean. For each group, there are indeed outliers of gaining large world gross compared to their budget range, but the gross is still relatively low when compared to movies from larger budget groups.


<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/9929edd5-d64d-4e5f-bb19-e5a255e54e38" />


Both of our multilinear regression models were statistically significant with the same p-value of 2.2*e^-16. The profitability model had an adjusted  R^2 value of 0.1994 and the world gross model had an adjusted R^2 value of 0.6688, suggesting that the results from the world gross model explains more of the variability in the data. The results from our multilinear regressions found that audience score, the number of theaters showing a movie opening week, and box office average revenue opening weekend were the only variables that were statistically significant in predicting both profitability and world gross. We also observed that Rotten Tomatoes score was not statistically significant in either, therefore we cannot suggest that there is a significant relationship between the Rotten Tomatoes score of a movie and its success. Across both models we found there were no consistent relationships between the top five most frequent genres in our dataset and either of the outcomes. A genre would be significant in predicting one variable, and not in predicting the other, or it would have a negative coefficient in one model and a positive coefficient in the other. Because we saw no consistent relationships in the genres, we cannot conclusively suggest a relationship between a movie’s genre and how successful it is. 


# Discussion


Through our clustering analysis we were able to conclude that movies with a higher budget tend to have a higher world gross value. From our regression analysis we found that audience score, the number of theaters showing movie opening week, and the box office average revenue opening weekend have a significant relationship with profitability and world gross. Based on the patterns in our clustering analysis, movies with a higher budget are more likely to yield a higher world gross and be more successful. From our regression results we can conclude that there is a stronger positive relationship with the significant variables listed above with our outcomes, suggesting that for a movie to be more successful in both profitability and world gross, producers should target those three predictor variables. Combining the conclusions from both models, we might suggest to movie producers looking to maximize their success to aim for a high budget, audience score, and to play the movie in a lot of theaters opening weekend.

Our analysis was not without limitations, the largest of which is the time frame our dataset comes from. The data only encompassed 2007-2013, and because that is over a decade ago we cannot make as convincing an argument for how to predict the success of movies being released today. Another limitation of our data is that it only includes Hollywood movies and not independent or foreign films, making it hard to apply our results to those movies. During our data cleaning when we filtered out our missing variables, there was a lot of categorical data that was missing and caused our number of observations to shrink. 


For future investigations that expand the scope of our research, it is possible to run our analysis on a set of movie data containing more recent movies, since there are a significant amount of influential movies produced within the past 10 years. Also, people can perform analysis on a dataset covering worldwide movies instead of only Hollywood movies. This way, a more comprehensive pattern for movies from other countries can also be detected. Moreover, people can apply other movie rating sources, since Rotten Tomatoes are mostly not statistically significant, it will be supportive to use other rating sources other than Audience Score to justify the results. 


