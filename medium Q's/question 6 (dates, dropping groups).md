# Table of Contents

* [converting dates](#converting-dates)
* [extracting date and time](#extracting-date-and-time)

## Converting Dates

 question [link](https://www.analystbuilder.com/questions/multi-level-marketing-VXWrg)

## R

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

## python

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

## MySQL

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

## PostgresSQL

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

spliting columns when there is no date

```
SELECT customer_id,
  SPLIT_PART(full_name, ' ', 1) AS first_name
FROM customers;
```

## MSSQL

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

spliting columns when there is no date

CHARINDEX(' ', full_name + ' '): Finds the exact position of the first space. Appending an extra ' ' (space) ensures the function doesn't fail if the full_name only has a single word and no spaces.LEFT(full_name, ... - 1): Extracts all characters in full_name to the left of that space.

```
SELECT customer_id,
  LEFT(full_name, CHARINDEX(' ', full_name + ' ') - 1) AS first_name
FROM customers;
```

## Extracting date and time

## R

* `as.POSIXct()` is a function that is used to convert a value to a datetime object. So the date and time is included
* `as.POSIXct(paste(as.Date(check_out), "10:00:00"))` is basically getting the date from the date column and merging it with the time which is 10 AM

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(hotel_guests)

hotel_guests$check_out <- as.POSIXct(hotel_guests$check_out, format = "%m/%d/%Y %H:%M")

hotel_guests %>%
mutate(check_out_hours = format(check_out, "%H:%M:%S")) %>%
filter(check_out > as.POSIXct(paste(as.Date(check_out), "10:00:00"))) %>% 
mutate(counts = n()) %>%
select(counts) %>%
head(1)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

hotel_guests.head()

hotel_guests['check_out'] = pd.to_datetime(hotel_guests['check_out'], format='%m/%d/%Y %H:%M')

hotel_guests["check_out_hours"] = hotel_guests["check_out"].dt.strftime("%H:%M:%S")

hotel_guests = hotel_guests.loc[ (    hotel_guests["check_out"] > pd.to_datetime(hotel_guests["check_out"].dt.date.astype(str) + " 10:00:00")) ,:]

hotel_guests.count().reset_index(name = 'counts').loc[:,['counts']].head(1)
```

## MySQL

```
SELECT COUNT(*) AS counts
FROM hotel_guests
WHERE TIME(check_out) > '10:00:00';
```

## PostgresSQL

```
SELECT COUNT(*) AS counts
FROM hotel_guests
WHERE check_out::time > '10:00:00'::time;
```

## MSSQL

```
SELECT COUNT(*) AS counts
FROM hotel_guests
WHERE CAST(check_out AS time) > '10:00:00';
```
