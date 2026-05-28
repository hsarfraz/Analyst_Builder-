# question

# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(medication_information)

head(med_list)

med_list <- med_list %>% 
  rename(medication = medication_name,
         rec_dosage = recommended_dosage)
bind_rows(medication_information, med_list) %>%
select(medication, rec_dosage) %>%
arrange(medication)
```

# Python

```
import pandas as pd;

medication_information.head()

med_list = med_list.rename(columns={"medication_name": "medication", 'recommended_dosage':'rec_dosage'})
df = pd.concat([medication_information, med_list], axis=0) #sxis=0 tells python to do a vertical stack
df.loc[:,['medication', 'rec_dosage']].sort_values(by='medication', ascending=True)
```

# MySQL

```
(SELECT medication, rec_dosage
FROM medication_information)
UNION ALL
(SELECT 
  medication_name AS medication,
  recommended_dosage AS rec_dosage
FROM med_list)

ORDER BY medication ASC
```
