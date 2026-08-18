# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(facebook)
library(stringr)

facebook %>%
mutate(christmas = ifelse(str_detect(dates, '12/25'), 1, 0)) %>%
filter(christmas == 1) %>%
group_by(actions) %>%
summarise(actions_count = n()) %>%
mutate(ranks = min_rank(desc(actions_count))) %>%
arrange(ranks, actions)
```

# Python

```

```

# MYSQL

```

```
