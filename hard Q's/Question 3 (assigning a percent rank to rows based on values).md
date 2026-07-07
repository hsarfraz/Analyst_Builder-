# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(marketing_spend)

marketing_spend %>%
mutate(
  ROI = round(((revenue_generated - investment)/investment) * 100, digits = 0),
rank = percent_rank(ROI)*100
) %>%
arrange(desc(ROI), desc(campaign_id)) %>%
filter(rank <= 100 & rank >=75) %>%
select(campaign_id, campaign_name, ROI)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;

marketing_spend.head()

marketing_spend['ROI'] = ((marketing_spend['revenue_generated'] - marketing_spend['investment']) / marketing_spend['investment']) * 100
marketing_spend['ROI'] = marketing_spend['ROI'].round()
marketing_spend['rank'] = (marketing_spend['ROI'].rank(pct=True) * 100)
marketing_spend.loc[ (marketing_spend['rank'] <= 100) & (marketing_spend['rank'] >= 75) ,['campaign_id', 'campaign_name', 'ROI']].sort_values(by=['ROI', 'campaign_id'], ascending = [False, False])


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
