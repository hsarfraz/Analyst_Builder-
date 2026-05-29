# Question

# R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(stringr)

# access your data
head(laptops)

laptops %>%
mutate(laptop_HD = ifelse(str_detect(laptop_name, "SSD"), 'SSD', 0),
       laptop_HD = ifelse(str_detect(laptop_name, "HDD"), 'HDD', laptop_HD)) %>%
arrange(laptop_id)
```

# Python

```
import numpy as np

laptops.head()

laptops['laptop_HD'] = np.where(laptops['laptop_name'].str.contains('SSD') == True, 'SSD', '0')
laptops['laptop_HD'] = np.where(laptops['laptop_name'].str.contains('HDD') == True, 'HDD', laptops['laptop_HD'])
laptops.loc[:,:].sort_values(by='laptop_id', ascending=True)
```

# MySQL

```
SELECT *,
  (CASE 
  WHEN laptop_name LIKE '%HDD%' THEN 'HDD'
  WHEN laptop_name LIKE '%SSD%' THEN 'SSD'
  ELSE 0
  END) AS laptop_HD
FROM laptops
ORDER BY laptop_id ASC;
```
