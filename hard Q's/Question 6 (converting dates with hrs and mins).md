# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(isp_outages)

isp_outages$start_time_format <- as.POSIXct(isp_outages$start_time, format='%m/%d/%Y %H:%M')
isp_outages$end_time_format <- as.POSIXct(isp_outages$end_time, format='%m/%d/%Y %H:%M')

isp_outages %>%
mutate(time_difference = (end_time_format - start_time_format)*60,
       ongoing_outage = ifelse(is.na(end_time_format),1,0)) %>%
group_by(isp_name) %>%
summarise(average_outage_duration = mean(time_difference, na.rm=TRUE),
          ongoing_outages = sum(ongoing_outage)) %>%
arrange(desc(average_outage_duration)) 
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np

isp_outages.head()

isp_outages['start_time'] = pd.to_datetime(isp_outages['start_time'], format='%m/%d/%Y %H:%M')
isp_outages['end_time'] = pd.to_datetime(isp_outages['end_time'], format='%m/%d/%Y %H:%M')
isp_outages['time_difference'] = (isp_outages['end_time'] - isp_outages['start_time']).dt.total_seconds() / 60
isp_outages['outgoing_outage'] = np.where(isp_outages['end_time'].isna(), 1, 0)
isp_outages
isp_outages.groupby('isp_name').agg(
  average_outage_duration = ('time_difference', 'mean'),
  ongoing_outages = ('outgoing_outage', 'sum')
).reset_index().sort_values(by='average_outage_duration', ascending=False)
```

# MySQL

```
SELECT isp_name,
 AVG(TIMESTAMPDIFF(MINUTE,
  STR_TO_DATE(start_time, '%m/%d/%Y %H:%i'),
  STR_TO_DATE(end_time, '%m/%d/%Y %H:%i')
  )) AS average_outage_duration,
  SUM(CASE WHEN end_time IS NULL THEN 1 ELSE 0 END) AS ongoing_outages
FROM isp_outages
GROUP BY isp_name
ORDER BY average_outage_duration DESC;
```

# PostgresSQL

```
SELECT isp_name,
 AVG(
  EXTRACT( EPOCH from (end_time::timestamp - start_time::timestamp)) / 60
  ) AS average_outage_duration,
  SUM(CASE WHEN end_time IS NULL THEN 1 ELSE 0 END) AS ongoing_outages
FROM isp_outages
GROUP BY isp_name
ORDER BY average_outage_duration DESC;
```

# MSSQL

```
SELECT isp_name,
 AVG(
  DATEDIFF( MINUTE, CAST(start_time AS DATETIME), CAST(end_time AS DATETIME) )
  ) AS average_outage_duration,
  SUM(CASE WHEN end_time IS NULL THEN 1 ELSE 0 END) AS ongoing_outages
FROM isp_outages
GROUP BY isp_name
ORDER BY average_outage_duration DESC;
```

