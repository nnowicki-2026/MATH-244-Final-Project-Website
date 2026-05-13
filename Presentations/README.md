---
title: "Analysis of Hollywood Movies"
subtitle: "244 Final Project Presentation"
author: "Team Movie: Nora Nowicki and Chunyi Quan"
format:
  revealjs:
    theme: default 
    footnote: "244 Final Presentation"
editor: 
  markdown: 
    wrap: 72
engine: knitr
---

## Why Movies? {.smaller}

-   Mainstream form of entertainment
-   Familiar topic to the audience
-   We both become nearsighted because we watch too many movies
-   We're interested to see what contributed to a successful movie

## Dataset: Hollywood Movies {.smaller}

-   GitHub
-   Movies that went to theaters from 2007-2013

```{r}
#|echo: false
library(dplyr)
library(tidyverse)
library(reticulate)
py_require(c("pandas"))
py_require(c("seaborn"))
py_require(c("matplotlib"))
py_require(c("scikit-learn"))
py_require(c("numpy"))
```
```{python}
#|echo: false
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.pipeline import Pipeline
from sklearn.compose import make_column_transformer
from sklearn.model_selection import cross_validate
from sklearn.model_selection import GroupKFold
from sklearn.linear_model import LinearRegression, Ridge, RidgeCV
from sklearn.linear_model import ElasticNet, ElasticNetCV
from sklearn.model_selection import train_test_split
from sklearn.linear_model import Lasso
from sklearn.pipeline import Pipeline
from sklearn.metrics import mean_squared_error, r2_score
```

```{r}
movies <- read.csv("/Users/noranowicki/Downloads/0-data science/HollywoodMovies.csv")
movies <- movies %>%
  mutate(BOAvgOpenWeekend = BOAvgOpenWeekend / 1000000)

movies <- movies %>% 
  drop_na()

movies <- movies %>% 
  mutate(across(where(is.character), ~ na_if(., ""))) %>% 
  drop_na()
head(movies)
```
## Codebook {.scrollable}

```{r}
movie_data <- movies %>% 
  select(Movie, RottenTomatoes, AudienceScore, Genre, TheatersOpenWeek, OpeningWeekend, BOAvgOpenWeekend, WorldGross, Budget, Profitability)
codebook <- tibble(
  Variables = paste0("`", names(movie_data), "`"),
  Description = c("Name of movie",
                  "The Rotten Tomatoes critics’ website ratings of movies",
                  "Scoring for the movie by the audience",
                  "Movie categories",
                  "Count of the number of theaters showing the movie for the open week",
                  "Total box office revenue earned during the opening weekend",
                  "The average box office revenue per theater during the opening weekend",
                  "Amount of money the movie grossed worldwide",
                  "Amount of money spent on movie production",
                  "How much money the movie made relative to its total cost over its entire run"),
  Value = c("Name",
            "Percent",
            "Percent",
            "Type",
            "Count",
            "Millions",
            "Millions",
            "Millions",
            "Millions",
            "Millions"))

knitr::kable(codebook)
```

## Data Cleaning {.smaller}

-   Replaced column: BOAvgOpenWeekend
       -   Change the unit of this column to million
-   Remove all rows with missing values
-   Remove all rows with empty values

## Outcome and Explanatory Variables {.smaller}

-   Outcome Variables: Profitability and World Gross


-   Explanatory Variables: Genre, RottenTomatoes, AudienceScore, TheatersOpenWeek, OpeningWeekend, BOAvgOpenWeekend, and Budget

## Data Summary {.scrollable}

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
| Domestic Gross | 0.36 | 44.67 | 74.91 | 760.5 |
| ForeignGross | 0.01 | 46.93 | 99.96 | 2021 |
| WorldGross | 1.52 | 91.4 | 174.74 | 2781.5 |
| Budget | 0.5 | 39 | 57.57 | 300 |
| Profitability | 15.25 | 254.32 | 371.73 | 6694.4 |
| OpenProfit | 0.34 | 36.9 | 59.65 | 1368 |
| Year | 2007 | 2009 | 2009 | 2013 |
| BOAvg Weekend | 0.0001504 | 0.0059195 | 0.0081153 | 0.0925000 |

## Movie Profitability vs. Worldwide Gross {.smaller}

-   Filtered out extreme outliers and found top five most frequent genres in data set

```{r}
movies <- movies %>%
  filter(Profitability <= 2000, WorldGross <= 1000) 
#filter out profitability <= 2000, and worldgross <= 1000 in order to remove extreme outliers

top5_genres <- movies %>%
  count(Genre, sort = TRUE) %>%  # Count occurrences of each genre
  slice_max(n, n = 5) %>%         # Take top 5
  pull(Genre)                     

# Filter data to only include top 5 genres
movies_top5 <- movies %>%
  filter(Genre %in% top5_genres)

# Visualize profitability and world gross with genre as the points
ggplot(data = movies_top5, aes(x = WorldGross, y = Profitability, color = Genre)) + geom_point() + geom_smooth(method = "lm", se = FALSE) + labs(
    title = "Movie Profitability vs. Worldwide Gross",
    x = "Worldwide Gross (in millions $)",
    y = "Profitability (in millions $)",
    color = "Film Genre"
  ) +
  theme_minimal()
```
## Audience Score vs. Worldwide Gross {.smaller}

