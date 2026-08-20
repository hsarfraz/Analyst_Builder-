# Table of Contents

* [min rank + dense rank](#min-rank)
* [percent rank](#percent-rank)

## min and dense rank


min_rank() gives a rank while maintaining the original sequence of numbers. So if two numbers are assigned rank one the next rank number will be 3

dense_rank() gives a rank and doesn't maintain the original sequence of numbers. So if two numbers are assigned rank one the next rank number will be 2

## R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(departments)

left_join(employees, departments, by = c("department_id" = "department_id")) %>%
group_by(department_name, employee_name) %>%
summarise(total_salary = sum(salary)) %>%
mutate(ranks = min_rank(desc(total_salary))) %>%
filter(ranks == 2) %>%
select(department_name, employee_name, total_salary)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

departments.head()

df = pd.merge(
  employees,
  departments,
  how='left',
  left_on='department_id',
  right_on='department_id'
).groupby(['department_name', 'employee_name'])['salary'].sum().reset_index()

df['ranks'] = df.groupby('department_name')['salary'].rank(method='min', ascending=False)

df.loc[(df['ranks'] == 2),['department_name', 'employee_name', 'salary']]
```

## MYSQL

```
WITH df AS( 
SELECT 
  department_name, 
  employee_name,
  SUM(salary) AS total_salary,
  RANK() OVER (PARTITION BY department_name ORDER BY SUM(salary) desc) AS ranks
FROM employees employees_df
LEFT JOIN departments departments_df
  ON employees_df.department_id = departments_df.department_id 
GROUP BY department_name, employee_name)
  
SELECT department_name, employee_name, total_salary
FROM df
WHERE ranks = 2;
```

# percent rank

## What is percent rank?

Percent rank basically gets the column and orders the values from smallest to largest. It then assigns a number from 1 to a number as a rank and then it calculates the percentage/rank of each row value relative to the other values in the column

## R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(marketing_spend)

marketing_spend %>%
mutate(
  ROI = round(((revenue_generated - investment)/investment) * 100, digits = 0),
rank = percent_rank(ROI)*100
) %>%
arrange(desc(ROI), desc(campaign_id)) %>%
filter(rank <= 100 & rank >=75) %>%
select(campaign_id, campaign_name, ROI)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

marketing_spend.head()

marketing_spend['ROI'] = ((marketing_spend['revenue_generated'] - marketing_spend['investment']) / marketing_spend['investment']) * 100
marketing_spend['ROI'] = marketing_spend['ROI'].round()
marketing_spend['rank'] = (marketing_spend['ROI'].rank(pct=True) * 100)
marketing_spend.loc[ (marketing_spend['rank'] <= 100) & (marketing_spend['rank'] >= 75) ,['campaign_id', 'campaign_name', 'ROI']].sort_values(by=['ROI', 'campaign_id'], ascending = [False, False])


```

## MySQL

```
WITH df AS (SELECT campaign_id,
  campaign_name,
  ROUND(((revenue_generated - investment)/investment)*100, 0) AS ROI,
  PERCENT_RANK() OVER (ORDER BY (revenue_generated - investment)/investment)*100 as pct_rank
FROM marketing_spend
ORDER BY ROI DESC, campaign_id DESC)
  
SELECT campaign_id,
  campaign_name,
  ROI 
FROM df
WHERE pct_rank <= 100 AND pct_rank >= 75;
```
