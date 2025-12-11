# End-to-end analytics project using N8N, PostgreSQL, and Quadratic to solve real-world supply chain challenges.

## 📋 Table of Contents

* Overview
* Tech Stack
* Architecture
* Quick Start
* Database Setup
* N8N Automation
* Quadratic Analytics
* Supply Chain KPIs
* Troubleshooting

## 📖 Project Overview

### Case Study: Atlart Organic Foods

* Gujarat-based organic food manufacturer with operations in India and New Jersey
* Challenge: Inventory optimization and order management issues
* Solution: AI-powered analytics pipeline with automated data ingestion

## 🏗️ Technical Architecture

1. Data Source: CSV files received via Email (Gmail).
2. Automation/ETL: n8n (AI Agentic Workflow Automation) triggers on new emails, processes CSVs, and cleans data.
3. Storage: PostgreSQL (hosted on Supabase) stores the raw fact and dimension tables.
4. Analytics & UI: Quadratic (AI-Powered Spreadsheet) connects to the database, runs Python for data transformation, and generates insights/charts via AI prompts.

## 🛠️ Tech Stack

* N8N - Workflow automation for data extraction from emails
* PostgreSQL (Supabase) - Cloud-hosted database
* Quadratic - AI-powered spreadsheet for analytics
* OpenExchangeRates API - Currency conversion

## Flow

<img width="1467" height="407" alt="image" src="https://github.com/user-attachments/assets/918c7fa3-ba69-40db-a6c3-eb26cb5c3b7c" />

## Quick Start

### Prerequisites

1. Download CSV files from project resources
2. Create accounts:
   * N8N - 14-day free trial
   * Supabase - Free tier
   * Quadratic - Free credits
   * OpenExchangeRates - Free API key


### Data Files

Send two emails to your inbox with attachments:

* Email 1: "Daily Sales India" + India CSV files
* Email 2: "Daily Sales USA" + USA CSV files

## Database Setup

1. Create Supabase Project

        # Navigate to Supabase → Create New Project
        # Note connection details:
        Host: [your-project].supabase.co
        Port: 5432
        Database: postgres
        User: postgres
        Password: [your-password]

### 2. Create Tables

Dimension Tables:

* dim_customers - Customer information
* dim_products - Product catalog with INR/USD prices
* dim_target_orders - Sales targets

### Fact Tables:

* fact_orders_aggregate - Consolidated order data
* fact_order_line_items - Detailed line-level data

<img width="1907" height="865" alt="image" src="https://github.com/user-attachments/assets/bbc16ffa-e9a4-45ab-980b-45fa8b96634f" />
  

### N8N Automation

1. 1. Gmail Trigger Setup

           Node: Gmail → On Message Received
          Config:
            - Frequency: Every 1 minute
            - Label: Inbox
            - Subject Contains: "daily sales"
            - Options: ✓ Access attachments

2. Data Extraction & Loading

For Each CSV File:

       Extract Node:
        - Type: CSV
        - Output: JSON
      
      PostgreSQL Node:
        - Operation: Insert rows
        - Table: fact_orders_aggregate / fact_order_line_items

###  3. Critical Date Conversion

    // Convert dates to ISO format
    {{$json.order_placement_date.toDateTime("dd/MM/yyyy").format("yyyy-MM-dd")}}


### 4. Test & Activate

    -- Verify data
    SELECT MAX(order_placement_date) FROM fact_orders_aggregate;
    
    -- Delete test records if needed
    DELETE FROM fact_orders_aggregate WHERE order_placement_date > '2025-05-16';


<img width="1916" height="802" alt="image" src="https://github.com/user-attachments/assets/5fc447a0-28df-4dbc-97a0-d26db955b83b" />

## Quadratic Analytics

### 1. Connect to PostgreSQL

    Connection Details:
      - Name: AtlartMart
      - Host: [Supabase host]
      - Port: 5432
      - Database: postgres
      - User: postgres
      - Password: [Supabase password]

### 2. Import Tables

#### Create sheets and query each table (remove LIMIT clause):     

* dim_customers
* dim_products
* dim_target_orders
* fact_order_line
* fact_orders_aggregate

### 3. Create Date Dimension

    # Prompt in dim_date sheet:
    Create a date table with dates from March 1, 2025 to March 31, 2025.
    Include columns for: date, year, month, day_of_week, month_name

### 4. Generate Exchange Rates

    # Prompt in exchange_rates sheet:
    Create an exchange rate table from March 1, 2025 to March 31, 2025
    using the Open Exchange Rates API.
    API key: YOUR_APP_ID
    Get USD to INR conversion rates.
    Round to 4 decimal places.

