



# Data wrangling {#wrangle}

## Intended Learning Outcomes {#ilo-wrangle}

By the end of this chapter you should be able to:

* Select and filter data for relevance
* Create new columns and edit existing ones
* Handle missing data

## Walkthrough video {#walkthrough-wrangle}

We encourage you to read the workbook and attempt each step on your own before watching the video as this will help consolidate your learning (it may feel harder but making mistakes is informative and will help you learn more in the long-run).

<iframe width="560" height="315" src="https://www.youtube.com/embed/8vvLmz6sCFQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Set-up {#setup-wrangle}

Create a new project for this course and then do the following:

* Create and save a new R Markdown document named `chapter_2.Rmd`, get rid of the default template text from line 11 onwards.
* Add the below code to the set-up chunk and then run the code to load the packages and data.You may need to install the packages if you don't have them installed already.
* Download the [Data transformation cheat sheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-transformation.pdf)


```r
library(tidyverse)   
library(medicaldata)
data("opt")
data("polyps")
```

## Wrangling functions

Data wrangling refers to the process of cleaning, transforming, and restructuring your data to get it into the format you need for analysis and it's something you will spend an awful lot of time doing. It's worth highlighting that in this chapter we're going to cover  common functions and common uses of said functions. However, <code class='package'>dplyr</code> (and packages beyond it) has a huge number of additional wrangling functions and each function has many different arguments. Essentially, if you think you should be able to wrangle your data in a particular way that we haven't explicitly shown you, you almost certainly can, it might just take a bit of Googling to find out how. 

We're going to use the `polyps` and `opt` datasets. You can learn more about these datasets by using the help function:


```r
?medicaldata::opt
?medicaldata::polyps
```

### Select

You can select a subset of the columns (variables) in a table to make it easier to view or to prepare a table for display. You can also select columns in a new order.

#### By name or index

You can select columns by name or number (which is sometimes referred to as the column index). Selecting by number can be useful when the column names are long or complicated. The `opt` dataset has a huge number of variables (171 in total) and it's likely that you might not need all of these.


```r
# select columns by name
opt_select <- opt %>% select(Clinic, Age, Education) 

# select columns by number
opt_select <- opt %>% select(2, 3, 10) 
```

You can select each column individually, separated by commas but you can also select all columns from one to another by separating them with a colon. The colon notation can be much faster because you don't need to type out each individual variable name, but make sure that you know what order your columns are in and always check the output to make sure you have selected what you intended.


```r
# select columns individually
opt_multiple <- opt %>% select(PID, Clinic, Group, Age)

# select columns with colon
opt_multiple <- opt %>% select(PID:Age)
```

You can rename columns at the same time as selecting them by setting `new_name = old_col`. 


```r
opt_rename <- opt %>% select(PID, Location = Clinic, Black:Hisp)

head(opt_rename, 2)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> PID </th>
   <th style="text-align:left;"> Location </th>
   <th style="text-align:left;"> Black </th>
   <th style="text-align:left;"> White </th>
   <th style="text-align:left;"> Nat.Am </th>
   <th style="text-align:left;"> Asian </th>
   <th style="text-align:left;"> Hisp </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 100034 </td>
   <td style="text-align:left;"> NY </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 100042 </td>
   <td style="text-align:left;"> NY </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;">  </td>
  </tr>
</tbody>
</table>

</div>

#### De-selecting columns

You can select columns either by telling R which ones you want to keep as in the previous examples, or by specifying which ones you want to exclude by using a minus symbol to de-select columns. You can also use the colon notation to de-select columns, but to do so you need to put parentheses around the span first, e.g., `-(Black:Hisp)`, not `-Black:Hisp`.


```r
# de-select individual columns
opt_deselect <- opt_rename %>% select(-Location)

