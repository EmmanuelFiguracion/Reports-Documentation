# Bank Loan Analysis

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Recommendations](#recommendations)

### Project Overview
---

This data analysis project aims to provide exloratory analysis to assess the profitability of bank loan portfolios by analysing data related to loan application, funded amount, recieved amount(payment), interest rate and debt to income ratio.

![Summary](https://github.com/EmmanuelFiguracion/Reports-Documentation/blob/main/Report%20Summary_.PNG)
![Overview](https://github.com/EmmanuelFiguracion/Reports-Documentation/blob/main/Report%20Overview.PNG)
![Details](https://github.com/EmmanuelFiguracion/Reports-Documentation/blob/main/Report%20Details.PNG)



### Data Sources

Bank Loan: The primary dataset used for this analysis is the "financial_loan_final.csv" file, containing detailed information about the loan lend by the bank to its customer
- [Download here](https://github.com/EmmanuelFiguracion/Reports-Documentation/blob/main/financial_loan_final.csv)

### Tools

- Excel - Data Cleaning
- SQL Server - Data Analysis
- Looker Studio - Creating reports
   - [Looker Studio Report here](https://lookerstudio.google.com/s/mb-A78YTzTk)

### Data Cleaning/Preparation

In the initial data preparation phase, we performed the following tasks:
1. Data loading and inspection.
2. Data profilling.
3. Data cleaning and formatting.

### Exploratory Data Analysis

EDA Exploratory data analysis involved exploring the bank loan data to answer key questions, such as:
 - What is the Total, MTD and MoM for
	- Loan Application?
	- Funded Amount?
	- Amount Recieved?
	- Average Interest Rate?
	- Average Debt to income ratio?
	- Good Loan percentage vs Bad Loan?
	- Average Debt to income ratio?
 - What is Total Funded Amount trend by month in past two year
 - What is the Top 10 state for Funded Amount
 - Which employment lenght has the highest Funded Amount
 - What mostly is the loan purpose taking the loan
 - What term is the Funded Amount mostly
 - Who among the homeowners mostly taking the loan

### Data Analysis

In data analysis the values are further verified using SQL Server Management Studio to check the correctness of data prior applying to the reports

  - [SQL Scripts Download here](https://github.com/EmmanuelFiguracion/Reports-Documentation/blob/main/SQLQuery.sql)

```
-----Bank Loan Analysis---------SQL verification----------------------------------

--Total Loan Applications
SELECT COUNT(id) AS Total_Applications FROM bank_loan_data

--MTD Loan Applications
Select COUNT(id) AS Total_Applications FROM bank_loan_data
WHERE MONTH(issue_date) = 2;

--PMTD Loan Applications
SELECT COUNT(id) AS Total_Applications FROM bank_loan_data
WHERE MONTH(issue_date) = 1;

--Total Funded Amount
SELECT SUM(loan_amount) AS Total_Funded_Amount FROM bank_loan_data

--MTD Total Funded Amount
SELECT SUM(loan_amount) AS Total_Funded_Amount FROM bank_loan_data
WHERE MONTH(issue_date) = 2

--PMTD Total Funded Amount
SELECT SUM(loan_amount) AS Total_Funded_Amount FROM bank_loan_data
WHERE MONTH(issue_date) = 1

--Total Amount Received
SELECT SUM(total_payment) AS Total_Amount_Collected FROM bank_loan_data

--MTD Total Amount Received
SELECT SUM(total_payment) AS Total_Amount_Collected FROM bank_loan_data
WHERE MONTH(issue_date) = 2

--PMTD Total Amount Received
SELECT SUM(total_payment) AS Total_Amount_Collected FROM bank_loan_data
WHERE MONTH(issue_date) = 1

--Average Interest Rate
SELECT AVG(int_rate)*100 AS Avg_Int_Rate FROM bank_loan_data

--MTD Average Interest
SELECT AVG(int_rate)*100 AS MTD_Avg_Int_Rate FROM bank_loan_data
WHERE MONTH(issue_date) = 2

--PMTD Average Interest
SELECT AVG(int_rate)*100 AS PMTD_Avg_Int_Rate FROM bank_loan_data
WHERE MONTH(issue_date) = 1

--Avg DTI
SELECT AVG(dti)*100 AS Avg_DTI FROM bank_loan_data

--MTD Avg DTI
SELECT AVG(dti)*100 AS MTD_Avg_DTI FROM bank_loan_data
WHERE MONTH(issue_date) = 2

--PMTD Avg DTI
SELECT AVG(dti)*100 AS PMTD_Avg_DTI FROM bank_loan_data
WHERE MONTH(issue_date) = 1



--GOOD LOAN ISSUED-----------------------------------------------------------


--Good Loan Percentage
SELECT
    (COUNT(CASE WHEN loan_status = 'Fully Paid' OR loan_status = 'Current' THEN id END) * 100.0) / 
	COUNT(id) AS Good_Loan_Percentage
FROM bank_loan_data

--Good Loan Applications
SELECT COUNT(id) AS Good_Loan_Applications FROM bank_loan_data
WHERE loan_status = 'Fully Paid' OR loan_status = 'Current'

--Good Loan Funded Amount
SELECT SUM(loan_amount) AS Good_Loan_Funded_amount FROM bank_loan_data
WHERE loan_status = 'Fully Paid' OR loan_status = 'Current'

--Good Loan Amount Received
SELECT SUM(total_payment) AS Good_Loan_amount_received FROM bank_loan_data
WHERE loan_status = 'Fully Paid' OR loan_status = 'Current'


--Bad Loan Percentage
SELECT
    (COUNT(CASE WHEN loan_status = 'Charged Off' THEN id END) * 100.0) / 
	COUNT(id) AS Bad_Loan_Percentage
FROM bank_loan_data


--Bad Loan Applications
SELECT COUNT(id) AS Bad_Loan_Applications FROM bank_loan_data
WHERE loan_status = 'Charged Off'

--Bad Loan Funded Amount
SELECT SUM(loan_amount) AS Bad_Loan_Funded_amount FROM bank_loan_data
WHERE loan_status = 'Charged Off'


--Bad Loan Amount Received
SELECT SUM(total_payment) AS Bad_Loan_amount_received FROM bank_loan_data
WHERE loan_status = 'Charged Off'


--LOAN STATUS----------------------------------------------------------------------
	SELECT
        loan_status,
        COUNT(id) AS LoanCount,
        SUM(total_payment) AS Total_Amount_Received,
        SUM(loan_amount) AS Total_Funded_Amount,
        AVG(int_rate * 100) AS Interest_Rate,
        AVG(dti * 100) AS DTI
    FROM
        bank_loan_data
    GROUP BY
        loan_status;

SELECT 
	loan_status, 
	SUM(total_payment) AS MTD_Total_Amount_Received, 
	SUM(loan_amount) AS MTD_Total_Funded_Amount 
FROM bank_loan_data
WHERE MONTH(issue_date) = 2 
GROUP BY loan_status;


--	BANK LOAN REPORT | OVERVIEW----------------------------------
MONTH
SELECT 
	MONTH(issue_date) AS Month_Munber, 
	DATENAME(MONTH, issue_date) AS Month_name, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM bank_loan_data
GROUP BY MONTH(issue_date), DATENAME(MONTH, issue_date)
ORDER BY MONTH(issue_date)


---STATE------------------------------------------------
SELECT 
	address_state AS State, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM bank_loan_data
GROUP BY address_state
ORDER BY address_state

--TERM-------------------------------------------------------
SELECT 
	term AS Term, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM bank_loan_data


---EMPLOYEE LENGTH----------------------------------------------
SELECT 
	emp_length AS Employee_Length, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM bank_loan_data
GROUP BY emp_length
ORDER BY emp_length


-----PURPOSE-------------------------------------------------------
SELECT 
	purpose AS PURPOSE, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM bank_loan_data
GROUP BY purpose
ORDER BY purpose


------HOME OWNERSHIP---------------------------------------------------
SELECT 
	home_ownership AS Home_Ownership, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM bank_loan_data
GROUP BY home_ownership
ORDER BY home_ownership


SELECT 
	purpose AS PURPOSE, 
	COUNT(id) AS Total_Loan_Applications,
	SUM(loan_amount) AS Total_Funded_Amount,
	SUM(total_payment) AS Total_Amount_Received
FROM bank_loan_data
WHERE grade = 'A'
GROUP BY purpose
ORDER BY purpose

```

### Results/Findings

The analysis results are summarized as follows:
1. The Bank overall loan profit have been steadily increasing over the past year
2. People who taken more loan are primary use for debt consolidation and credit card use in mortgage.
3. Customer who has longest job tenure has most likely to take the loan and not get default
4. CA>NY>TX>FL are the top 4 State contributes to the higher loan amount.

### Recommendations

Based on the analysis, we recommend the following actions:
- Connect/offer a person who belong to category A that have completed or nearly completed his loan to take another loan with lower or same interest rate.
- Focus on promoting loans in other cities with lower funding amount



