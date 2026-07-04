# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(client_assignments)

client_assignments <- client_assignments %>% select(-assignment_date)

df <- left_join(client_assignments, client_assignments, by=('client_id' = 'client_id'))

# recruiter_name.x > recruiter_name.y is TRUE when string x comes after string y 
df %>%
mutate(
  recruiter_a = ifelse(recruiter_name.x > recruiter_name.y, recruiter_name.y, recruiter_name.x),
  recruiter_b = ifelse(recruiter_name.x > recruiter_name.y, recruiter_name.x, recruiter_name.y),
  delete_row = ifelse(recruiter_name.x == recruiter_name.y,1,0)) %>%
filter(delete_row != 1) %>% #to pick values that aren't equal to 1
distinct(client_id, recruiter_a, recruiter_b) %>% #removing repeating rows
group_by(recruiter_a, recruiter_b) %>%
summarise(count = n()) %>%
arrange(desc(count), recruiter_a, recruiter_b)
```

# Python

```

```

# MySQL

```

```
