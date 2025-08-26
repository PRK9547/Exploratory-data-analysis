#  Exploratory Data Analysis on Layoffs Dataset (MySQL)

##  Project Overview

This project focuses on **Exploratory Data Analysis (EDA)** using **MySQL** on a global layoffs dataset.
The dataset captures information about company layoffs, industries, countries, funding, and stages over time.

The aim is to analyze patterns, trends, and insights from layoffs data across different dimensions such as **company, country, industry, and time**.

---

## 🛠️ Technologies Used

* **SQL (MySQL Workbench / MySQL Server)**
* **Dataset**: Layoffs dataset (processed and cleaned into `layoffs_staging2` table)

---

## Project Structure

```
 Layoffs-EDA-MySQL
│── 📄 EXPLORATORY DATA ANALYSIS PROJECT(MY SQL).sql   # SQL queries for analysis
│── 📄 README.md                                       # Documentation
```

---

## 🔎 Key Analysis Performed

The following SQL queries were written and executed to analyze the dataset:

1. **Basic Data Overview**

   ```sql
   SELECT * FROM layoffs_staging2;
   ```

   Retrieves the dataset for initial inspection.

2. **Maximum Layoffs**

   ```sql
   SELECT MAX(total_laid_off), MAX(percentage_laid_off)
   FROM layoffs_staging2;
   ```

3. **Companies with Complete Layoffs**

   ```sql
   SELECT *
   FROM layoffs_staging2
   WHERE percentage_laid_off = 1
   ORDER BY funds_raised_millions DESC;
   ```

4. **Layoffs by Company**

   ```sql
   SELECT company, SUM(total_laid_off)
   FROM layoffs_staging2
   GROUP BY company
   ORDER BY 2 DESC;
   ```

5. **Layoffs by Country**

   ```sql
   SELECT country, SUM(total_laid_off)
   FROM layoffs_staging2
   GROUP BY country
   ORDER BY 2 DESC;
   ```

6. **Layoffs by Industry**

   ```sql
   SELECT industry, SUM(total_laid_off)
   FROM layoffs_staging2
   GROUP BY industry
   ORDER BY 2 DESC;
   ```

7. **Date Range of Layoffs**

   ```sql
   SELECT MIN(`date`), MAX(`date`)
   FROM layoffs_staging2;
   ```

8. **Yearly Trends**

   ```sql
   SELECT YEAR(`date`), SUM(total_laid_off)
   FROM layoffs_staging2
   GROUP BY YEAR(`date`)
   ORDER BY 1 DESC;
   ```

9. **Layoffs by Stage**

   ```sql
   SELECT stage, SUM(total_laid_off)
   FROM layoffs_staging2
   GROUP BY stage
   ORDER BY 1 DESC;
   ```

10. **Monthly Layoffs with Rolling Total**

```sql
WITH Rolling_Total AS (
    SELECT SUBSTRING(`date`,1,7) AS `MONTH`, 
           SUM(total_laid_off) AS total_off
    FROM layoffs_staging2
    WHERE SUBSTRING(`date`,1,7) IS NOT NULL
    GROUP BY `MONTH`
    ORDER BY `MONTH` ASC
)
SELECT `MONTH`, 
       total_off AS total_laid_off, 
       SUM(total_off) OVER(ORDER BY `MONTH`) AS rolling_total
FROM Rolling_Total;
```

11. **Top 5 Companies by Year (Ranking)**

```sql
WITH Company_year AS (
    SELECT company, YEAR(`date`) AS years, SUM(total_laid_off) AS total_laid_off
    FROM layoffs_staging2
    WHERE `date` IS NOT NULL
    GROUP BY company, YEAR(`date`)
),
Company_Year_Rank AS (
    SELECT company, years, total_laid_off,
           DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS Ranking
    FROM Company_year
)
SELECT *
FROM Company_Year_Rank
WHERE Ranking <= 5;
```

---

## Insights & Findings:

* Some companies had **100% layoffs** despite raising significant funds.
* The **technology sector** had the highest layoffs compared to others.
* Layoffs were concentrated in certain years, peaking during global financial downturns.
* Certain countries (e.g., **USA, India**) saw the largest layoffs.
* A **rolling trend** helped understand layoffs growth month-over-month.
* Ranking analysis identified the **top 5 companies per year** with the highest layoffs.

---

## How to Use

1. Clone the repository:

   ```bash
   git clone https://github.com/<your-username>/<repo-name>.git
   ```
2. Import the dataset into **MySQL** (make sure the table is named `layoffs_staging2`).
3. Run the queries inside `EXPLORATORY DATA ANALYSIS PROJECT(MY SQL).sql` in MySQL Workbench or any SQL editor.