# de-select a range of columns
opt_deselect2 <- opt_rename %>% select(-(Black:Hisp))
```

#### Select helpers

Finally, you can select columns based on criteria about the column names, for example:

| function | definition |
|----------|------------|
| `starts_with()` | select columns that start with a character string|
| `ends_with()` | select columns that end with a character string |
| `contains()` | select columns that contain a character string |


### Filter

Whilst `select()` chooses the columns you want to retain, `filter()` chooses the rows to retain by matching row or column criteria.

You can filter by a single criterion. This criterion can be rows where a certain column's value matches a character value (e.g., "NY") or a number (e.g., 20). It can also be the result of a logical equation (e.g., keep all rows with a specific column value larger than a certain value). The criterion is checked for each row, and if the result is FALSE, the row is removed. You can reverse equations by specifying `!=` where `!` means "not".


```r
# select all rows where clinic equals NY
opt %>% filter(Clinic == "NY")

# select all rows where Age is exactly equal to 20
opt %>% filter(Age == 20)

# select all rows where Age was more than 20
opt %>% filter(Age > 20)

# everything but NY
opt %>% filter(Clinic != "NY")
```

::: {.warning data-latex=""}
Remember to use `==` and not `=` to check if two things are equivalent. A single `=` assigns the right-hand value to the left-hand variable (much like the `<-` operator).
:::





::: {.try data-latex=""}
Which IDs are kept from the table below?

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> id </th>
   <th style="text-align:left;"> grade </th>
   <th style="text-align:right;"> score </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> A </td>
   <td style="text-align:right;"> 95 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> A </td>
   <td style="text-align:right;"> 91 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> C </td>
   <td style="text-align:right;"> 76 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> B </td>
   <td style="text-align:right;"> 84 </td>
  </tr>
</tbody>
</table>

</div>

* `demo %>% filter(score < 80)`
    <select class='webex-select'><option value='blank'></option><option value='x'>1, 2</option><option value='x'>2</option><option value='answer'>3</option><option value='x'>3, 4</option></select>
* `demo %>% filter(grade == "A")`
    <select class='webex-select'><option value='blank'></option><option value='answer'>1, 2</option><option value='x'>2</option><option value='x'>3</option><option value='x'>3, 4</option></select>
* `demo %>% filter(grade != "A")`
    <select class='webex-select'><option value='blank'></option><option value='x'>1, 2</option><option value='x'>2</option><option value='x'>3</option><option value='answer'>3, 4</option></select>
* `demo %>% filter(score == 91)`
    <select class='webex-select'><option value='blank'></option><option value='x'>1, 2</option><option value='answer'>2</option><option value='x'>3</option><option value='x'>3, 4</option></select>
    

:::

You can also select on multiple criteria by separating them by commas (rows will be kept if they match *all* criteria). Additionally, you can use `&` ("and") and `|` ("or") to create complex criteria.


```r
# patients with a BMI equal to or above 30 AND who have diabetes
bmi_diabetes <- opt %>% 
  filter(
    Diabetes == "Yes",
    BMI >= 30
  )

# the same as above, using & instead of a comma
bmi_diabetes <- opt %>% 
  filter(
    Diabetes == "Yes" &
    BMI >= 30
  )

# patients with a BMI above or equal to 30 OR who have diabetes
diabetes_either <- opt %>% 
  filter(
    Diabetes == "Yes" |
    BMI >= 30
  )
```

If you want the filter to retain multiple specific values in the same variable, the <a class='glossary' target='_blank' title='A binary operator (%in%) that returns a logical vector indicating if there is a match or not for its left operand.' href='https://psyteachr.github.io/glossary/m#match-operator'>match operator</a> (`%in%`) should be used rather than `|` (or). The `!` can also be used in combination here, but it is placed before the variable name.


```r
# retain any rows where Clinic is NY or MN or KY, and where Black equals yes
opt %>%
  filter(Clinic %in% c("NY", "MN", "KY"),
         Black == "Yes")

# retain any rows where the region is not NY or MN or KY, and where Black does not equal Yes
opt %>%
  filter(!Clinic %in% c("NY", "MN", "KY"),
         Black != "Yes")
