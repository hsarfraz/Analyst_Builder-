# Table of Contents

* [capitalizing first letter of a word and making others lowercases and removing symbols in words](#capitalizing-first-letter-of-a-word-and-making-others-lowercases-and-removing-symbols-in-words)
* [making all characters lowercase](#making-all-characters-lowercase)


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

```

```

## MySQL

```

```
