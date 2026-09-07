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

a horizontal join with many dataframes

```
WITH customers_df AS (
  SELECT *
  FROM customers
),

movienames_df AS (
  SELECT *
  FROM movienames
),

movie_counts_per_customer AS (SELECT c.name,
  mn.movie_name,
  COUNT(*) AS total_movie_watches
FROM date_viewed dv
LEFT JOIN customers_df c
  ON dv.customer_id = c.customer_id
LEFT JOIN movienames_df mn 
  ON dv.movie_id = mn.movie_id
GROUP BY c.name, mn.movie_name)

SELECT name
FROM movie_counts_per_customer
GROUP BY name
ORDER BY SUM(total_movie_watches) DESC
LIMIT 1
;

```

# excel

* `=VLOOKUP(B6,Catalogue!A1:C85,2, FALSE)`
* **first argument**: select the id number of the original dataset where you want to perform the join
* **second argument**: now go to the other table which contains all the information that you want to extract and select the whole table. To select the whole table select all the table columns and then press `shift + ctrl + down arrow`
* **third argument**: Now you need to specify the column that you are referencing from the other table that we just selected in the second argument. excel numbers columns starting from 1 so count the very first column (from left to right) will be given the number 1 and as you move to the right the numbering increases. Don't select the table headings here
* **fourth argument**: select false to get the exact matches
* `=IFNA(VLOOKUP(B6,Catalogue!A1:C85,2), "not found")` use this to deal with NA values in a VLOOKUP

some limitations of VLOOKUP

* Duplication: If the secondary table has multiple matches for a single key, a SQL Left Join duplicates the original row to show every match. VLOOKUP only stops at the first match it finds and ignores the rest.
* Column Direction: VLOOKUP strictly requires the matching key to be in the very first column of your lookup array. A SQL Left Join can link tables using columns in any position.

**Index functions: when you need to specify columns and headers to extract values**

* `=INDEX('Financials USA'!$E$7:$N$19,MATCH(CONCAT('Input Data'!D2,'Input Data'!C2),'Financials USA'!$A$7:$A$19,0),MATCH('Input Data'!B2,'Financials USA'!$E$1:$N$1,0))`
* **first argument**: pick the dataset values that you want to extract information from. You don't have to include rows and column labels here. It is like the vlookup array where you just highlight the values and select those
*  **second argument**: `MATCH(CONCAT('Input Data'!D2,'Input Data'!C2),'Financials USA'!$A$7:$A$19,0)` the match function is used to give a number to index/identifier columns that will be used to identify the rows in the table specified in the first argument. now the first argument in the match function is the index key you want to create in your current dataset. Then the second argument is where you specify the index column from the other dataset. You do not include the column heading when specifying the array. The 3rd argument in the match function is specifying whether you want a exact match so you select zero for that.
*  **third argument**: `MATCH('Input Data'!B2,'Financials USA'!$E$1:$N$1,0)`