```

<a class='glossary' target='_blank' title='A symbol that performs some mathematical or comparative process. ' href='https://psyteachr.github.io/glossary/o#operator'>Operator</a>	|Name   |is TRUE if and only if
-----------|----------------------|---------------------------------
`A < B`    |less than 	          |A is less than B
`A <= B`   |less than or equal    |A is less than or equal to B
`A > B`    |greater than 	        |A is greater than B
`A >= B`   |greater than or equal |A is greater than or equal to B
`A == B`   |equivalence 	        |A exactly equals B
`A != B`   |not equal 	          |A does not exactly equal B
`A %in% B` |in 	                  |A is an element of vector B

::: {.warning data-latex=""}
`filter()` is incredibly powerful and can allow you to select very specific subsets of data. But, it is also quite dangerous because when you start combining multiple criteria and operators, it's very easy to accidentally specify something slightly different than what you intended. **Always check your output**. If you have a small dataset, then you can eyeball it to see if it looks right. With a larger dataset, you may wish to compute summary statistics or count the number of groups/observations in each variable to verify your filter is correct. There is no level of expertise in coding that can substitute knowing and checking your data. 
:::

### Arrange

You can sort your dataset using `arrange()`. You will find yourself needing to sort data in R much less than you do in Excel, since you don't need to have rows next to each other in order to, for example, calculate group means. But `arrange()` can be useful when preparing data for display in tables. `arrange()` works on character data where it will sort alphabetically, as well as numeric data where the default is ascending order (smallest to largest). Reverse the order using `desc()`.


```r
# arranging the table 
# first by sex in alphabetical order
# then by "baseline" in descending order
polyps %>%
  arrange(sex, desc(baseline)) 
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> participant_id </th>
   <th style="text-align:left;"> sex </th>
   <th style="text-align:right;"> age </th>
   <th style="text-align:right;"> baseline </th>
   <th style="text-align:left;"> treatment </th>
   <th style="text-align:right;"> number3m </th>
   <th style="text-align:right;"> number12m </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 002 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 77 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 67 </td>
   <td style="text-align:right;"> 63 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 017 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 54 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 45 </td>
   <td style="text-align:right;"> 46 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 006 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 35 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 31 </td>
   <td style="text-align:right;"> 61 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 021 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 007 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 23 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 020 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 23 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 012 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 23 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 001 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 17 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> NA </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 004 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 28 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 010 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 19 </td>
   <td style="text-align:right;"> 318 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 347 </td>
   <td style="text-align:right;"> 44 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 011 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 17 </td>
   <td style="text-align:right;"> 160 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 142 </td>
   <td style="text-align:right;"> 25 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 016 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 23 </td>
   <td style="text-align:right;"> 34 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 27 </td>
   <td style="text-align:right;"> 33 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 019 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 34 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:right;"> 50 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 015 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 27 </td>
   <td style="text-align:right;"> 24 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 26 </td>
   <td style="text-align:right;"> 40 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 005 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 23 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:right;"> 17 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 013 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:right;"> 28 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 018 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> NA </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 008 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 34 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 022 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 42 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 014 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 10 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 003 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:left;"> sulindac </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 009 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 50 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:left;"> placebo </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 15 </td>
  </tr>
</tbody>
</table>

</div>


### Mutate

The function `mutate()` allows you to add new columns or change existing ones by overwriting them by using the syntax `new_column = operation`.  You can add more than one column in the same mutate function by separating the columns with a comma. Once you make a new column, you can use it in further column definitions. 

For example, the `polyps` dataset contains data on the number of polyps at baseline, after 3 months of treatment, and then after 12 months of treatment. We can create `treatment1` and `treatment2` to tell us the improvement in the number of polyps between these two treatment milestones (a negative number would be a good thing, as it means fewer polyps than at baseline), and then use the new variables to create `total`.

