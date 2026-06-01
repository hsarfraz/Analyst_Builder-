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
# access datasets as pandas dataframes
import pandas as pd
import numpy as np

direct_reports.head()

direct_reports['manager_position'] = np.where(direct_reports['position'].str.contains('Manager') == True, 1, 0)
df_of_manager_employee_id = direct_reports.loc[direct_reports['manager_position'] == 1, ['employee_id', 'position']].rename(columns = {'employee_id' : 'employee_id_of_manager'})
pd.merge(
  df_of_manager_employee_id,
  direct_reports,
  how='left',
  left_on='employee_id_of_manager',
  right_on='managers_id'
).groupby(['employee_id_of_manager', 'position_x'])['employee_id'].count().reset_index()
```

# MySQL

```
WITH df_of_manager_employee_id AS( 
  SELECT position,
  employee_id AS managers_employee_id
FROM direct_reports
WHERE position LIKE '%Manager%')

SELECT df.managers_employee_id, df.position,
  COUNT(*) AS COUNT
FROM df_of_manager_employee_id df
LEFT JOIN direct_reports dr 
  ON  df.managers_employee_id = dr.managers_id 
GROUP BY df.managers_employee_id, df.position;
```
