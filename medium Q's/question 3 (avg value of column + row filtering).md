# Question

# R

```
heights %>%
filter(average_height > mean(average_height)) %>%
arrange(desc(average_height))
```

# Python

```
avg_height_mean = heights['average_height'].mean()

heights.loc[ (heights['average_height'] > avg_height_mean) , :].sort_values(by="average_height", ascending=False)
```

# MySQL

```
SELECT *
FROM heights
WHERE average_height > (SELECT AVG(average_height) FROM heights)
ORDER BY average_height DESC
```
