
# r

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(grades)

grades %>%
mutate(rank = dense_rank(desc(grade)) ) %>%
arrange(rank, student_name)
```


# python

```
# access datasets as pandas dataframes
import pandas as pd;

grades.head()

grades['rank'] = grades['grade'].rank(method='dense', ascending = False)
grades.loc[:,:].sort_values(by=['rank', 'student_name'], ascending=[True,True])
```



# mySQL

```
SELECT *,
  DENSE_RANK() OVER (ORDER BY grade DESC) AS rank
FROM grades
ORDER BY rank ASC, student_name ASC;
```
