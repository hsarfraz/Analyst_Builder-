min_rank() gives a rank while maintaining the original sequence of numbers. So if two numbers are assigned rank one the next rank number will be 3

dense_rank() gives a rank and doesn't maintain the original sequence of numbers. So if two numbers are assigned rank one the next rank number will be 2

# R

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

# Python

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

# MYSQL

```
SELECT actions,
  COUNT(actions) AS counts,
  RANK() OVER (ORDER BY COUNT(actions) desc) AS ranks
FROM facebook
WHERE (STR_TO_DATE(dates, '%Y-%m-%d') = '2023-12-25')
GROUP BY actions
ORDER BY ranks ASC, actions ASC;
```
