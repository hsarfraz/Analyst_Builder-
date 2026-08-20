# R

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

# Python

```
import string

# 1. Remove punctuation
janines_mistakes['product_name'] = janines_mistakes['product_name'].str.replace('…', '', regex=False).str.translate(str.maketrans("", "", string.punctuation)).str.lower().str.capitalize()
janines_mistakes
```

# MySQL

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
