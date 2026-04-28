* vertical stack/join of two datasets in R
* changing column names in R

# Question

# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(domestic_flights)

international_flights <- international_flights %>%
filter(international_zone == 'Zone A') %>%
rename(flight_id = flight_number,
       airline = carrier,
       departure_city = origin_city,
       arrival_city = destination_city,
       passenger_count = passenger_total) %>%
select(flight_id, airline, departure_city, arrival_city, passenger_count)

df <- bind_rows(domestic_flights, international_flights)

df %>%
arrange(flight_id)
```

# Python

```

```

# MySQL

```

```