### 5. Create Fact Summary

    # Prompt in fact_summary sheet:
    Load data from fact_order_line, fact_orders_aggregate, dim_customers, 
    dim_products, and exchange_rates.
    
    Clean data:
    - Convert product_id and customer_id to numeric
    - Remove whitespaces
    - Convert dates to datetime
    
    Merge tables on appropriate keys.
    
    Calculate:
    - total_amount_inr = order_quantity × inr_price
    - total_amount_usd = order_quantity × usd_price
    - converted_amount = total_amount_inr / exchange_rate

### Supply Chain Analytics & KPIs

We will use AI prompts to calculate standard Supply Chain metrics.

<img width="827" height="632" alt="image" src="https://github.com/user-attachments/assets/8fcd5c9b-bbf1-4481-85a7-40737a08615c" />

## Calculate KPIs with AI

    # Prompt in KPIs sheet:
    Calculate the following supply chain KPIs:
    - Total Orders
    - Total Order Lines
    - Line Fill Rate percentage
    - Volume Fill Rate percentage
    - On-Time Delivery percentage
    - In-Full Delivery percentage
    - OTIF (On-Time In-Full) percentage
    
    Use fact_order_line for line-level metrics,
    fact_orders_aggregate for order-level metrics.

### Important Concepts

#### Orders vs Order Lines:

    Order 1: 5 keyboards           → 1 order, 1 line
    Order 2: 3 keyboards + 2 mice  → 1 order, 2 lines
    Total:                         → 2 orders, 3 lines

### OTIF Calculation:

    OTIF = (Orders On-Time AND In-Full) / Total Orders × 100

    Critical Rule: Even if 199/200 lines are perfect, 
    missing 1 line fails the entire order.
    
    Industry Benchmark: 85-90% is good performance

### Business Analysis Examples

#### Monthly Performance by City

    Show me monthly on-time performance by cities.
    Create a chart visualization.

 ### Top Customers

       Show me the top 5 customers based on order value and their
    on-time percentage, in-full percentage, and OTIF percentage.
    Include customer name, customer ID, and city.

 <img width="1910" height="863" alt="image" src="https://github.com/user-attachments/assets/2f8b1466-f202-4fa0-b7f5-c0eae7b61864" />


 <img width="1918" height="877" alt="image" src="https://github.com/user-attachments/assets/b1b96801-67bc-4b04-bcaa-09a3223f9e3b" />

 ### Country-Specific Analysis

     Show me top 5 customers in India based on order value and their
    on-time percentage, in-full percentage, and OTIF percentage.
    Include customer name, customer ID, and city.

### Troubleshooting

#### N8N Issues

* ✓ Verify Gmail authentication is active
* ✓ Check subject line filter matches emails
* ✓ Ensure workflow is activated
* ✓ Test with "Fetch Test Event"

### Date Format Errors

    // Always use explicit format
    {{$json.date_field.toDateTime("dd/MM/yyyy").format("yyyy-MM-dd")}}

 ### PostgreSQL Connection

* ✓ Verify Supabase credentials
* ✓ Confirm port is 5432
* ✓ Test connection in Supabase dashboard

### Quadratic Data Mismatch

* ✓ Remove LIMIT clauses from queries
* ✓ Refresh data connections
* ✓ Use correct tables (aggregate vs line)

### KPI Validation

    # Always review generated code
    # Verify:
    # - Uses correct table (order vs line level)
    # - Formula matches business definition
    # - Data types are correct

## Best Practices

### 1. Always Validate

* Review AI-generated Python code
* Check calculations match business logic
* Verify data transfers between systems
* Understand which table to use (order vs line level)

## 2. Domain Knowledge Matters

### AI writes code, but you must:

* Validate correctness
* Understand supply chain concepts
* Guide AI with proper context
* Catch errors before deployment

### 3. Fundamentals Still Essential

* Python/SQL - Read and modify generated code
* Data Analysis - Validate transformations
* Supply Chain - Know the business context  

## Project Checklist

### Setup:

*  Create all accounts (N8N, Supabase, Quadratic, OpenExchangeRates)
*  Send test emails with CSV files
*  Configure PostgreSQL database

### Automation:

*  Set up N8N Gmail trigger
*   Configure data extraction nodes
*   Map fields to PostgreSQL tables
*   Test and activate workflow
  
### Analytics

*  Connect Quadratic to PostgreSQL
*  Import all dimension and fact tables
*   Create date and exchange rate tables
*   Generate fact summary table
*   Calculate and validate KPIs

 ### Analysis:

* Create performance visualizations
* Analyze top customers
* Generate country-specific reports
* Complete practice exercises

## Key Takeaways

✅ Automation eliminates manual data entry
✅ Cloud infrastructure provides scalability
✅ AI-powered analysis accelerates insights
✅ Domain expertise validates results and drives decisions


### Remember: AI tools multiply productivity, but fundamentals in coding, data analysis, and domain knowledge remain essential for validation and career growth.

##  📬 Contacts  

[![Email](https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:connectingsrl@gmail.com)  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/sahajahanur-laskar/)











   