If you look at the dataset, you'll see that `total` contains some `NAs` (missing values), we'll come back to this soon.


```r
polyps2 <- polyps %>%
  mutate(
    treatment1 = number3m - baseline ,
    treatment2 = number12m - number3m,
    total = treatment1 + treatment2,
    treatment  = paste(treatment , "condition")
  )
```

`mutate()` can also be used in conjunction with other functions and Boolean operators. For example, we can add another column to `polyps2` that states whether any improvement in the number of polyps was seen or overwrite our `treatment` variable as a factor. Just like when we used <a class='glossary' target='_blank' title='An expression that evaluates to TRUE or FALSE.' href='https://psyteachr.github.io/glossary/b#boolean-expression'>Boolean expressions</a> with filter, it will evaluate the equation and return TRUE or FALSE depending on whether the observation meets the criteria.


```r
polyps2 <- polyps2 %>%
  mutate(improvement = total < 0,
         treatment = as.factor(treatment))
```

::: {.warning data-latex=""}
You can overwrite a column by giving a new column the same name as the old column (see `treatment`) above. Make sure that you mean to do this and that you aren't trying to use the old column value after you redefine it.
:::

You can also use `case_when()` to specify what values to return, rather than defaulting to TRUE or FALSE:


```r
polyps3 <- polyps2 %>%
  mutate(improvement = case_when(total > 0 ~ "Decline",
                                 total == 0 ~ "No change",
                                 total < 0 ~ "Improvement"))
```

Use it to recode values:


```r
# create a column of categories depending on number of polyps

polyps4 <- polyps3 %>%
  mutate(category = case_when(baseline <= 10 ~ "Low",
                              baseline > 10 & baseline <= 30 ~ "Medium",
                              baseline > 30 ~ "High"))
```

And combine different criteria:


```r
# patients are categorized as high if they have a high number of polyps if they are male and under 25

polyps5 <- polyps4 %>%
  mutate(risk = case_when(category == "High" ~ "High risk",
                          sex == "Male" & age < 25 ~ "High risk",
                          TRUE ~ "Not high risk")) # set all other values to "no bonus"
```

Just like `filter()`, `mutate()` is incredibly powerful and the scope of what you can create is far beyond what we can cover in this book. 

## Descriptive statistics

### Count

A useful and simple function is `count()`. `count()` will return the number of unique values of one or more variables - it's most likely you'd use this with a categorical/factor variable but it will also work with numeric data. You can count the total number of observations in a dataset:


```r
# total number of observations (rows) in the dataset
polyps5 %>%
  count()
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> n </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 22 </td>
  </tr>
</tbody>
</table>

</div>

Or the number of observations by a grouping variable:


```r
# total number in each treatment condition
polyps5 %>%
  count(treatment)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> treatment </th>
   <th style="text-align:right;"> n </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:right;"> 11 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:right;"> 11 </td>
  </tr>
</tbody>
</table>

</div>