-   Faceted on the top five movie genres
-   Looked at those against the audience score and world gross

```{r}
ggplot(data = movies_top5, aes(x = AudienceScore, y = WorldGross, color = Genre)) + geom_point() + geom_smooth(method = "lm", se = FALSE) + facet_wrap(~ Genre, scales = "free", ncol = 3) + labs(
    title = "Audience Score vs. Worldwide Gross",
    x = "Audience Score (%)",
    y = "Worldwide Gross (in millions $)"
  ) +
  theme_minimal()
```

## Distribution of World Gross by Movie Ratings {.smaller}

-   Range and IQR of Rotten Tomatoes scores is larger that audience score
-   Median audience score has a higher world gross

```{r}
critic_review <- pivot_longer(movies, 
                        cols = c(AudienceScore, RottenTomatoes),
                        names_to = "group",
                        values_to = "value")

ggplot(data = critic_review,
       mapping = aes(x = group, y = value, fill = group)) +
  geom_boxplot() +
  labs(title = "Distribution of World Gross by Rotten Tomatoes and Audience Scores",
       x = "Critic Scores (percent)", 
       y = "World Gross (million)") +
  theme_minimal()
```

## Number of Theaters Showing Movie vs. Opening Weekend Revenue {.smaller}

-   Fit an exponential line of best fit
-   Moderately strong relationship 

```{r}
ggplot(data = movies,
       mapping = aes(x = TheatersOpenWeek, y = OpeningWeekend)) +
  geom_point() +
  geom_smooth(method = "glm", 
              formula = y ~ x,
              method.args = list(family = gaussian(link = "log")),
              color = "red", se = FALSE) +
  labs(title = "Number of Theaters Showing Movie vs. Opening Weekend Revenue",
       x = "Number of Theaters Showing Movie",
       y = "Opening Weekend Revenue (in millions $)") +
  theme_minimal()
```
## Opening Weekend Distribution by Budget Cluster {.scrollable}

-   Clustering analysis
-   Created 5 clusters based on budget
-   Created boxplots to show the distribution of Opening Weekend Gross for each cluster

```{r}
#|echo: false
budget_data <- data.frame(Budget = movies$Budget)

set.seed(123)
kmeans_result <- kmeans(budget_data, centers = 5)
kmeans_result$centers

movies$Cluster <- as.factor(kmeans_result$cluster)

budget_ranges <- movies %>%
  group_by(Cluster) %>%
  summarise(
    Count = n(),
    Min_Budget = min(Budget, na.rm = TRUE),
    Max_Budget = max(Budget, na.rm = TRUE),
    Mean_Budget = mean(Budget, na.rm = TRUE),
    Budget_Range = paste("$", round(Min_Budget, 0), "-", round(Max_Budget, 0))
  )

print(budget_ranges)
print(kmeans_result$centers)

cluster_means <- movies %>%
  group_by(Cluster) %>%
  summarise(mean_opening = mean(OpeningWeekend, na.rm = TRUE)) %>%
  arrange(mean_opening)


movies$Cluster <- factor(movies$Cluster, 
                         levels = cluster_means$Cluster[order(cluster_means$mean_opening)])
```
## Cluster Graph {.smaller}

```{r}
cluster_graph <- ggplot(data = movies, aes(x = Cluster, y = WorldGross, fill = Cluster)) +
  geom_boxplot() + labs(title = "World Gross Distribution by Budget Cluster",
       x = "Budget Cluster",
       y = "Worldwide Gross (in millions $)") +
  stat_summary(fun = mean, geom = "point", shape = 18, size = 4, color = "black") +
  theme_minimal()
cluster_graph
```

## Conclusiong from Clustering {.smaller}

-  Larger budget is more likely to gain higher world gross 


-  Very unlikely to gain high world gross with small budget

## Multilinear Regression Predicting Profitability {.smaller}

```{r}
m1 <- lm(Profitability ~ RottenTomatoes + AudienceScore + TheatersOpenWeek + Genre + BOAvgOpenWeekend, data = movies_top5)
summary(m1)
```
## Multilinear Regression Predicting World Gross {.smaller}

```{r}
m2 <- lm(WorldGross ~ RottenTomatoes + AudienceScore + TheatersOpenWeek + Genre + BOAvgOpenWeekend, data = movies_top5)
summary(m2)
```

## Conclusions from Regression {.smaller}

-   Significant in both: audience score, number of theaters showing movie opening week, box office average revenue opening weekend 
-   Rotten tomatoes was not significant in either, but audience score was
-   Genre differs across the models
-   R^2 was stronger in the world gross model

## Overall Conclusions/Discussion {.smaller}

-   Clustering analysis showed us that movies with higher budgets tend to have higher world gross

-   Regression analysis showed us audience score, number of theaters showing movie opening week, box office average revenue opening weekend have a significant relationship with profitability and world gross

## Limitations {.smaller}

-   Only used data from 2007-2013

-   The data set was for Hollywood movies (not independent or foreign films)

-   A lot of missing categorical data when we filtered

## Potential Future Analysis {.smaller}

-   Run our analysis on movie data for more recent movies

-   Perform analysis on data set covering worldwide movies instead of only Hollywood

-   Find other movie rating sources
