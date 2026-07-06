# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(marketing_spend)

marketing_spend %>%
mutate(ROI = round(((revenue_generated - investment)/investment) * 100, digits = 0) ) %>%
select(campaign_id, campaign_name, ROI) %>%
arrange(desc(ROI), desc(campaign_id)) %>%
head((nrow(marketing_spend) * 0.25))
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;

marketing_spend.head()

rows = (len(marketing_spend) * 0.25)

marketing_spend['ROI'] = ((marketing_spend['revenue_generated'] - marketing_spend['investment']) / marketing_spend['investment']) * 100
marketing_spend['ROI'] = marketing_spend['ROI'].round()
marketing_spend.loc[:,['campaign_id', 'campaign_name', 'ROI']].sort_values(by=['ROI', 'campaign_id'], ascending = [False, False]).head(int(rows))

```

# MySQL

```
WITH df AS (SELECT campaign_id,
  campaign_name,
  ROUND(((revenue_generated - investment)/investment)*100, 0) AS ROI,
  PERCENT_RANK() OVER (ORDER BY (revenue_generated - investment)/investment)*100 as pct_rank
FROM marketing_spend
ORDER BY ROI DESC, campaign_id DESC)
  
SELECT campaign_id,
  campaign_name,
  ROI 
FROM df
WHERE pct_rank <= 100 AND pct_rank >= 75;
```
