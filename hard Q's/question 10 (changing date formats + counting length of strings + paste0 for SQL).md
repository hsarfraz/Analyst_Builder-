# R

```
#for this problem you need to look at the different data formats that exist
#the 2 date formats that exist "YYYY-MM-DD" or "MM-DD-YYYY"

# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(dates)

dates %>%
separate_wider_delim(cols = date,
                     delim = '-',
                     names = c('first', 'second', 'third')) %>%
mutate(
  standardized_date = ifelse(nchar(first) == 4, paste0(first, '-', second, '-', third), paste0(third, '-', first, '-', second))           
) %>%
select(standardized_date)
```

# Python

```
import pandas as pd
import numpy as np

dates[['first', 'second', 'third']] = dates['date'].str.split('-', expand=True)

dates['standardized_date'] = np.where(
    dates['first'].str.len() == 4,
    dates['first'] + '-' + dates['second'] + '-' + dates['third'],
    dates['third'] + '-' + dates['first'] + '-' + dates['second']
)

dates[['standardized_date']]
```

# MySQL

```
WITH df AS(SELECT *,
  SUBSTRING_INDEX(date, '-', 1) AS first,
  SUBSTRING_INDEX(SUBSTRING_INDEX(date, '-', 2), '-', -1) AS second, 
  SUBSTRING_INDEX(date, '-', -1) AS third
FROM dates)
  
SELECT 
  CASE 
  WHEN CHAR_LENGTH(first) = 4 
  THEN CONCAT(first, '-', second, '-', third) 
  ELSE CONCAT(third , '-', first , '-', second) END AS standardized_date
FROM df;
```

# postgresSQL

```
WITH df AS(SELECT *,
  split_part(date::text, '-', 1) AS first,
  split_part(date::text, '-', 2) AS second, 
  split_part(date::text, '-', 3) AS third
FROM dates)
  
SELECT 
  CASE 
  WHEN LENGTH(first) = 4 
  THEN CONCAT(first, '-', second, '-', third) 
  ELSE CONCAT(third , '-', first , '-', second) END AS standardized_date
FROM df;
```

# MSSQL

```

```

