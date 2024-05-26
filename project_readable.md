
###                  Data Analytics: Skills and Salaries Analysis for optimized job search
# Introduction
This project focuses on data analyst careers, identifying the highest-paying positions, the most in-demand skills, and the locations where demanded expertise meets lucrative pay and streamlining the job search process.

# Softwares and tools Used for the project
SQL is used to query databases and derive information.
PostgreSQL is the database management system utilized.
Visual Studio Code is Used to manage databases and execute SQL queries.
Git and GitHub: sharing SQL scripts and analysis

### The project focuses on answering the below questions:
1) What are the best-paying data analyst jobs?
2) What skills are necessary for these high-paying jobs?
3) What are the most in-demand skills for data analysts?
4) Which talents lead to greater salaries?
5) What are the best talents to learn?

# Analysis
Each query attempted to clarify a distinct facet of the data analyst job market. Here's how to handle each question:

# 1. Top Paying Data Analyst Jobs
To find the highest-paying employment, I sorted data analyst postings by average annual income and location, with a concentration on remote work. This query focuses on the field's high-paying possibilities.

```sql
/* top_paying_jobs */

SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name as company_name
FROM 
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
     job_title_short= 'Data Analyst' AND 
     job_location = 'Anywhere' AND
     salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```
![Average Salary Distribution for Top 10 Paying Data Analyst Jobs in 2023](/data_analysis_sql_proj/graph1.png)
The above graph visualizes 20203's top data analyst positions offered a wide range of opportunities: from lucrative roles with salaries ranging from $184,000 to $650,000, demonstrating the field's significant earning potential, to a diverse range of employers such as SmartAsset, Meta, and AT&T, demonstrating widespread interest across industries. Furthermore, the employment market had a varied range of titles, from Data Analyst to Director of Analytics, representing the many functions and specialized specializations within the world of data analytics.



# 2. Skills for Top Paying Jobs
This query displays the necessary abilities for the highest-paying data analyst positions.

```sql
/* top_paying_job_skills */

WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        name AS company_name
    FROM 
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
         job_title_short= 'Data Analyst' AND 
         job_location = 'Anywhere' AND
         salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)
SELECT 
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id;
```

![Skill Count for Top 10 Paying Data Analyst Jobs in 2023](/data_analysis_sql_proj/graph2.png)

The above graphs visualizes the top ten highest-paying data analyst roles in 2023 will require the following skills: SQL emerged as the frontrunner, with a substantial total of 8, closely followed by Python with 7 references. Tableau also received a lot of attention, with a total of six. Meanwhile, R, Snowflake, Pandas, and Excel were in varied degrees of demand in the data analyst job market.



# 3. In-Demand Skills for Data Analysts

This query finds the most commonly required skills in data analyst job ads.

```sql
/* job demanded skills */

SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;

```
![In-Demand Skills for Data Analysts](/data_analysis_sql_proj/graph3.png)

In 2023, the most in-demand skills for data analysts emphasized the fundamental relevance of SQL and Excel, as well as the need for proficient data processing and spreadsheet manipulation capabilities. Furthermore, knowledge of programming languages such as Python, as well as visualization tools like as Tableau and Power BI, emerged as essential, demonstrating the rising importance of technical expertise in supporting effective data storytelling and decision support.

# 4. Skills Based on Salary
This query determines which talents correspond with higher average incomes.

```sql
/* top_paying_skills */

SELECT 
    skills,
    AVG(salary_year_avg) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
      job_title_short = 'Data Analyst' AND
      salary_year_avg IS NOT NULL
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25;

```
![Skills Based on Salary](/data_analysis_sql_proj/graph4.png)

The above graph visualizes Top-paying Data Analyst talents in 2023 will include big data (PySpark, Couchbase), machine learning (DataRobot, Jupyter), and Python libraries (Pandas, NumPy). Proficiency in software development tools (GitLab, Kubernetes) and cloud platforms (Elasticsearch, Databricks, GCP) improves earning potential, emphasizing the necessity of automation and cloud-based analytics settings.

# 5. Most Optimal Skills to Learn

This query combines demand and income statistics to identify the best talents to study.
```sql
/* optimized_skills */

WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL AND
        job_work_from_home = TRUE 
    GROUP BY
        skills_dim.skill_id
), 
average_salary AS (
    SELECT 
        skills_job_dim.skill_id,
        ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL AND
        job_work_from_home = TRUE 
    GROUP BY
        skills_job_dim.skill_id
)
SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM
    skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE  
    demand_count > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```
![Most Optimal Skills to Learn](/data_analysis_sql_proj/graph5.png)

The above graph visualizes 2023's most ideal skills for Data Analysts, listed by income, provide a varied landscape: Python and R appear as highly sought-after programming languages, with average salaries of $101,397 and $100,499, respectively, suggesting their great worth despite ubiquitous availability. Cloud technologies like as Snowflake, Azure, AWS, and BigQuery are in great demand and pay well, demonstrating the rising relevance of cloud platforms and big data tools. Business intelligence and visualization products like Tableau and Looker, with average wages ranging from $99,288 to $103,795, show the importance of data visualization in obtaining actionable insights.Furthermore, knowledge of database technologies such as Oracle, SQL Server, and NoSQL is still in high demand, with average wages ranging from $97,786 to $104,534, highlighting the ongoing need for data storage and management abilities in the data analyst field.


# Conclusion
Remote data analysts earn a broad variety of salaries, with the top reaching $650,000. SQL is the most in-demand expertise in the industry, hence advanced SQL competence is essential for landing these high-paying positions. Specialized abilities like SVN and Solidity command more pay. SQL is a critical talent for data analysts to master in order to optimize their market worth, since it is both in great demand and pays well on average.This assignment considerably improved my SQL skills and gave essential insights into the data analyst employment market. The findings advise prospective data analysts to focus on high-demand, high-paying talents, highlighting the significance of constant learning and adapting to changing trends in data analysis.



Link to SQL queries and data [data_analysis_sql_proj folder](/data_analysis_sql_proj/)


