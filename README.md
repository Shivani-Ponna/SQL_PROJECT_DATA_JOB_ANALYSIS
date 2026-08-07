# Introduction
Dive into data job market! Focusing on data analytics roles, this project explores top-paying jobs, in-demand skills and where high demand meets high salary in data analytics.

SQL queries? Check them out here: [project_sql](/project_sql/)
# Background
Driven by a quest to navigate the data analyst job market more effectively, the project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

Data hails from my [SQL Course](https://www.lukebarousse.com/sql). It's packed with insights on job titles, salaries, locations and essential skills.

### The question I wanted to answer through my SQL queries were:

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in-demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I Used
For my deep dive into the data analyst job market, I hharnessed the power of several key tools:
- **SQL:** The backbone of my analysis, allowing me to query the database and unreach critical insights.
- **PostgreSQL:** The chosen database management system, ideal for handling the job posting date.
- **Visual Studio Code:** My go-to for database management and executing SQL queries.
- **Git & Github:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here's how I approached each question:

### 1. Top Paying Data Analyst Jobs
To identify the highest-paying roles I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying oppurtunities in the field.

```sql
SELECT 
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
job_title_short = 'Data Analyst' AND
job_location = 'Canada' AND
salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 15;
```
Here's the breakdown of the top data analyst jobs:
- Canada offers highly competitive salaries for Data Analysts, with the top-listed roles paying up to $111,175 CAD annually, indicating strong earning potential for experienced professionals.
- Large technology and enterprise companies dominate the highest-paying opportunities, with organizations like Stripe, HoYoverse, Swiss Re, Sun Life, Kinaxis, and Zynga appearing multiple times among the top-paying jobs.
- Most high-paying Data Analyst positions are full-time roles and often extend beyond traditional analyst titles (e.g., Analytics Engineering Lead, Analytics Lab Architect, and Data Strategy Product Manager), suggesting that analytical skills are valuable across a wide range of advanced data-focused positions.

### 2. Skills for Top Paying Jobs
To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT 
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND
        job_location = 'Canada' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)
SELECT 
top_paying_jobs.*,
skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
ORDER BY 
    salary_year_avg DESC;
```
Here's the breakdown of the most demanded skills for the top 10 highest paying data analyst jobs:
- SQL and Python are the most common skills across the top 10 highest-paying Data Analyst jobs, making them essential technical skills for high-paying roles.
- Big data technologies like Spark and Hadoop appear frequently in top-paying positions, showing that employers highly value experience with large-scale data processing.
- Specialized tools such as Tableau, Azure, Databricks, SAP, SAS, SPSS, and TypeScript are required for specific roles, indicating that combining core data skills with domain-specific tools can increase job opportunities and salary potential.

### 3. In-Demand Skills for Data Analysts
This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM 
    job_postings_fact
INNER  JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
WHERE
    job_title_short = 'Data Analyst' AND job_country = 'Canada'
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;
```
Here's the breakdown of the most demanded skills for data analysts
- SQL and Excel remain fundamental, emphasizing the need for strong foundational skills in data processing and spreadsheet manipulation.
- programming and Visualization Tools like Python, Tableau, and Power BI are esential, pointing towards the increasing importance of technical skills in data storytelling and decision support.

### 4. Skills Based on Salary
Exploring the average salaries associated with different skills revealed which skills are the higest paying.
```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM 
    job_postings_fact
INNER  JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
WHERE
    job_title_short = 'Data Analyst' AND salary_year_avg IS NOT NULL
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25;
```
Here's the breakdown of the results for top paying skills for data Analysts:
- **High Demand for Big Data & ML Skills:** Top salaries are commanded by analysts skilled in big data technologies(PySpark, Coughbase), manchine learning tools(DataRobot, Jupyter), and Python libraries(Pandas, NumPy), reflecting the industry's high valuation of data processing and predective modelling capabilities.
- **Software Development & Deployment Proficiency:** Knowledge in development and deplyment tools(GitLab, Kubernetes, Airflow) indicates a lucrative crossover between data analysis and engineering, with a premium on skills that facilitate automation and efficient data pipeline managemnet.
-**Cloud Computing Expertise:** Familiarity withcloud and data engineering tools(Elasticsearch, Databricks, GCP) underscores the growing importance of cloud-based analytics environments, suggesting that cloud proficiency significantly boosts potential in data analytics. 
### 5. Optimal Skills to Learn
Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.
```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM 
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
WHERE
    job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY 
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```
Here's the breakdown of the most optimal skills for Data Analysis:
- **High-Demand Programming Languages:** Python and R stand out for their high demand. Despite their high demand, proficiency in these languages is highly valued but also wiedly available.
- **Machine learning and big data skills:** Skills including PyTorch, TensorFlow, and Kafka, offer above-average salaries while remaining in demand, making them excellent long-term investments for career growth.
- Business Intelligence and Visualization Tools: Tableau and Looker and average salaries, higlight the critical role of data visualization and business intelligence in deriving actionable insights fromm data.

# What I Learned
Throughout this adventure, I've turbocharged my SQL toolkit with some serious firepower:
- **Complex Query Crafting:** Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.
- **Data Aggregation:** Got cozy with GROUP BY and turned aggregate functions like COUNT() and AVG() into my data-summarizing sidekicks.
- **Analytical Wizardry:** Level up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusions

### Insights
1. **Top Paying Data Analyst Jobs:** The highest-paying Data Analyst jobs in Canada offer salaries of over $100,000 per year.
2. **Skills for Top-Paying Jobs:** High-paying data analyst jobs require advanced proficiency in SQL, suggesting it's a critical skill for earning a top salary. 
3. **Most In-Demand Skills:** SQL is also the most demanded skill in the data analyst job market, thus making it essential for job seekers.
4. **Skills with Higher Salaries:** Specializedskills, such as SVN and solidity are associated with the highest avaerage salaries, indicating a premium on niche expertise.
5. **Optimal Skills for Job Market Value:** Data Analysts who develop expertise in advanced technologies such as machine learning, big data, cloud platforms, and data engineering tools can significantly increase their earning potential, as specialized technical skills consistently attract the highest salaries.


### Closing Thoughts
This project enhanced my sql skils and provided valuable insights into the data analyst job market. The findings from the analysis serve as a guide to prioritizing skill development and job search efforts. Aspiring data analysts can better position themselves in a competitive job market by focusing on high-demand, high-slary skills. This exploration highlights the importance of continuous learning and adaption to emerging trends in the field of data analytics.