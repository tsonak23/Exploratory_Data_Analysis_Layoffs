# Exploratory_Data_Analysis_Layoffs_Oracle_SQL

This EDA covered very basic as well as advance SQL queries. Though the tutorial video was about 40 minutes, it took me more than 3 hours to finish. ⌛
Won't say I am confident about all the quereis used in here ,but overall it was challenging and fun project. 😅 

Project Title: Exploratory Data Analysis to find patterns, trends using Oracle_SQL

Query with explanation: 

#️⃣--Reviewed mentioned columns to understand layoff nos. 

select * from layoffs_staging2;

select max(to_number(total_laid_off))
from layoffs_staging2

select max(to_number(total_laid_off))
from layoffs_staging2
where percentage_laid_off = 1
