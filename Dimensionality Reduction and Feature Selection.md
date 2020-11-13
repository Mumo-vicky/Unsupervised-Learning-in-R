# **Dimensionality Reduction and Feature Selection**
I am a data analyst at Carrefour. It is an supermarket company that has various outlets acroos the globe and I have been tasked with the work of formulating a suitable marketing strategy that will help in maximising the profits of the company. I have been provided with 

```R
#loading the appropriate libraries
library(data.table)
library(tidyverse)
library(readr)
library(psych)
library(corrplot)
library(Hmisc)
library(caret)
```
## **Data Loadning and Checking.**
```R
sales <- fread("http://bit.ly/CarreFourDataset")
head(sales)
```
```R
dim(sales)
```
1000 rows and 16 columns

```R
colnames(sales)
```
```R
str(sales)
```
```R
summary(sales)
```

## **Data Cleaning.**
```R
anyDuplicated(sales)
```
No duplicates in the data
```R
sum(is.na(sales))
```
No missing values.
```R
sales[!complete.cases(sales),]
```
No missing values in the columns
```R
#changing column names to lowercase
names(sales) <- tolower(names(sales))

#changing column names.
setnames(sales, old = c("invoice id", "customer type", "product line", "unit price", "gross margin percentage", "gross income"), new = c("invoice_id" ,"customer_type", "product_line", "unit_price", "gross_margin_%", "gross_income"))

sum(sales$total != (sales$cogs + sales$tax))
#252 totals are not the same as the sum of the cogs and the tax

sum(sales$cogs != (sales$unit_price * sales$quantity))
#173 cogs values arent the same as the multiplication of the unit price and the quantity.

#we will take care of the above two columns
sales$cogs <- sales$unit_price * sales$quantity
sales$total <- sales$cogs + sales$tax

sum(sales$cogs != (sales$unit_price * sales$quantity))
sum(sales$total != (sales$cogs + sales$tax))

#checking for the change
str(sales)
numerical.columns <- select(sales, 'unit_price', 'quantity', 'tax', 'cogs', 'gross_margin_%', 'gross_income', 'rating', 'total')
```
```R
describe(numerical.columns)
```
From our description, the gross margin percentage column has only one distinct value. We will remove the whole column since it doesnt provide a varse insight to our dataset. 
We can also see that the tax and the gross income are of the same values all through. We will look deeper into this.
```R
sales <- sales[,-c(13)]
numerical.columns <- numerical.columns[,-c(5)]

boxplot(numerical.columns)

sum(sales$tax != sales$gross_income)

```
The cogs and total columns have few outliers. We will leave these since they are calculated fields and they depend on the unit price and quantity columns which do not seen to have any outliers
I have also confirmed the similatity between the tax and gross income columns. I will thus remove one of them.

```R
sales <- sales[,-c(13)]
head(sales)
```

## **Exploratory Data Analysis.**
```R
sales$ratings <- as.numeric(sales$ratings)
sales$totals <- as.numeric(sales$ratings)

par(mfrow=c(2,2))
hist(sales$unit_price)
hist(sales$cogs)
hist(sales$tax)
hist(sales$quantity)
#hist(sales$ratings)
#hist(sales$totals)

```
## **Implementing Solution.**