create database bank_customer_churn_database;
use bank_customer_churn_database;
create table bank_customers (
customer_id int,
credit_score int,
country text,
gender text,
age int,
tenure int,
balance double,
products_number int,
credit_card int,
active_member int,
estimated_salary double,
churn int);
select * from bank_customers;


-- How many customers are in the dataset?

select count(customer_id) as total_customers from bank_customers;

-- What is the average age of customers?

select avg(age) as Avg_age_customers from bank_customers;

-- What is the overall churn rate?
 
 select avg(churn) as churn_rate from bank_customers;
 
 -- How many customers are from each country?
 
 select country ,count(customer_id) as customer_count from bank_customers
 group by country 
 order by count(customer_id);
 
 -- How many male and female customers are there?
 
 select gender ,
 count(*) as customer_count
 from bank_customers
 group by gender;
 
 -- How many customers have churned, and how many have not?
 
 SELECT churn,
    COUNT(customer_id) AS customers
FROM
    bank_customers
GROUP BY churn;

-- What percentage of customers churned?

select avg(churn)*100 as churned_customers
from bank_customers;
 
 -- What is the average tenure of customers?
 
 select avg(tenure) as avg_tenure from bank_customers;
 
 -- Is there a relationship between tenure and churn rate?

SELECT 
    ROUND(tenure, -1) AS tenure_group,
    AVG(churn) AS churn_rate
FROM 
    bank_customers
GROUP BY 
    ROUND(tenure, -1)
ORDER BY 
    tenure_group;

-- What is the average balance of customers?

SELECT 
    ROUND(AVG(balance), 2) AS avg_bal_customers
FROM
    bank_customers;
    
-- Are churned customers more likely to have fewer products?

select  churn,
sum(products_number) as products
from bank_customers 
group by churn;

-- How many customers have a credit card, and what percentage of them churned?

SELECT 
    credit_card,
    COUNT(*) AS total_cc_cust,
    SUM(CASE
        WHEN churn = ' churned' THEN 1
        ELSE 0
    END) AS churned_customers,
    ROUND((SUM(CASE
                        WHEN churn = 'churned' THEN 1
                        ELSE 0
                    END) * 100) / COUNT(*),
            2) AS churned_percentage
FROM
    bank_customers
GROUP BY credit_card;

-- advnced

-- Is there a significant difference in churn rate between male and female customers?

SELECT 
    gender,
    COUNT(*) AS gender_wise_cust,
    SUM(CASE
        WHEN churn = 'churned_rate' THEN 1
        ELSE 0
    END) AS churn_rate,
    ROUND((SUM(CASE
                WHEN churn = 'churned_rate' THEN 1
                ELSE 0
            END) * 100) / COUNT(*),
            2) AS diff_churn_rate
FROM
    bank_customers
GROUP BY gender;

-- What is the churn rate for each country?

select 
country,
count(*) as country_cust,
(sum(case when churn = "churned" then 1 else 0 end)) as churned,
round((sum(case when churn = "churned" then 1 else 0 end) *100) / count(*),2) as churn_rate
from bank_customers
group by country;

-- Are there any countries with notably high or low churn rates?

select 
country,
count(*) as country_cust,
(sum(case when churn = "churned" then 1 else 0 end)) as churned,
round((sum(case when churn = "churned" then 1 else 0 end) *100) / count(*),2) as churn_rate
from bank_customers
group by country
order by churn_rate desc;

-- Are active members more likely to churn compared to inactive members?

select active_member,
count(*) as active_mem,
sum(case when churn='churned' then 1 else 0 end) as churned_customers,
round((sum(case when churn = "churned" then 1 else 0 end) *100) / count(*),2) as churn_percentage
from bank_customers
group by active_member;

-- Can you create a view or a new table that preprocesses the data for predictive modeling?

CREATE VIEW preprocessed_customer_data AS
SELECT
    customer_id,
    COALESCE(age, AVG(age) OVER ()) AS filled_age,
    CASE WHEN gender = 'Male' THEN 1 ELSE 0 END AS is_male,
    CASE WHEN gender = 'Female' THEN 1 ELSE 0 END AS is_female,
    CASE WHEN country = 'France' THEN 1 ELSE 0 END AS is_France,
    CASE WHEN country = 'Spain' THEN 1 ELSE 0 END AS is_spain,
    CASE WHEN country = 'Germany' THEN 1 ELSE 0 END AS is_Germany,
    CASE WHEN active_member = 'Active' THEN 1 ELSE 0 END AS is_active,
    CASE WHEN active_member = 'Inactive' THEN 1 ELSE 0 END AS is_inactive,
    CASE WHEN churn = 'Churned' THEN 1 ELSE 0 END AS churned
FROM 
    bank_customers;
    select * from  preprocessed_customer_data;





