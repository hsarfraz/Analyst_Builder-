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

```

# MySQL

```

```
