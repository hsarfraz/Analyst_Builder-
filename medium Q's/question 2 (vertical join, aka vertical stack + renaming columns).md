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
# access datasets as pandas dataframes
import pandas as pd;

domestic_flights.head()

international_flights = international_flights.loc[ international_flights['international_zone'] == 'Zone A' , ['flight_number', 'carrier', 'origin_city', 'destination_city', 'passenger_total']]
international_flights = international_flights.rename(columns = {'flight_number' : 'flight_id',
                                       'carrier' : 'airline',
                                       'origin_city' : 'departure_city',
                                       'destination_city' : 'arrival_city',
                                       'passenger_total' : 'passenger_count'})

pd.concat([domestic_flights, international_flights]).sort_values(by='flight_id', ascending = True)
```

# MySQL

```
SELECT *
FROM ( 
SELECT flight_number AS flight_id,
  carrier AS airline,
  origin_city AS departure_city,
  destination_city AS arrival_city,
  passenger_total AS passenger_count
FROM international_flights
WHERE international_zone = 'Zone A'

UNION ALL

SELECT *
FROM domestic_flights
  ) AS vertical_join
ORDER BY flight_id ASC
```
