# question

# R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(stringr)

# access your data
head(direct_reports)

#the first step is to create a dataframe with the employee id's of the managers
manager_eomployee_id_df <- direct_reports %>%
filter(str_detect(position, 'Manager')) %>%
rename(employee_id_of_manager = employee_id) %>%
select(employee_id_of_manager, position)
manager_eomployee_id_df

#you can use either a left or right join, but just keep in mind the order of the dfs
left_join(manager_eomployee_id_df, direct_reports, by = c("employee_id_of_manager" = "managers_id")) %>%
group_by(employee_id_of_manager, position.x) %>%
summarise(count = n())
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
