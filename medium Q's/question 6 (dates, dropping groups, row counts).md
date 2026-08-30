# Table of Contents

* [converting dates](#converting-dates)
* [extracting date and time](#extracting-date-and-time)
* [Using group by and dropping groups to extract values based on one week intervals using dates](#using-group-by-and-dropping-groups-to-extract-values-based-on-one-week-intervals-using-dates)
* [subtracting dates and counting rows](#subtracting-dates-and-counting-rows)

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

* [table of contents](#table-of-contents)

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

## Using group by and dropping groups to extract values based on one week intervals using dates

## R

* [table of contents](#table-of-contents)

* `format(day_walked_format, "%w")` looks at the date and gives it a number which represents the day of the week with Sunday being 0 and Saturday being 6
* `weeks_numbered = as.integer(format(day_walked_format, "%w"))` The logic behind this code is that the dates are being "reset" to the beginning of it's respective week. For example, if the date is Saturday then the date minus 6 would change the date to the start of the week.
* `any()` checks if at least one of the values are true. If one of the values is true then the 'Bad Owner" label would be given
* if you are not using summarise() you would need to pipe and add `%>% ungroup() %>%` to ungroup

```
library(dplyr)

head(walks)

walks$day_walked_format <- as.Date(walks$day_walked, format='%m/%d/%Y')

walks %>%
mutate( 
day_of_the_week_numbered = as.integer(format(day_walked_format, "%w")),
start_of_the_week_date = day_walked_format - day_of_the_week_numbered
  ) %>%
group_by(owner_name, dog_name, start_of_the_week_date) %>%
summarise(total_times_walked = sum(times_walked),
          owner_type = ifelse(total_times_walked < 5, 'Bad Owner', 'Good Owner'),
          .groups = 'drop') %>%
group_by(owner_name) %>%
summarise(owner_type = ifelse( any(owner_type == "Bad Owner"), "Bad Owner", "Good Owner"),
  .groups = "drop" ) %>%
arrange(owner_name)
```

## Python

* [table of contents](#table-of-contents)

* `(walks['day_walked_format'].dt.dayofweek + 1) % 7` looks at the date and gives it a number which represents the day of the week with Sunday being 0 and Saturday being 6. The reason why 1 is being added and integer division by 7 is being used is to make the numbering with Sunday being 0 and Saturday being 6 because Monday is 0 and Sunday is 6. Integer division gets the remainder as the output.
* `pd.to_timedelta(walks['day_of_week'], unit='D')` gets the number which represents a day of the week and converts it to a date.
* `.apply(lambda x: (x == 'Bad Owner').any())` lambda looks at each group and applies the any() function on it

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np;

walks.head()

walks['day_walked_format'] = pd.to_datetime(walks['day_walked'], format = '%m/%d/%Y')

walks['day_of_week']  = (walks['day_walked_format'].dt.dayofweek + 1) % 7

walks['start_of_week'] = walks['day_walked_format'] - pd.to_timedelta(walks['day_of_week'], unit='D')

walks = walks.groupby(['owner_name', 'dog_name', 'start_of_week'])['times_walked'].sum().reset_index(name = 'total_walks')

walks['good_bad'] = np.where(walks['total_walks'] < 5, 'Bad Owner', 'Good Owner')

walks = walks.groupby('owner_name')['good_bad'].apply(lambda x: (x == 'Bad Owner').any()).reset_index(name='owner_type')

walks['owner_type'] = np.where(
    walks['owner_type'] == True,
    'Bad Owner',
    'Good Owner'
)

walks
```

## MySQL

```
WITH df AS (
    -- 1. Convert day_walked and find the start of its week
    SELECT
        owner_name,
        dog_name,
        times_walked,
        STR_TO_DATE(day_walked, '%c/%e/%Y') AS day_walked_format
    FROM walks
),

weekly_walks AS (
    -- 2. Group by owner, dog, and week
    SELECT
        owner_name,
        dog_name,
        DATE_SUB(
            day_walked_format,
            INTERVAL (DAYOFWEEK(day_walked_format) - 1) DAY
        ) AS start_of_week,
        SUM(times_walked) AS total_walks
    FROM df
    GROUP BY
        owner_name,
        dog_name,
        DATE_SUB(
            day_walked_format,
            INTERVAL (DAYOFWEEK(day_walked_format) - 1) DAY
        )
),

owner_status AS (
    -- 3. Determine whether each owner has ANY bad week
    SELECT
        owner_name,
        MAX(
            CASE
                WHEN total_walks < 5 THEN 1
                ELSE 0
            END
        ) AS has_bad_week
    FROM weekly_walks
    GROUP BY owner_name
)

-- 4. Turn 1/0 into Bad Owner/Good Owner
SELECT
    owner_name,
    CASE
        WHEN has_bad_week = 1 THEN 'Bad Owner'
        ELSE 'Good Owner'
    END AS owner_type
FROM owner_status
ORDER BY owner_name;
```

## PostgresSQL

```

```

## MSSQL

```

```

## subtracting dates and counting rows

* [table of contents](#table-of-contents)

## R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(customers)

customers$visit_date_format <- as.Date(customers$visit_date, format='%m/%d/%Y')


left_join(customers, customers, by = 'customer_id') %>%
mutate(date_difference = visit_date_format.y - visit_date_format.x) %>%
filter(receipt_id.x != receipt_id.y, 
       date_difference >= 0,
       date_difference <=5) %>%
group_by(customer_id) %>%
summarise(counts = n()) %>%
select(customer_id)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

customers.head()

customers['visit_date_convert'] = pd.to_datetime(customers['visit_date'], format = '%m/%d/%Y')

customers = pd.merge(
  customers,
  customers,
  how='left',
  left_on='customer_id',
  right_on='customer_id'
)

customers['date_diff'] = (customers['visit_date_convert_y'] - customers['visit_date_convert_x']).dt.days

customers = customers.loc[ (customers['date_diff'] <= 5) & 
  (customers['date_diff'] >= 0) &
  (customers['receipt_id_x'] != customers['receipt_id_y']) ,:]

customers = customers.groupby('customer_id')['date_diff'].count().reset_index(name='counts')

customers.loc[:,['customer_id']]
```

## MySQL and PostgresSQL

```
WITH df AS(SELECT 
  receipt_id AS receipt_id_y,
  customer_id AS customer_id_y,
  visit_date AS visit_date_y
FROM customers)
  
SELECT customer_id
FROM customers cdf 
LEFT JOIN df df 
ON cdf.customer_id = df.customer_id_y
WHERE (visit_date_y - visit_date) >= 0 
  AND (visit_date_y - visit_date) <=5
  AND (receipt_id != receipt_id_y)
GROUP BY customer_id;
```

## MSSQL

```
WITH df AS(SELECT 
  receipt_id AS receipt_id_y,
  customer_id AS customer_id_y,
  visit_date AS visit_date_y
FROM customers)
  
SELECT customer_id
FROM customers cdf 
LEFT JOIN df df 
ON cdf.customer_id = df.customer_id_y
WHERE (DATEDIFF(DAY, visit_date, visit_date_y)) >= 0 
  AND (DATEDIFF(DAY, visit_date, visit_date_y)) <=5
  AND (receipt_id != receipt_id_y)
GROUP BY customer_id;
```
