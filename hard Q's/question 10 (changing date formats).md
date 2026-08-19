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

```

# MySQL

```

```

