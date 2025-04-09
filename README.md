# SQL-Mastery-Diabetes-Dataset-Analysis-and-Reporting
This project demonstrates advanced SQL techniques to explore and analyze the Diabetes Dataset. It involves data cleaning, aggregation, and exploration to uncover insights into diabetes diagnosis and related health factors. The project showcases SQL expertise in data validation, analysis, and optimized reporting.

## Technologies Used
- **SQL**: For querying, data cleaning, and analysis.
- **MySQL** (or your chosen database): For storing and managing the dataset.
- **Markdown**: For project documentation and reporting.

## Project Objectives
- **Data Exploration**: Use SQL queries to explore the Diabetes Dataset and understand key trends and patterns.
- **Data Cleaning**: Handle missing or inconsistent data through SQL techniques to ensure dataset integrity.
- **Aggregated Analysis**: Apply aggregation functions to uncover insights, such as average glucose levels by age group or BMI categories.
- **Advanced SQL Techniques**: Use advanced SQL features such as joins, subqueries, and window functions to extract deeper insights from the dataset.
- **Reporting**: Generate and optimize SQL queries to produce actionable reports on diabetes diagnosis and contributing factors.


## Step 1: Handling Missing Values

Before we proceed with the analysis, it’s important to ensure that no data is missing. If there are any missing values, we will handle them appropriately.

### a. Checking for Missing Data

To identify which columns  have missing values, we  run the following SQL query to count the number of missing values in each column:

    ```sql
          SELECT 
             SUM(CASE WHEN pregnancies IS NULL THEN 1 ELSE 0 END) AS missing_pregnancies,
             SUM(CASE WHEN glucose IS NULL THEN 1 ELSE 0 END) AS missing_glucose,
             SUM(CASE WHEN blood_pressure IS NULL THEN 1 ELSE 0 END) AS missing_bloodpressure,
             SUM(CASE WHEN skin_thickness IS NULL THEN 1 ELSE 0 END) AS missing_skinthickness,
             SUM(CASE WHEN insulin IS NULL THEN 1 ELSE 0 END) AS missing_insulin,
             SUM(CASE WHEN bmi IS NULL THEN 1 ELSE 0 END) AS missing_bmi,
             SUM(CASE WHEN diabetes_pedigree_function IS NULL THEN 1 ELSE 0 END) AS missing_dpf,
             SUM(CASE WHEN age IS NULL THEN 1 ELSE 0 END) AS missing_age
         FROM healthcare.diabetes_data;

### b. Deleting Rows with Missing Values

To remove rows with missing values in specific columns  use the following SQL query:

    ```sql
    DELETE FROM healthcare.diabetes_data
    WHERE Glucose IS NULL OR BMI IS NULL;


## Step 2: Handling Duplicates

Duplicate records can distort the analysis and need to be handled to ensure the dataset's accuracy.

### a. Checking for Duplicates

To identify any duplicate rows in the dataset, use this SQL query:

    ```sql
    SELECT COUNT(*) AS total_rows, 
       COUNT(DISTINCT *) AS unique_rows
    FROM healthcare.diabetes_data;


### b. Removing Duplicates

Once duplicates are identified, you can remove them using the following query:

    ```sql
    DELETE FROM healthcare.diabetes_data
    WHERE id NOT IN (
    SELECT MIN(id)
    FROM healthcare.diabetes_data
    GROUP BY pregnancies, glucose, blood_pressure, skin_thickness, insulin, bmi, diabetes_pedigree_function, age, outcome
    );


## Step 3: Handling Outliers

Outliers can have a significant impact on the results of data analysis, especially in statistical modeling. Identifying and handling outliers is an important part of data cleaning. You can either remove or treat them depending on the context of your analysis.

### a. Identifying Outliers

One of the methods to identify outliers is by using statistical measures such as the IQR (Interquartile Range). The IQR is the range between the first quartile (Q1) and the third quartile (Q3), and any value outside 1.5 times the IQR can be considered an outlier.

Here’s how you can calculate Q1, Q3, and the IQR for **Glucose** .

    ```sql
    SELECT 
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY Glucose) AS Q1,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY Glucose) AS Q3,
    (PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY Glucose) - PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY Glucose)) AS IQR
    FROM healthcare.diabetes_data;
 

### b. Removing Outliers

Once outliers are identified, you can decide whether to remove them. For example, if you consider values below the lower bound or above the upper bound as outliers for **Glucose**, you can delete those rows using the following SQL query:

```sql
DELETE FROM healthcare.diabetes_data
WHERE Glucose < (SELECT PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY Glucose) - 1.5 * (PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY Glucose) - PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY Glucose)))
   OR Glucose > (SELECT PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY Glucose) + 1.5 * (PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY Glucose) - PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY Glucose)));