You can also use count with multiple grouping variables. For example, we could look at the number of male and female participants (it's not specified but I assume this is sex assigned at birth) in each treatment condition. Note that the order you specify the variables will affect the layout of the resulting table (I can never visualize which order I need to specify the variables in - my best advice is to run the code, look at the output, and edit if needed).

This code produces the number of male and female patients in each treatment


```r
polyps5 %>%
  count(treatment, sex)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> treatment </th>
   <th style="text-align:left;"> sex </th>
   <th style="text-align:right;"> n </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 6 </td>
  </tr>
</tbody>
</table>

</div>

Whilst this code produced the number in each treatment condition by sex assigned at birth:


```r
polyps5 %>%
  count(sex, treatment)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> sex </th>
   <th style="text-align:left;"> treatment </th>
   <th style="text-align:right;"> n </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> female </td>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> female </td>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> male </td>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:right;"> 7 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> male </td>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:right;"> 6 </td>
  </tr>
</tbody>
</table>

</div>

### Summarise {#dplyr-summarise}

`summarise()` applies summary functions to an entire table (or groups, as you'll see in the next section). However, before we go much further we will need to deal with those pesky missing values.

Let's say we want to determine the mean, median, min and max number of polyps after treatment. The function `summarise()` allows us to create a table where the column names will be the left-hand value (.e.g, `mean_polyps`) and the values will be the result of the operation on the right-hand side (e.g., take the `mean()` of the variable `total`).

The problem is that our dataset has a few missing values which produces a table full of `NA`s:


```r
polyps5 %>%
  summarise(
    mean_polyps = mean(total),
    median_polyps = median(total),
    min_polyps = min(total),
    max_polyps = max(total)
  )
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> mean_polyps </th>
   <th style="text-align:right;"> median_polyps </th>
   <th style="text-align:right;"> min_polyps </th>
   <th style="text-align:right;"> max_polyps </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> NA </td>
  </tr>
</tbody>
</table>

</div>

Whilst it can seem unintuitive that R won't return a number, it makes logical sense - the average of 100 and "I don't know" isn't 100, it's "I don't know". There's a few ways we can deal with this and this is where understanding your data and knowing what is missing and why is crucial.

### Missing values

First, let's get some stats on exactly how much missing data we have - we're going to go back to using the original `polyps` dataset we loaded in rather than the later ones we've created as it's the original missing data that is the cause of our problems.

* `is.na()` evaluates whether the value in the cell is an `NA` so the below simply adds up how many values return an `NA` in each column.


```r
polyps %>%
  summarise(
    missing_baseline = sum(is.na(baseline)),
    missing_3m = sum(is.na(number3m)),
    missing_12m = sum(is.na(number12m))
  )
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> missing_baseline </th>
   <th style="text-align:right;"> missing_3m </th>
   <th style="text-align:right;"> missing_12m </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
</tbody>
</table>

</div>

There's actually only two missing values in the 12 month column but that's enough to have caused us big problems down the line. When we calculated `treatment`, `treatment2`, and `total` we simply added or subtracted these variables from each other, ignoring the missing values which have been replicated in any new variable that used them:


```r
polyps5 %>%
    summarise(
    missing_treatment1 = sum(is.na(treatment1)),
    missing_treatment2 = sum(is.na(treatment2)),
    missing_total = sum(is.na(total))
  )
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> missing_treatment1 </th>
   <th style="text-align:right;"> missing_treatment2 </th>
   <th style="text-align:right;"> missing_total </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
</tbody>
</table>

</div>

There's three options for dealing with this. One option is to only use a complete dataset and remove any rows that have `NA` using the `drop_na()` function. The new dataset `polyps6` only has 20 observations as we have dropped the two participants that had missing data in the `number12m` column:


```r
polyps6 <- polyps %>%
  drop_na(number12m) %>%
  mutate(treatment1 = number3m - baseline ,
    treatment2 = number12m - number3m,
    total = treatment1 + treatment2)

polyps6 %>%
  summarise(
    mean_polyps = mean(total),
    median_polyps = median(total),
    min_polyps = min(total),
    max_polyps = max(total)
  )
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> mean_polyps </th>
   <th style="text-align:right;"> median_polyps </th>
   <th style="text-align:right;"> min_polyps </th>
   <th style="text-align:right;"> max_polyps </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> -19.85 </td>
   <td style="text-align:right;"> -5 </td>
   <td style="text-align:right;"> -274 </td>
   <td style="text-align:right;"> 26 </td>
  </tr>
</tbody>
</table>

</div>

A second option is to tell R to ignore the missing values in its calculations. We'll use the `polyps5` dataset that we first created that has the missing values but this time when we run `summarise()`, we add in `na.rm = TRUE` which means "remove the NAs from the calculation". A useful addition to `summarise()` if you're going to use `na.rm = TRUE` is to call `n = n()` which essentially does the same thing as `count()` and tells you how many observations were used in the calculation and helps ensure we don't accidentally calculate summary data with lots of missing observations without realising it:


```r
polyps6 %>%
  summarise(
    mean_polyps = mean(total, na.rm = TRUE),
    median_polyps = median(total, na.rm = TRUE),
    min_polyps = min(total, na.rm = TRUE),
    max_polyps = max(total, na.rm = TRUE),
    n = n()
  )
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> mean_polyps </th>
   <th style="text-align:right;"> median_polyps </th>
   <th style="text-align:right;"> min_polyps </th>
   <th style="text-align:right;"> max_polyps </th>
   <th style="text-align:right;"> n </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> -19.85 </td>
   <td style="text-align:right;"> -5 </td>
   <td style="text-align:right;"> -274 </td>
   <td style="text-align:right;"> 26 </td>
   <td style="text-align:right;"> 20 </td>
  </tr>
</tbody>
</table>

</div>


Finally, we could also replace the `NA` values with another value. For example, in some datasets it might be appropriate to replace missing data with 0 or with the mean. Neither of these are actually appropriate for this dataset but we'll show you the code for how to do it anyway:


```r
# replace NAs with 0
polyps7 <- polyps %>%
  mutate(number12m = replace_na(number12m, 0))%>%
  mutate(treatment1 = number3m - baseline ,
    treatment2 = number12m - number3m,
    total = treatment1 + treatment2)

# replace NAs with the mean of the 12m column
polyps8 <- polyps %>%
  mutate(number12m = replace_na(number12m, mean(number12m, na.rm = TRUE)))%>%
  mutate(treatment1 = number3m - baseline ,
    treatment2 = number12m - number3m,
    total = treatment1 + treatment2)
```

Missing data can be quite difficult to deal with depending on how it is represented. As always, no amount of coding expertise can make up for not understanding the structure and idiosyncrasies of your data. 

### Group By {#dplyr-groupby}

We've already used `group_by()` a few times but it's worth introducing it formally. `group_by()` groups the dataset and whatever function is then performed on that dataset will be done separately by each level of the grouping variable. `group_by()` makes it very easy to look at different combinations of variables:


```r
# by one grouping variable (sex)

polyps5 %>%
  group_by(sex) %>%
  summarise(mean = mean(total, na.rm = TRUE))

# by two grouping variables

polyps5 %>%
  group_by(sex, treatment) %>%
  summarise(mean = mean(total, na.rm = TRUE))

# order of the variables affects the layout of the table produced

polyps5 %>%
  group_by(treatment, sex) %>%
  summarise(mean = mean(total, na.rm = TRUE))
```

You can also use `group_by()` in combination with other functions. For example, `slice_max()` returns the top N rows, ordered by a specific variable. In this example, we return the three patients with the largest reduction in polyps.


```r
# return top 3 sales
polyps5 %>%
  slice_max(n = 3, order_by = desc(total))
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> participant_id </th>
   <th style="text-align:left;"> sex </th>
   <th style="text-align:right;"> age </th>
   <th style="text-align:right;"> baseline </th>
   <th style="text-align:left;"> treatment </th>
   <th style="text-align:right;"> number3m </th>
   <th style="text-align:right;"> number12m </th>
   <th style="text-align:right;"> treatment1 </th>
   <th style="text-align:right;"> treatment2 </th>
   <th style="text-align:right;"> total </th>
   <th style="text-align:left;"> improvement </th>
   <th style="text-align:left;"> category </th>
   <th style="text-align:left;"> risk </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 010 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 19 </td>
   <td style="text-align:right;"> 318 </td>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:right;"> 347 </td>
   <td style="text-align:right;"> 44 </td>
   <td style="text-align:right;"> 29 </td>
   <td style="text-align:right;"> -303 </td>
   <td style="text-align:right;"> -274 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> High </td>
   <td style="text-align:left;"> High risk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 011 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 17 </td>
   <td style="text-align:right;"> 160 </td>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:right;"> 142 </td>
   <td style="text-align:right;"> 25 </td>
   <td style="text-align:right;"> -18 </td>
   <td style="text-align:right;"> -117 </td>
   <td style="text-align:right;"> -135 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> High </td>
   <td style="text-align:left;"> High risk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 021 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> -15 </td>
   <td style="text-align:right;"> -4 </td>
   <td style="text-align:right;"> -19 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> Medium </td>
   <td style="text-align:left;"> Not high risk </td>
  </tr>
</tbody>
</table>

</div>

But this can be combined with `group_by()` to return the largest reductions for each treatment:


```r
# return top sale for each region
polyps5 %>%
  group_by(treatment) %>%
  slice_max(n = 3, order_by = desc(total))
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> participant_id </th>
   <th style="text-align:left;"> sex </th>
   <th style="text-align:right;"> age </th>
   <th style="text-align:right;"> baseline </th>
   <th style="text-align:left;"> treatment </th>
   <th style="text-align:right;"> number3m </th>
   <th style="text-align:right;"> number12m </th>
   <th style="text-align:right;"> treatment1 </th>
   <th style="text-align:right;"> treatment2 </th>
   <th style="text-align:right;"> total </th>
   <th style="text-align:left;"> improvement </th>
   <th style="text-align:left;"> category </th>
   <th style="text-align:left;"> risk </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 010 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 19 </td>
   <td style="text-align:right;"> 318 </td>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:right;"> 347 </td>
   <td style="text-align:right;"> 44 </td>
   <td style="text-align:right;"> 29 </td>
   <td style="text-align:right;"> -303 </td>
   <td style="text-align:right;"> -274 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> High </td>
   <td style="text-align:left;"> High risk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 002 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:right;"> 77 </td>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:right;"> 67 </td>
   <td style="text-align:right;"> 63 </td>
   <td style="text-align:right;"> -10 </td>
   <td style="text-align:right;"> -4 </td>
   <td style="text-align:right;"> -14 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> High </td>
   <td style="text-align:left;"> High risk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 017 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 54 </td>
   <td style="text-align:left;"> placebo condition </td>
   <td style="text-align:right;"> 45 </td>
   <td style="text-align:right;"> 46 </td>
   <td style="text-align:right;"> -9 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> -8 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> High </td>
   <td style="text-align:left;"> High risk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 011 </td>
   <td style="text-align:left;"> male </td>
   <td style="text-align:right;"> 17 </td>
   <td style="text-align:right;"> 160 </td>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:right;"> 142 </td>
   <td style="text-align:right;"> 25 </td>
   <td style="text-align:right;"> -18 </td>
   <td style="text-align:right;"> -117 </td>
   <td style="text-align:right;"> -135 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> High </td>
   <td style="text-align:left;"> High risk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 021 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> -15 </td>
   <td style="text-align:right;"> -4 </td>
   <td style="text-align:right;"> -19 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> Medium </td>
   <td style="text-align:left;"> Not high risk </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 007 </td>
   <td style="text-align:left;"> female </td>
   <td style="text-align:right;"> 23 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:left;"> sulindac condition </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> -5 </td>
   <td style="text-align:right;"> -5 </td>
   <td style="text-align:right;"> -10 </td>
   <td style="text-align:left;"> Improvement </td>
   <td style="text-align:left;"> Medium </td>
   <td style="text-align:left;"> Not high risk </td>
  </tr>
</tbody>
</table>

</div>

## Complications

### Rounding

For the purposes of this exercise we're going to switch back to the `opt` gum disease dataset as we need some data in decimal form. 

First, let's just round one variable to one decimal place.`BL.PD.avg` is the whole-mouth average pocket depth at baseline (mm).


```r
opt2 <- opt %>%
  select(BL.PD.avg) %>%
  mutate(BL.PD.avg.1 = round(BL.PD.avg, 1), # round to one decimal place
         BL.PD.avg.whole = round(BL.PD.avg)) # round to whole number
```

Looks fairly simple, but, there's a quirk of rounding in R that's important to know about. Let's filter the values to those that are 2.5, 3.5 and 4.5 and then round those values to a whole number:


```r
opt3 <- opt2 %>%
  filter(BL.PD.avg.1 %in% c(1.5, 2.5, 3.5, 4.5)) %>%
  mutate(BL.PD.avg.whole = round(BL.PD.avg.1))
```

If you look through the values you will spot that 2.5 has rounded down to 2, 3.5 has rounded up to 4, and 4.5 has rounded down to 4. This may seem like a mistake, but R rounds .5 to the nearest even number, rather than always up, like you were probably taught in school. This prevents overestimation biases, since x.5 is *exactly* halfway between x and x+1, so there is no reason it should always round up.


```r
round(0.5)
round(1.5)
```

```
## [1] 0
## [1] 2
```

However, this might throw a monkey wrench into your own systems. For example, at the University of Glasgow, the policy is to round up for course marks at x.5. One solution is to define your own version of `round()` (modified from [Andrew Landgraf's blog](http://andrewlandgraf.com/2012/06/15/rounding-in-r/){target="_blank"}). Put it in a hidden code block at the top of your script, with a clear warning that this is changing the way `round()` normally works. You don't need to understand how this function works, just how to use it.

when you run this code, a new section will appear in the environment pane labelled "Functions". In addition to using functions from packages, you can also make your own. It's not something we are going to go into detail on in this course, but it's useful to know the functionality exists.


```r
#!!!!!! redefining round so 5s round up !!!!!! 
round <- function(x, digits = 0) {
  posneg = sign(x)
  z = abs(x)*10^digits
  z = z + 0.5 + sqrt(.Machine$double.eps)
  z = trunc(z)
  z = z/10^digits
  z*posneg
}
```

Now `round()` should work as you'd expect.


```r
round(0.5)
round(1.5)
```

```
## [1] 1
## [1] 2
```

Just remove your version if you want R to go back to the original method. Remember that you have to define the new round method in any script that uses it, and run the definition code before you use it interactively. You can check your Environment pane to see whether `round` is listed under "Functions".


```r
# remove new round() method
rm(round)
```

## Exercise

To help consolidate what you've learned in this chapter, use a dataset of your own that you're familiar with and replicate the functions we've gone through in this chapter. Additionally, try and identifying other steps in your data cleaning and wrangling process that you typically need to perform and see if you can find the necessary functions or arguments needed to do so. If you're looking for help online, you may find it useful to append "r dplyr" to any search, for example, "rename a column r dplyr" as this will be more likely to return results that use the tidyverse style of code we have used in this course.

## Glossary {#glossary-wrangle}

<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> term </th>
   <th style="text-align:left;"> definition </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> [boolean expression](https://psyteachr.github.io/glossary/b.html#boolean-expression){class="glossary" target="_blank"} </td>
   <td style="text-align:left;"> An expression that evaluates to TRUE or FALSE. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> [match operator](https://psyteachr.github.io/glossary/m.html#match-operator){class="glossary" target="_blank"} </td>
   <td style="text-align:left;"> A binary operator (%in%) that returns a logical vector indicating if there is a match or not for its left operand. </td>
  </tr>
  <tr>
   <td style="text-align:left;"> [operator](https://psyteachr.github.io/glossary/o.html#operator){class="glossary" target="_blank"} </td>
   <td style="text-align:left;"> A symbol that performs some mathematical or comparative process. </td>
  </tr>
</tbody>
</table>



## Further resources {#resources-wrangle}

* [Data transformation cheat sheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-transformation.pdf)
* [Chapter 5: Data Transformation ](http://r4ds.had.co.nz/transform.html) in *R for Data Science*
* [Chapter 19: Functions](https://r4ds.had.co.nz/functions.html) in *R for Data Science*
* [Introduction to stringr](https://stringr.tidyverse.org/articles/stringr.html)




