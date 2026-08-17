# R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(tidyr)
library(stringr)

# access your data
head(job_listings)

job_listings['job_salary_copy'] <- job_listings['job_salary']

job_listings %>%
separate_wider_delim(cols = job_salary_copy, 
                     delim = "-", 
                     names = c("start_range", "end_range")) %>%
mutate(position = ifelse(str_detect(job_title, 'Senior'), 1, 0),
       position = ifelse( str_detect(job_title, 'Lead') ,1 , position),
       skills = ifelse(str_detect(required_skills, 'SQL') & str_detect(required_skills, 'Python'), 1, 0),
       start_range = as.numeric(gsub("$", "", start_range, fixed = TRUE))
       ) %>%
filter(start_range >= 85000,
position == 1,
skills == 1) %>%
select(-c(position, skills, start_range, end_range)) %>%
arrange(as.numeric(job_id))
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np

job_listings.head()

job_listings['job_id'] = pd.to_numeric(job_listings['job_id'])

job_listings['job_salary_copy']  = job_listings['job_salary']
job_listings[['start_salary', 'end_salary']] = job_listings['job_salary_copy'].str.split('-', expand=True)
job_listings['start_salary'] = job_listings['start_salary'].str.replace('$', '', regex=False)
job_listings['start_salary'] = pd.to_numeric(job_listings['start_salary'])

job_listings['position'] = np.where(job_listings['job_title'].str.contains('Senior') == True, 1, 0)
job_listings['position'] = np.where(job_listings['job_title'].str.contains('Lead') == True, 1, job_listings['position'])

job_listings['skills'] = np.where(job_listings['required_skills'].str.contains('SQL') &
                                 job_listings['required_skills'].str.contains('Python'), 1, 0)

job_listings = job_listings.loc[ (job_listings['start_salary'] >= 85000) & 
  (job_listings['position'] == 1) & 
  (job_listings['skills'] == 1) , ].sort_values(by= 'job_id', ascending= True)
job_listings = job_listings.drop(['start_salary', 'end_salary', 'position', 'skills', 'job_salary_copy'], axis=1)
job_listings
```

# MySQL

```

```
