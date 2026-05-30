# question

# R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(stringr)

# access your data
head(bad_data)

bad_data %>%
mutate(
  customer_id = str_sub(id, 1, 5),
  first_name = str_sub(id, 6)
) %>%
select(customer_id, first_name)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;

bad_data.head()
bad_data['customer_id'] = bad_data['id'].str[:5]
bad_data['first_name'] = bad_data['id'].str[5:]

bad_data.loc[:,['customer_id', 'first_name']]
```

# MySQL

```
SELECT
  SUBSTRING(id, 1, 5) AS customer_id,
  SUBSTRING(id, 6) AS first_name
FROM bad_data;
```

# MSSQL

```
SELECT
  SUBSTRING(id, 1, 5) AS customer_id,
  SUBSTRING(id, 6, len(id)) AS first_name
FROM bad_data;
```

