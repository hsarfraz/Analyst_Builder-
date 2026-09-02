# Table of Contents

* [capitalizing first letter of a word and making others lowercases and removing symbols in words](#capitalizing-first-letter-of-a-word-and-making-others-lowercases-and-removing-symbols-in-words)
* [making all characters lowercase](#making-all-characters-lowercase)
* [replacing individual characters]()

<b><u>removing spaces</u></b>

* [removing start and end spaces from all columns](#removing-start-and-end-spaces-from-all-columns)


## capitalizing first letter of a word and making others lowercases and removing symbols in words

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

## MySQL

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

## making all characters lowercase

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

## MySQL

* `LOWER(CONCAT(first_name, '.', last_name, '@gmail.com'))`

```
SELECT first_name, last_name,
CASE WHEN (email IS NULL) THEN LOWER(CONCAT(first_name, '.', last_name, '@gmail.com')) ELSE email END AS email_copy
FROM contacts dfc
LEFT JOIN people dfp 
ON dfc.id = dfp.id
ORDER BY email_copy ASC;
```

## removing start and end spaces from all columns

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
