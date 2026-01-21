# Exploratory_Data_Analysis_Layoffs_Oracle_SQL

This EDA covered very basic as well as advance SQL queries. Though the tutorial video was about 40 minutes, it took me more than 3 hours to finish. ⌛
Won't say I am confident about all the quereis used in here ,but overall it was challenging and fun project. 😅 

Project Title: Exploratory Data Analysis to find patterns, trends using Oracle_SQL

Query with explanation:

#️⃣--Started the analysis with highest nos. of layoffs in a day.
 
select * from layoffs_staging2;

select max(total_laid_off) 
from layoffs_staging2;

select max(total_laid_off), max(percentage_laid_off)
from layoffs_staging2;

select * from layoffs_staging2
where percentage_laid_off =1
order by total_laid_off desc NULLS last;

Observations: A large number of companies went out of business during the COVID period.

#️⃣--Reviewed the laid off nos. company wise:

select company, sum(total_laid_off)
from layoffs_staging2
group by company
order by sum(total_laid_off) desc
nulls last;

Observations: Many of the highest layoffs came from large companies.


#️⃣--Reviewed the laid off nos. industry wise:

select min(layoff_date), max(layoff_date)
from layoffs_staging2;

select industry, sum(total_laid_off)
from layoffs_staging2
group by industry
order by sum(total_laid_off) desc
nulls last;

Observations: The consumer and retail sectors were the hardest hit.


#️⃣--Reviewed the laid off nos. country wise:


select * from layoffs_staging2;

select country, sum(total_laid_off)
from layoffs_staging2
group by country
order by sum(total_laid_off) desc
nulls last;

Observations:The United States experienced a significant number of layoffs.

#️⃣--Reviewed the laid off nos. year wise:

select extract(year from layoff_date),sum(total_laid_off)
from layoffs_staging2
group by extract(year from layoff_date)
order by 1 desc nulls last ;


Observations: 2023 marked the peak of layoffs.

#️⃣--Reviewed the laid off nos. stage wise:

select stage ,sum(total_laid_off)
from layoffs_staging2
group by stage
order by 2 desc nulls last ;


Observations: Big companies are among the top contributors to layoffs.


#️⃣-- Review sum of layoff nos. month and year wise. Used CTE with partition to get rollng total of every month of the particular year. 


select * from layoffs_staging2;

select to_char(layoff_date, 'yyyy-mm') as month_year , sum(total_laid_off)
from layoffs_staging2
where to_char(layoff_date, 'yyyy-mm') is not NULL
group by to_char(layoff_date, 'yyyy-mm')
order by 1 asc;



WITH monthly_totals AS (
    SELECT
        TRUNC(layoff_date, 'MM') AS month_start,
        SUM(total_laid_off) AS monthly_total
    FROM layoffs_staging2
    WHERE layoff_date IS NOT NULL
    GROUP BY TRUNC(layoff_date, 'MM')
)
SELECT
    TO_CHAR(month_start, 'YYYY-MM') AS month_year,
    monthly_total,
    SUM(monthly_total) OVER (
        ORDER BY month_start
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS rolling_total
FROM monthly_totals
ORDER BY month_start;


Observations: Layoffs started rising sharply after the COVID lockdown. The last few months of 2022 were really tough.
The start of 2023 was the worst of all.


#️⃣--Ranked the companies with respect to nos of layoffs per year.

select company ,extract(year from layoff_date) , sum(total_laid_off)
from layoffs_staging2
group by company, extract(year from layoff_date) 
order by 3 desc nulls last;


with company_year (Company, Year, Total_laid_off) as
(
select company, to_char(layoff_date, 'YYYY') as Year ,sum(total_laid_off) as Total_laid_off
from layoffs_staging2
group by company, to_char(layoff_date, 'YYYY')
),
Company_year_rank as 
(
select Company, Year, Total_laid_off , dense_rank() over (partition by Year ORDER by Total_laid_off desc  nulls LAST
) as Ranks
from company_year
where Year is not NULL
)
select * 
from Company_year_rank 
where Ranks <=5;



