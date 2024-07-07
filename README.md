# Exploratory-Data-Analysis
This project involves exploratory data analysis (EDA) on a dataset containing information about layoffs. The goal is to uncover trends, patterns, and insights from the data.

## Table of Contents

- [Dataset](#dataset)
- [Data Preparation](#data-preparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
  - [1. Maximum Values](#1-maximum-values)
  - [2. Companies with 100% Layoffs](#2-companies-with-100-layoffs)
  - [3. Layoffs by Company](#3-layoffs-by-company)
  - [4. Date Range](#4-date-range)
  - [5. Layoffs by Industry](#5-layoffs-by-industry)
  - [6. Layoffs by Country](#6-layoffs-by-country)
  - [7. Layoffs by Date](#7-layoffs-by-date)
  - [8. Layoffs by Year](#8-layoffs-by-year)
  - [9. Layoffs by Stage](#9-layoffs-by-stage)
  - [10. Layoff Percentages by Company](#10-layoff-percentages-by-company)
  - [11. Monthly Layoffs](#11-monthly-layoffs)
  - [12. Rolling Total of Layoffs](#12-rolling-total-of-layoffs)
  - [13. Company-Year Analysis](#13-company-year-analysis)

## Dataset

The dataset `layoffs_staging2` contains information about layoffs including:
- Company
- Total laid off
- Funds raised (in millions)
- Date
- Industry
- Country
- Stage
- Percentage laid off

## Data Preparation

The following SQL commands were used to prepare the data for analysis:

```sql
ALTER TABLE layoffs_staging2
MODIFY COLUMN total_laid_off NUMERIC;

ALTER TABLE layoffs_staging2
MODIFY COLUMN funds_raised_millions NUMERIC;
```

## Exploratory Data Analysis

### 1. Maximum Values

Find the maximum number of layoffs and the maximum percentage of layoffs:

```sql
SELECT MAX(total_laid_off), MAX(percentage_laid_off)
FROM layoffs_staging2;
```

### 2. Companies with 100% Layoffs

List the companies with 100% layoffs, ordered by the total laid off and funds raised:

```sql
SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY total_laid_off DESC;

SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;
```

### 3. Layoffs by Company

Sum of total layoffs by company:

```sql
SElECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```

### 4. Date Range

Find the minimum and maximum dates in the dataset:

```sql
SELECT MIN(`date`), MAX(`date`)
FROM layoffs_staging2;
```

### 5. Layoffs by Industry

Sum of total layoffs by industry:

```sql
SElECT industry, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;
```

### 6. Layoffs by Country

Sum of total layoffs by country:

```sql
SElECT country, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;
```

### 7. Layoffs by Date

Sum of total layoffs by date:

```sql
SElECT `date`, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY `date`
ORDER BY 1 DESC;
```

### 8. Layoffs by Year

Sum of total layoffs by year:

```sql
SElECT YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY YEAR(`date`)
ORDER BY 1 DESC;
```

### 9. Layoffs by Stage

Sum of total layoffs by company stage:

```sql
SElECT stage, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;
```

### 10. Layoff Percentages by Company

Sum and average of layoff percentages by company:

```sql
SElECT company, SUM(percentage_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;

SElECT company, AVG(percentage_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```

### 11. Monthly Layoffs

Sum of total layoffs by month:

```sql
SELECT SUBSTRING(`date`, 6, 2) AS `month`, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY `month`
ORDER BY 1 ASC;

SELECT SUBSTRING(`date`, 1, 7) AS `month`, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY `month`
ORDER BY 1 ASC;
```

### 12. Rolling Total of Layoffs

Rolling total of layoffs by month:

```sql
WITH Rolling_Total AS (
  SELECT SUBSTRING(`date`, 1, 7) AS `month`, SUM(total_laid_off) AS Sum_TLF
  FROM layoffs_staging2
  GROUP BY `month`
  ORDER BY 1 ASC
)
SELECT `month`, Sum_TLF, SUM(Sum_TLF) OVER(ORDER BY `month`) AS rolling_total
FROM Rolling_Total;
```

### 13. Company-Year Analysis

Sum of total layoffs by company and year, and ranking of layoffs within each year:

```sql
SElECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
ORDER BY 3 DESC;

WITH Company_Year AS (
  SElECT company, YEAR(`date`), SUM(total_laid_off)
  FROM layoffs_staging2
  GROUP BY company, YEAR(`date`)
), Company_Year_Rank AS (
  SELECT *, DENSE_RANK() OVER(PARTITION BY years ORDER BY total_laid_off DESC) AS ranking
  FROM Company_Year
)
SELECT *
FROM Company_Year_Rank
WHERE ranking <= 5;
```
