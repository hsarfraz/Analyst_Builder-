# question [link](https://www.analystbuilder.com/questions/multi-level-marketing-VXWrg)

# R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(tidyr)

# access your data
head(profits)
class(profits$date)

profits$date_format <- as.Date(profits$date, format = "%m/%d/%Y")

profits %>% 
  separate_wider_delim(
    cols = date, 
    delim = "/", 
    names = c("month", "day", "year")) %>%
mutate(month = as.numeric(month)) %>%
filter(date_format < "2024-07-01",
  date_format >= "2024-01-01") %>%
group_by(month) %>%
summarise(profit = sum(profit)) %>%
filter(profit > 0) %>%
arrange(desc(profit))
```

# python

```
import pandas as pd;

profits.head()

profits['date_convert'] = pd.to_datetime(profits['date'], format='%m/%d/%Y')
profits[['month', 'day', 'year']] = profits['date'].str.split('/', expand=True)
profits['month'] = pd.to_numeric(profits['month'])
profits = profits.loc[ (profits['date_convert'] < "2024-07-01") & (profits['date_convert'] >= "2024-01-01") ,:]
profits = profits.groupby('month')['profit'].sum().reset_index()
profits.loc[(profits['profit'] > 0),:].sort_values(by='profit', ascending = False)
```

# MySQL

```
SELECT 
  CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(date, '-', 2), '-', -1) AS INT) AS month,
  SUM(profit) AS total_profit
FROM profits
WHERE (STR_TO_DATE(date, '%Y-%m-%d') < "2024-07-01") AND (STR_TO_DATE(date, '%Y-%m-%d') >= "2024-01-01")
GROUP BY CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(date, '-', 2), '-', -1) AS INT) 
HAVING (SUM(profit)) > 0
ORDER BY total_profit DESC
  LIMIT 10;
```

# PostgresSQL

important to note that I didn't need to convert the date column to a datetype column since the date format was in 2024-01-01 and postgresSQL already converts dates with dashes to a date

```
SELECT
  EXTRACT(MONTH FROM date::date)::int AS month,
  SUM(profit) AS total_profits
FROM profits
WHERE date < '2024-07-01' AND date >= '2024-01-01'
GROUP BY EXTRACT(MONTH FROM date::date)::int
HAVING SUM(profit) > 0
ORDER BY total_profits DESC;
```

would like to include the snippet below in situations where you are not dealing with dates and can't just simply specify a month to be extracted

```
SELECT customer_id, name, email
FROM gmail_users
WHERE email LIKE '%@gmail.com'
ORDER BY customer_id ASC;
```

# MSSQL

```
SELECT
  MONTH(CAST(date AS date)) AS month,
  SUM(profit) AS total_profits
FROM profits
WHERE date < '2024-07-01' AND date >= '2024-01-01'
GROUP BY MONTH(CAST(date AS date))
HAVING SUM(profit) > 0
ORDER BY total_profits DESC;
```

would like to include the snippet below in situations where you are not dealing with dates and can't just simply specify a month to be extracted

```
SELECT customer_id, name, email
FROM gmail_users
WHERE email LIKE '%@gmail.com'
ORDER BY customer_id ASC;
```
