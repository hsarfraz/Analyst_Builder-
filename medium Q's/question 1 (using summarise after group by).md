* sorting column values in R, Python, MySQL in descending order (Z-A)

# Question

James is a Help Desk Manager in their IT Department. He wants to know the resolution rates for each of his employees.

Each call the help desk receives is either marked as "Y" for resolved or "N" for not resolved.

Calculate each employees percentage of calls resolved compared to all their calls.

Output the name of the employee and their resolution rate. Order on their name alphabetically.

# R 

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(help_desk)

help_desk %>%
mutate(call_outcome = ifelse(call_outcome == 'Y', 1,0)) %>%
group_by(employee_name) %>%
summarise(percentage = mean(call_outcome) * 100) %>%
arrange(employee_name)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;
import numpy as np

help_desk.head()

help_desk['call_outcome'] = np.where(help_desk['call_outcome'] ==  'Y', 1, 0)
(help_desk.groupby('employee_name')['call_outcome'].mean() * 100).reset_index().sort_values(by='employee_name', ascending=True)
```

MySQL

```
SELECT employee_name,
  (SUM(CASE WHEN call_outcome == 'Y' THEN 1 ELSE 0 END)/COUNT(*))*100 AS call_proportion
FROM help_desk
GROUP BY employee_name
ORDER BY employee_name ASC;
```
