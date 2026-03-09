# Sql_Practice_data_job_analysis
# 📊 Data Analyst Job Market Analysis — SQL Project

---

### Introduction

This project explores the **data analyst job market** using SQL to answer key career questions:
- 💰 Which jobs pay the most?
- 🔥 Which skills are most in demand?
- 📈 Which skills lead to higher salaries?
- 🎯 What's the best skill to learn for maximum ROI?

All queries were written in **PostgreSQL** and analyze thousands of real job postings
to help aspiring data analysts make smarter career decisions.

---

### Background

Breaking into the data field can be overwhelming. This project was built to answer one core question:

> *"What skills should a data analyst learn to maximize both employability and salary?"*

The dataset contains real job postings with salary, location, required skills, and company info.
The analysis focuses on **remote Data Analyst roles** with listed salaries.

---

### 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| **PostgreSQL** | Database & running SQL queries |
| **VS Code** | Writing and managing SQL files |
| **GitHub** | Version control & project sharing |
| **GitHub Desktop** | Easy push/pull workflow |

---

### 📂 Analysis

#### 1️⃣ Top Paying Jobs
```sql
SELECT 
    job_title,
    salary_year_avg,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE job_title_short = 'Data Analyst'
AND job_location = 'Anywhere'
AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```

**Key Finding:** Top salaries range from **$184,000 to $650,000/year** for remote roles.

---

#### 2️⃣ Skills Behind Top Paying Jobs
```sql
WITH top_paying_jobs AS (
    SELECT job_id, job_title, salary_year_avg, name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE job_title_short = 'Data Analyst'
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)
SELECT top_paying_jobs.*, skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```

**Key Finding:** SQL, Python, and Tableau appear most in top-paying roles.

---

#### 3️⃣ Most In-Demand Skills
```sql
SELECT 
    skills,
    COUNT(skills_job_dim.skill_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND job_work_from_home = 'Yes'
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

**Top 5 Most In-Demand Skills:**

| Skill    | Demand Count |
|----------|-------------|
| SQL      | 7,291 |
| Excel    | 4,611 |
| Python   | 4,330 |
| Tableau  | 3,745 |
| Power BI | 2,609 |

---

#### 4️⃣ Highest Paying Skills
```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg), 0) AS average_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
AND salary_year_avg IS NOT NULL
AND job_work_from_home = 'Yes'
GROUP BY skills
ORDER BY average_salary DESC
LIMIT 25;
```

**Top Paying Skills:**

| Skill      | Avg Salary |
|------------|------------|
| PySpark    | $208,172   |
| Bitbucket  | $189,155   |
| Watson     | $160,515   |
| DataRobot  | $155,486   |
| Swift      | $153,750   |

---

#### 5️⃣ Optimal Skills (High Demand + High Pay)
```sql
WITH skills_demand AS (
    SELECT skills_dim.skill_id, skills_dim.skills,
           COUNT(skills_job_dim.skill_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL AND job_work_from_home = 'Yes'
    GROUP BY skills_dim.skill_id
),
average_salary AS (
    SELECT skills_dim.skill_id,
           ROUND(AVG(salary_year_avg), 0) AS average_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL AND job_work_from_home = 'Yes'
    GROUP BY skills_dim.skill_id
)
SELECT skills_demand.skills, demand_count, average_salary
FROM skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE demand_count > 10
ORDER BY average_salary DESC, demand_count DESC
LIMIT 25;
```

**Key Finding:** Go, Snowflake, and Python offer the best balance of salary and demand.

---

### 💡 What I Learned

- **CTEs** make complex multi-step queries cleaner and easier to read
- **JOINs** are essential — almost every insight required joining 3+ tables
- **Aggregations** like `COUNT()` and `AVG()` with `GROUP BY` reveal powerful trends
- **Filtering matters** — focusing on remote jobs with listed salaries gave actionable data
- Real datasets are messy — `IS NOT NULL` checks were needed constantly

---

### ✅ Conclusion

1. **SQL is non-negotiable** — #1 most demanded skill by far
2. **Python pays off** — high demand AND top-paying roles
3. **Cloud tools** (Snowflake, Azure, AWS) are increasingly valuable
4. **Niche skills** (PySpark, DataRobot) pay a lot but have lower demand
5. **Sweet spot:** SQL + Python + Tableau or Power BI

> *"Don't learn everything. Learn the right things."*

---

*Project by Tushi | PostgreSQL | VS Code*