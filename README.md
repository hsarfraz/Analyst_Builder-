# Foundational-Data-Analytics-Techniques

Order of writing SQL queries

Select, From, Join, Where, Group By, Having, Order By, Limit

SQL order of execution

1. From & Join
2. Where
3. Group By
4. Having
5. Select
6. Order By
7. Limit

```
# reading a .csv file
library(readr) #library needed
setwd("C:/Users/hussainsarfraz/Downloads") #setting the working directory 
alabama.court <-  read.csv('AlabamaCourt.csv') #function to read the file
```

1. Creating a new column + adding values to the new column based on conditions of another column [click here](https://github.com/hsarfraz/Foundational-Data-Analytics-Techniques/blob/main/number1.md)
2. Grouping by column values + getting distinct/unique counts [click here](https://github.com/hsarfraz/Foundational-Data-Analytics-Techniques/blob/main/number%202.md)
3. Cartesian product / cross join [click here](https://github.com/hsarfraz/Foundational-Data-Analytics-Techniques/blob/main/number%203.md)
4. PostgresSQL and MSSQL data types, converting to float [click here](https://github.com/hsarfraz/Foundational-Data-Analytics-Techniques/blob/main/number%204.md)
5. Converting Date columns to DateTime object + sorting them [click here](https://github.com/hsarfraz/Foundational-Data-Analytics-Techniques/blob/main/number%205.md)
6. Vertial Join of two datasets
7. Filtering rows of a column is it contains a certain string
9. Using 2 group bys and performing 2 aggregates or sum and mean
10. Full join of two dataset where the column has to be re-named to make the join happen
