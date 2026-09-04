# Table of Contents

**lower/uppercase**

* [capitalizing first letter of a word and making others lowercases and removing symbols in words](#capitalizing-first-letter-of-a-word-and-making-others-lowercases-and-removing-symbols-in-words)
* [making all characters lowercase](#making-all-characters-lowercase)

**replacing characters**

* [replacing individual characters](#replacing-individual-characters)

**removing spaces**

* [removing start and end spaces from all columns](#removing-start-and-end-spaces-from-all-columns)
* [removing start, end, and trailing/middle spaces from select columns](#removing-start-end-and-trailingmiddle-spaces-from-select-columns)


# capitalizing first letter of a word and making others lowercases and removing symbols in words

* [table of contents](#table-of-contents)

## R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(stringr)

# access your data
head(janines_mistakes)

janines_mistakes %>%
mutate(product_name = str_to_title(product_name),
       product_name = str_replace_all(product_name, "[[:punct:]]" , '')) 

```

## Python

```
import string

# 1. Remove punctuation
janines_mistakes['product_name'] = janines_mistakes['product_name'].str.replace('…', '', regex=False).str.translate(str.maketrans("", "", string.punctuation)).str.lower().str.capitalize()
janines_mistakes
```

## MySQL and postgresSQL

* LEFT takes all the characters that are to the left of the specified index
* SUBSTRING takes all the characters that are to the right of the specified index
* `REGEXP_REPLACE(product_name, '[[:punct:]…]', '')` removes punctuation

```
SELECT *,
  CONCAT(
  UPPER(LEFT(REGEXP_REPLACE(product_name, '[[:punct:]…]', ''), 1)), 
  LOWER(SUBSTRING(REGEXP_REPLACE(product_name, '[[:punct:]…]', ''), 2))
  ) AS product_name

FROM janines_mistakes;
```

## MSSQL

```
WITH df AS (
    SELECT *,
        REPLACE(
          REPLACE(
            REPLACE(
                REPLACE(product_name, '.', ''),
                '…', ''),
            '-', ''), '!', '') AS clean_product_name
    FROM janines_mistakes
)

SELECT 
    product_id,
    CONCAT(
        UPPER(LEFT(clean_product_name, 1)),
        LOWER(SUBSTRING(clean_product_name, 2, LEN(clean_product_name)))
    ) AS product_name,
    amount_sold
FROM df;
```

## Microsoft Excel

* `PROPER()`


# making all characters lowercase

* [table of contents](#table-of-contents)

## R

* ` tolower(paste0(first_name,'.', last_name, '@gmail.com'))`

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(contacts)

left_join(contacts, people, by = 'id') %>%
mutate(email_copy = ifelse(is.na(email) | email == '',
  tolower(paste0(first_name,'.', last_name, '@gmail.com')),
  email)) %>%
select(first_name, last_name, email_copy) %>%
arrange(email_copy)
```

## Python

* `(df['first_name'] + '.' + df['last_name'] + '@gmail.com').str.lower()`

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np;

contacts.head()

df = pd.merge(
  contacts,
  people,
  how='left',
  left_on='id',
  right_on='id'
)

df['email_copy'] = np.where(df['email'].isna(),
                            (df['first_name'] + '.' + df['last_name'] + '@gmail.com').str.lower(),
                            df['email'])

df.loc[:,['first_name', 'last_name', 'email_copy']].sort_values(by='email_copy', ascending=True)
```

## MySQL, postgresSQL, and MSSQL

* `LOWER(CONCAT(first_name, '.', last_name, '@gmail.com'))`

```
SELECT first_name, last_name,
CASE WHEN (email IS NULL) THEN LOWER(CONCAT(first_name, '.', last_name, '@gmail.com')) ELSE email END AS email_copy
FROM contacts dfc
LEFT JOIN people dfp 
ON dfc.id = dfp.id
ORDER BY email_copy ASC;
```

## Microsoft Excel

* `LOWER()`

# replacing individual characters

* [table of contents](#table-of-contents)


## R

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

## Python

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

## MySQL

```
WITH df_copy AS (
SELECT *,
CAST(REPLACE(SUBSTRING_INDEX(job_salary, '-', 1), '$', '') AS FLOAT) AS starting_salary,
SUBSTRING_INDEX(job_salary, '-', -1) AS end_salary,
CASE 
  WHEN job_title LIKE '%Senior%' THEN 1
  WHEN job_title LIKE '%Lead%' THEN 1
  ELSE 0 END AS position,
CASE 
  WHEN required_skills LIKE '%SQL%' THEN 1
  WHEN required_skills LIKE '%Python%' THEN 1
  ELSE 0 END AS skill
FROM job_listings)

SELECT job_id, job_title, job_salary, required_skills
FROM df_copy
WHERE starting_salary > 85000 AND position = 1 AND skill = 1
ORDER BY job_id ASC;
```

## postgresSQL

```
WITH df_copy AS (
SELECT *,
  REPLACE(split_part(job_salary, '-', 1), '$', '')::float AS starting_salary,
  split_part(job_salary, '-', 2) AS end_salary,
CASE 
  WHEN job_title LIKE '%Senior%' THEN 1
  WHEN job_title LIKE '%Lead%' THEN 1
  ELSE 0 END AS position,
CASE 
  WHEN required_skills LIKE '%SQL%' THEN 1
  WHEN required_skills LIKE '%Python%' THEN 1
  ELSE 0 END AS skill
FROM job_listings)

SELECT job_id, job_title, job_salary, required_skills
FROM df_copy
WHERE starting_salary > 85000 AND position = 1 AND skill = 1
ORDER BY job_id ASC;
```

## MSSQL

```
WITH df_copy AS (
SELECT *,
CAST(REPLACE(PARSENAME(REPLACE(job_salary, '-', '.'), 2), '$', '') AS FLOAT) AS starting_salary,
PARSENAME(REPLACE(job_salary, '-', '.'), 1) AS end_salary,
CASE 
  WHEN job_title LIKE '%Senior%' THEN 1
  WHEN job_title LIKE '%Lead%' THEN 1
  ELSE 0 END AS position,
CASE 
  WHEN required_skills LIKE '%SQL%' THEN 1
  WHEN required_skills LIKE '%Python%' THEN 1
  ELSE 0 END AS skill
FROM job_listings)

SELECT job_id, job_title, job_salary, required_skills
FROM df_copy
WHERE starting_salary > 85000 AND position = 1 AND skill = 1
ORDER BY job_id ASC;
```

# removing start, end, and trailing/middle spaces from select columns

* [table of contents](#table-of-contents)

## R

* `str_squish(store_name)` removes leading and trailing spaces. In other words, it removes the spaces at the start of the word, end of the word, and any spaces in the middle of the word but makes sure there is at least one space in the middle of the word.
* `first(clean_column)` is useful to get the first value of a column

```
# You can load libraries like dplyr if needed
library(dplyr)
library(stringr)

# access your data
head(return_data)

return_data %>%
mutate(clean_column = str_squish(store_name), #removed leading and trailing spaces and repeating spaces within the name
       standard_column = str_replace_all(clean_column, '[[:punct:]]' ,''),
       standard_column = tolower(standard_column),
      standard_column = gsub(" ", "", standard_column, fixed = TRUE)
      ) %>%
group_by(clean_column, standard_column) %>%
summarise(totals = sum(returns),
         name_counts = n(),
         .groups = 'drop') %>%
group_by(standard_column) %>%
arrange(desc(name_counts)) %>%
mutate(clean_column = ifelse(max(name_counts) == name_counts,
                             clean_column,
                             first(clean_column) #this gets the first value in a column
                            )
      ) %>%
group_by(clean_column) %>%
summarise(totals = sum(totals)) %>%
arrange(desc(totals))
```

## Python

* `return_data['store_name'].str.replace(r"\s+", " ", regex=True)` removes the middle space
* `return_data['store_name'].str.strip())` removes the spaces at the start and end
* `return_data['store_name'].str.replace(r'[^a-zA-Z]', '', regex=True)` removes all punctuation including spaces
* `return_data['store_name'].str.lower()', '')` lowers down all characters

using group by to extract the name with the most counts

* `df.loc[:,:].sort_values(by=['standard_column', 'row_counts'] , ascending=[True, False])` orders the standard columns into one group and then sorts the row counts in desending order for each group. we are setting the base for the group by which will be used to extract the name with the most counts. 
* `df.groupby('standard_column')['row_counts'].transform('max')` gets the max number in the groupby
*  `df.groupby('standard_column')['clean_column'].transform('first')` gets the first value in the column which has been previously sorted

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np;

return_data.head()

return_data['clean_column'] = (
  return_data['store_name']
  .str.replace(r"\s+", " ", regex=True) #removes middle space
  .str.strip() #removes start and end spaces
)


return_data['standard_column'] = (
  return_data['store_name']
  .str.replace(r'[^a-zA-Z]', '', regex=True) #removes all punctuation including spaces
  .str.lower() #lowers all characters
)

df = return_data.groupby(['clean_column', 'standard_column']).agg(
  total_returns = ('returns', 'sum'),
  row_counts = ('returns', 'size')
).reset_index()

df = df.loc[:,:].sort_values(by=['standard_column', 'row_counts'] , ascending=[True, False]) #ordering the standard columns into one group then sorting the row counts 

df['clean_column'] = np.where(
  df.groupby('standard_column')['row_counts'].transform('max') == df['row_counts'], #gets the max number in the groupby and compares it
  df['clean_column'],
  df.groupby('standard_column')['clean_column'].transform('first') #gets the first value in the column (which has been previously sorted)
)

df = df.groupby('clean_column').agg(
  total_returns = ('total_returns', 'sum')
  ).reset_index()

df.loc[:,['clean_column', 'total_returns']].sort_values(by='total_returns', ascending=False)
```

## MySQL

* `TRIM()` removes spaces at the start and end of the word
* `REGEXP_REPLACE(store_name, '[[:space:]]+', '')` removes the repeated spaces in the middle
* `REGEXP_REPLACE(store_name, '[[:punct:]…]', '')` removes the punctuation
*  `MAX(total_returns) OVER (PARTITION BY standard_column)` get's the max total returns amount in each group by done by the partition
*  `FIRST_VALUE(clean_column) OVER (PARTITION BY standard_column ORDER BY counts DESC)` gets the first value in each group by done by the partition which has ordered the count values from greatest to least. This ensures that the highest value in the group by is selected

```
WITH df AS(
SELECT *,
 TRIM(
  REGEXP_REPLACE(
  store_name,
  '[[:space:]]+',
  ' ')
  ) AS clean_column,
  
 LOWER(
  TRIM(
  REGEXP_REPLACE(
  REGEXP_REPLACE(store_name, '[[:punct:]…]', ''),
  '[[:space:]]+', '')
  )) AS standard_column 
FROM return_data), 
  df2 AS( 
SELECT *,
 SUM(returns) AS total_returns,
 COUNT(*) AS counts
FROM df 
GROUP BY clean_column, standard_column),
  df3 AS( 
SELECT *,
  CASE WHEN (MAX(counts) OVER (PARTITION BY standard_column)) = counts 
  THEN clean_column 
  ELSE FIRST_VALUE(clean_column) OVER (PARTITION BY standard_column ORDER BY counts DESC) END AS official_clean_names
FROM df2
  )
  
SELECT official_clean_names,
  SUM(total_returns) AS final_total
FROM df3
GROUP BY official_clean_names
ORDER BY final_total DESC;
```

## PostgresSQL

```
WITH df AS(
SELECT *,
 TRIM(
        REGEXP_REPLACE(
            store_name,
            '[[:space:]]+',
            ' ',
            'g'
        )
  ) AS clean_column,
  
 LOWER(
  TRIM(
    REGEXP_REPLACE(
        store_name,
        '[^[:alpha:]]',
        '',
        'g'
    )
  )) AS standard_column 
FROM return_data), 
  df2 AS( 
SELECT clean_column, standard_column,
 SUM(returns) AS total_returns,
 COUNT(*) AS counts
FROM df 
GROUP BY clean_column, standard_column),
  df3 AS( 
SELECT *,
  CASE WHEN counts = (MAX(counts) OVER (PARTITION BY standard_column))  
  THEN clean_column 
  ELSE FIRST_VALUE(clean_column) OVER (PARTITION BY standard_column ORDER BY counts DESC) END AS official_clean_names
FROM df2
  )
  
SELECT official_clean_names,
  SUM(total_returns) AS final_total
FROM df3
GROUP BY official_clean_names
ORDER BY final_total DESC;
```

## MSSQL

```

```

# removing start and end spaces from all columns

* [table of contents](#table-of-contents)

## R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(tidyr)

# access your data
head(addresses)

addresses %>%
separate_wider_delim(
  cols = address,
  delim = '-',
  names = c('street', 'city', 'state', 'zip_code')) %>%
  mutate(
    across(everything(), trimws)
  )
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

addresses.head()

addresses[['street', 'city', 'state', 'zip_code']] = (
    addresses['address'].str.split('-', expand=True)
    .apply(lambda col: col.str.strip())
)

addresses.loc[:,['street', 'city', 'state', 'zip_code']]
```

## MySQL

```
SELECT
    TRIM(SUBSTRING_INDEX(address, '-', 1)) AS street,
    TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(address, '-', 2), '-', -1)) AS city,
    TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(address, '-', 3), '-', -1)) AS state,
    TRIM(SUBSTRING_INDEX(address, '-', -1)) AS zip_code
FROM addresses;
```

## PostgresSQL

```
SELECT
    TRIM(SPLIT_PART(address, '-', 1)) AS street,
    TRIM(SPLIT_PART(address, '-', 2)) AS city,
    TRIM(SPLIT_PART(address, '-', 3)) AS state,
    TRIM(SPLIT_PART(address, '-', 4)) AS zip_code
FROM addresses;
```

## MSSQL

```
SELECT
    LTRIM(RTRIM(PARSENAME(REPLACE(address, '-', '.'), 4))) AS street,
    LTRIM(RTRIM(PARSENAME(REPLACE(address, '-', '.'), 3))) AS city,
    LTRIM(RTRIM(PARSENAME(REPLACE(address, '-', '.'), 2))) AS state,
    LTRIM(RTRIM(PARSENAME(REPLACE(address, '-', '.'), 1))) AS zip_code
FROM addresses;
```

## Microsoft Excel

* `TRIM()`
