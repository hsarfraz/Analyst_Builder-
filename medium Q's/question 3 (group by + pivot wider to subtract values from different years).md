# Table of Contents

* [using group by and pivot wider to subtract values from different years](#using-group-by-and-pivot-wider-to-subtract-values-from-different-years)
* [using group by and getting row counts of a column based on certain string values](#using-group-by-and-getting-row-counts-of-a-column-based-on-certain-string-values)


## using group by and pivot wider to subtract values from different years

* [table of contents](#table-of-contents)

## R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(tidyr) # Needed for pivot_wider

# access your data
head(products)

products %>%
group_by(year, company_name) %>%
summarize(n = n(), .groups = 'drop') %>%
pivot_wider(names_from = year, values_from = n, values_fill = 0) %>%
mutate(difference = `2023` - `2022`) %>%
select(company_name, difference) %>%
arrange(company_name)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

products.head()

df = products.groupby(['year', 'company_name'])['product_name'].count().reset_index()
df = df.pivot_table(index='company_name', columns='year', values='product_name', fill_value=0).reset_index()
df['difference'] = df[2023] - df[2022]
df.loc[:,['company_name', 'difference']].sort_values(by='company_name',ascending=True)
```

## MySQL

```
SELECT company_name,
  SUM(CASE WHEN year = 2023 THEN 1 ELSE 0 END) - SUM(CASE WHEN year = 2022 THEN 1 ELSE 0 END) AS difference
FROM products
GROUP BY company_name
ORDER BY company_name ASC;
```

## using group by and getting row counts of a column based on certain string values

* [table of contents](#table-of-contents)

## R

* `sum(state == 'Approved')` looks at the state column and turns every value to a true and false boolean. When sum is used it adds up all the true values
* `sum(amount[state == 'Approved'])` looks at the amount column and gets all the values that were approved and then it is all added by sum()

```
# You can load libraries like dplyr if needed
library(dplyr)
library(tidyr) # Needed for pivot_wider

# access your data
head(transactions)

transactions$transaction_date_format <- as.Date(transactions$transaction_date, format='%m/%d/%Y')
transactions$Country <- transactions$country

transactions %>%
mutate(Months = format(transaction_date_format, '%m')) %>%
group_by(Months, Country) %>%
summarise(
  Approved_Transactions = sum(state == 'Approved'),
  Approved_Amount = sum(amount[state == 'Approved']),
  Chargebacks = sum(state != 'Approved'),
  Chargeback_Amount = sum(amount[state != 'Approved'])
          ) %>%
arrange(Months)
```


## Python

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np;

transactions.head()

transactions['transaction_date_format'] = pd.to_datetime(transactions['transaction_date'], format='%m/%d/%Y')

transactions['Month'] = transactions['transaction_date_format'].dt.month

# old column name, new column name
transactions = transactions.rename(columns = {'country' : 'Country'})

transactions['Approved_Transactions'] = np.where(transactions['state'] == 'Approved', 1, 0)

transactions['Approved_Amount'] = np.where(transactions['state'] == 'Approved', transactions['amount'],  0)

transactions['Chargebacks'] = np.where(transactions['state'] != 'Approved', 1, 0)

transactions['Chargeback_Amount'] = np.where(transactions['state'] != 'Approved', transactions['amount'],  0)


transactions

transactions.groupby(['Month', 'Country']).agg(
  Approved_Transactions = ('Approved_Transactions', 'sum'),
  Approved_Amount = ('Approved_Amount', 'sum'),
  Chargebacks = ('Chargebacks', 'sum'),
  Chargeback_Amount = ('Chargeback_Amount', 'sum')
).reset_index()
```

## MySQL and MSSQL

```
SELECT 
  MONTH(transaction_date) AS Month,
  country AS Country,
  SUM(CASE WHEN state = 'Approved' THEN 1 ELSE 0 END) AS Approved_Transactions,
  SUM(CASE WHEN state = 'Approved' THEN amount ELSE 0 END) AS Approved_Amount,
  SUM(CASE WHEN state != 'Approved' THEN 1 ELSE 0 END) AS Chargebacks,
  SUM(CASE WHEN state != 'Approved' THEN amount ELSE 0 END) AS Chargeback_Amount
FROM transactions
GROUP BY MONTH(transaction_date), country
ORDER BY Month ASC;
```

## PostgresSQL

```
SELECT 
  EXTRACT(MONTH FROM transaction_date) AS Month,
  country AS Country,
  SUM(CASE WHEN state = 'Approved' THEN 1 ELSE 0 END) AS Approved_Transactions,
  SUM(CASE WHEN state = 'Approved' THEN amount ELSE 0 END) AS Approved_Amount,
  SUM(CASE WHEN state != 'Approved' THEN 1 ELSE 0 END) AS Chargebacks,
  SUM(CASE WHEN state != 'Approved' THEN amount ELSE 0 END) AS Chargeback_Amount
FROM transactions
GROUP BY EXTRACT(MONTH FROM transaction_date), country
ORDER BY Month ASC;
```
