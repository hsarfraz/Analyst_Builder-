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

```

# MySQL

```

```
