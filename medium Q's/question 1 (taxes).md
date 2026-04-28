* sorting column values in R, Python in descending order (Z-A)

# Question

It's everyone's favorite time of the year! Tax season!

Let's help these business know how much taxes they owe for this year!

Calculate the taxes owed by each company for the most recent fiscal year. The taxes owed can be calculated as (Taxable Income * Tax Rate).

If a company doesn't have data for the most recent year they should not be included in the output.

The output should have the columns Company_Name, Fiscal_Year, and Taxes_Owed, and should be ordered by Taxes_Owed in descending order.

# R 

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(company_financials)

company_financials %>%
filter(fiscal_year == max(fiscal_year, na.rm=TRUE)) %>%
mutate(taxes_owed = tax_rate * taxable_income) %>%
arrange(desc(taxes_owed)) %>%
select(company_name, fiscal_year, taxes_owed)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;

company_financials.head()

df = company_financials.loc[ company_financials['fiscal_year'] == max(company_financials['fiscal_year']) ,:]
df['taxes_owed'] = df['taxable_income'] * df['tax_rate']
df.loc[:, ['company_name', 'fiscal_year', 'taxes_owed'] ].sort_values(by= 'taxes_owed', ascending=False)
```
