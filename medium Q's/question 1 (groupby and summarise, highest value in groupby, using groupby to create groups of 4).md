# Table of Contents

* [groupby and summarise](#groupby-and-summarise)
* [highest value in groupby and selecting rows and rounding row values](#highest-value-in-groupby-and-selecting-rows-and-rounding-row-values)
* 
 using groupby to create groups of 4

* [row counts of groups](#row-counts-of-groups)
* [removing duplicate rows then doing row counts](#removing-duplicate-rows-then-doing-row-counts)

* sorting column values in R, Python, MySQL in descending order (Z-A)

# groupby and summarise

* [table of contents](#table-of-contents)

James is a Help Desk Manager in their IT Department. He wants to know the resolution rates for each of his employees.

Each call the help desk receives is either marked as "Y" for resolved or "N" for not resolved.

Calculate each employees percentage of calls resolved compared to all their calls.

Output the name of the employee and their resolution rate. Order on their name alphabetically.

## R 

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(help_desk)

help_desk %>%
mutate(call_outcome = ifelse(call_outcome == 'Y', 1,0)) %>%
group_by(employee_name) %>%
summarise(percentage = mean(call_outcome) * 100) %>%
arrange(employee_name)
```
If you need to remove NA values in the summarise function calculations

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(restaurant_reviews)

restaurant_reviews %>%
group_by(restaurant) %>%
summarise(comment_count = sum(!is.na(comment) & comment != ""),
          avg_rating = mean(rating, na.rm=TRUE)) %>%
arrange(desc(comment_count), desc(avg_rating)) %>%
head(1)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;
import numpy as np

help_desk.head()

help_desk['call_outcome'] = np.where(help_desk['call_outcome'] ==  'Y', 1, 0)
(help_desk.groupby('employee_name')['call_outcome'].mean() * 100).reset_index().sort_values(by='employee_name', ascending=True)
```

when you want to use multiple functions in a group by

```
# access datasets as pandas dataframes
import pandas as pd;

restaurant_reviews.head()

restaurant_reviews['not_null_comment'] = (restaurant_reviews['comment'].notna()) & (restaurant_reviews['comment'].notna() != '')

restaurant_reviews.groupby('restaurant').agg(
total_patients = ('patient_id' , 'size'), #size counts all rows in the groupby
  comment_count = ('not_null_comment' , 'sum'),
rating_mean = ('rating' , 'mean')
).reset_index().sort_values(by=['comment_count', 'rating_mean'], ascending = [False, False]).head(1)
```

## MySQL

```
SELECT employee_name,
  (SUM(CASE WHEN call_outcome == 'Y' THEN 1 ELSE 0 END)/COUNT(*))*100 AS call_proportion
FROM help_desk
GROUP BY employee_name
ORDER BY employee_name ASC;
```

Here is a scenario when you need to include multiple ifelse statements

```
SELECT 
  (CASE 
  WHEN response = 'Yes' THEN 'Y'
  WHEN response = 'No' THEN 'N'
  ELSE response
  END) AS response
FROM responses;
```

## PostgresSQL

```
SELECT employee_name,
  (SUM(CASE WHEN call_outcome == 'Y' THEN 1 ELSE 0 END)::float/COUNT(*))*100 AS call_proportion
FROM help_desk
GROUP BY employee_name
ORDER BY employee_name ASC;
```

## MSSQL

```
SELECT employee_name,
  (CAST(SUM(CASE WHEN call_outcome == 'Y' THEN 1 ELSE 0 END) AS FLOAT)/CAST(COUNT(*) AS FLOAT))*100 AS call_proportion
FROM help_desk
GROUP BY employee_name
ORDER BY employee_name ASC;
```

# highest value in groupby and selecting rows and rounding row values

* [table of contents](#table-of-contents)

*  Question [link](https://www.analystbuilder.com/questions/biggest-country-debts-FVIGT)

The world is taking on debt like there is no tomorrow, but which country is taking on the most?

Write a query to find the top 3 countries with the largest national debt for the most recent year.

The output should have the columns Country and National_Debt (round to the nearest whole number), and should be ordered by National_Debt in descending order.

## R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(global_debts)

global_debts %>%
group_by(country) %>%
filter(year == max(year)) %>%
mutate(national_debt = round(national_debt)) %>%
arrange(desc(national_debt)) %>%
select(country, national_debt) %>%
head(3)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

global_debts.head()

# ['year'] says to get the max year only
# .idxmax() gets the index of each country with the highest year
max_year = global_debts.groupby('country')['year'].idxmax() 

global_debts.loc[max_year, ['country', 'national_debt']].round({'national_debt':0}).sort_values(by='national_debt', ascending=False).head(3)
```

## MySQL

```
SELECT country,
  ROUND(national_debt) AS national_debt
FROM global_debts as main
WHERE year = (
  SELECT MAX(year) 
  FROM global_debts as sub 
  WHERE main.country = sub.country)
ORDER BY national_debt DESC
LIMIT 3;
```

## PostgresSQL

* when using `ROUND()` it is important that the value inputting inside it is numeric

```
SELECT
ROUND((right_vote / (right_vote + left_vote))::numeric * 100, 2) AS Right_Twix_Percentage,
ROUND((left_vote / (right_vote + left_vote))::numeric * 100, 2)  AS Left_Twix_Percentage
FROM candy_poll;
```

## MSSQL

```
SELECT TOP 3 country, 
  ROUND(CAST(national_debt AS Numeric),0) as national_debt
FROM global_debts AS main
WHERE year = (
  SELECT MAX(year) 
  FROM global_debts AS sub
  WHERE main.country = sub.country)
ORDER BY national_debt DESC;
```

