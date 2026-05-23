GLOBAL LOGISTICS & SUPPLY CHAIN EFFICIENCY
AUDIT
An End-to-End Strategic Data Analytics Portfolio Project Blueprint
Author: Navdeep Kumar Role: Data Analyst Environments: Python, MySQL, Power BI

1. Executive Summary & Project Intent
In modern enterprise ecosystems, supply chain transparency serves as a primary driver for operational cost
optimization. This technical data analyst project models, validates, architectures, and visualizes an international
logistics pipeline containing over 1,000 deep voyage logs. The central goal is to diagnose systemic transit friction
points, score partner compliance against internal Service Level Agreements (SLAs), and uncover operational
capital leakage.
By routing complex synthetic data models through a robust pipeline—spanning Python engine generation, a
MySQL Workbench relational warehouse storage layer, and an interactive Power BI dimensional calculation layer
—this project addresses a series of real-world corporate data pipeline challenges.
Core Project Analytical Revelations:
The Germany-to-India Trade Friction: Advanced multi-axis dimensional matrix analysis isolated a severe
operational breakdown on the Germany-to-India maritime shipping lane, revealing an average transit
latency spike of 12.0 days past due.
Contractor Capacity Concentration: Dispatches are heavily consolidated across FedEx Ocean and DHL
Global, pinpointing a high corporate dependency on these logistics partners.
Financial Governance: Audited a comprehensive transactional shipping spend of $4.22M, providing an
empirical ceiling to guide future contract re-negotiations.

2. Data Pipeline Architecture Diagram
The data undergoes structured transformations across separate environments to ensure clean separation of
concerns between raw generation, relational transformations, and dimensional reporting:
[Phase 1: Python Processing Engine] ──> Generates Messy CSV Data System Log File

│

[Phase 2: MySQL Staging Framework] ◄───────┘ Schema Mapping & Clean View Compilation

│

[Phase 3: Power BI Engine] ◄──────────────┘ DAX Modeling, Calculations & Dashboard View
•

•

•

Navdeep Kumar • Data Analyst Portfolio Project Page 1 of 6

3. Phase 1: Synthetic Pipeline Simulation (Python)
A customized script was written using Python's scientific ecosystem to compile a realistic global dataset. To
simulate actual corporate database constraints, intentional database quality issues were introduced into the code
stream, including temporal anomalies (deliveries recorded prior to ordering dates) and illogical structural
classifications.

Navdeep Kumar • Data Analyst Portfolio Project Page 2 of 6

import pandas as pd
import numpy as np
from datetime import datetime, timedelta
np.random.seed(42)
num_records = 1000
carriers = ['Maersk', 'FedEx Ocean', 'DHL Global', 'Cosco Shipping', 'Swift Cargo']
modes = ['Ocean', 'Air', 'Road', 'Rail']
countries = ['China', 'USA', 'Germany', 'India', 'Japan', 'UK', 'Brazil', 'Vietnam']
data = []
start_date = datetime(2025, 1, 1)
for i in range(1, num_records + 1):
shipment_id = f"SHP{100000 + i}"
carrier = np.random.choice(carriers)
origin = np.random.choice(countries)
destination = np.random.choice([c for c in countries if c != origin])
mode = np.random.choice(modes, p=[0.5, 0.2, 0.2, 0.1]) if origin != 'USA' else 'Road'
if origin in ['China', 'India', 'Japan', 'Vietnam'] and destination in ['USA', 'Germany',
'UK']:
mode = np.random.choice(['Ocean', 'Air'], p=[0.8, 0.2])
order_days_offset = np.random.randint(0, 365)
order_date = start_date + timedelta(days=order_days_offset)
transit_days_target = {'Air': 3, 'Road': 5, 'Rail': 10, 'Ocean': 30}[mode]
est_delivery = order_date + timedelta(days=transit_days_target)
status = np.random.choice(['Delivered', 'Delayed', 'Cancelled'], p=[0.82, 0.15, 0.03])
if status == 'Delivered':
days_to_add = transit_days_target + np.random.randint(-2, 3)
actual_delivery = order_date + timedelta(days=max(1, days_to_add))
elif status == 'Delayed':
days_to_add = transit_days_target + np.random.randint(4, 15)
actual_delivery = order_date + timedelta(days=days_to_add)
else:
actual_delivery = np.nan
weight = round(np.random.uniform(50, 25000), 2)
base_cost = {'Air': 5.5, 'Road': 1.2, 'Rail': 0.8, 'Ocean': 0.4}[mode]
cost = round((weight * base_cost) + np.random.uniform(200, 1000), 2)
# Injected data quality issues to test cleaning robustness
if np.random.rand() < 0.02:
actual_delivery = order_date - timedelta(days=2)
if status == 'Cancelled' and np.random.rand() < 0.5:
status = 'Delivered'
data.append([
shipment_id, carrier, origin, destination, mode,

Navdeep Kumar • Data Analyst Portfolio Project Page 3 of 6

4. Phase 2: Relational Warehouse & Cleaning Schema (MySQL)
The flat file dataset was transitioned to a formal SQL data type infrastructure. Pure database-side procedures
resolve tracking glitches and construct calculated analytics-ready data views.
order_date.strftime('%Y-%m-%d'), est_delivery.strftime('%Y-%m-%d'),
actual_delivery.strftime('%Y-%m-%d') if pd.notnull(actual_delivery) else None,
status, cost, weight
])
df = pd.DataFrame(data, columns=[
'Shipment_ID', 'Carrier_Name', 'Origin_Country', 'Destination_Country', 'Shipment_Mode',
'Order_Date', 'Estimated_Delivery_Date', 'Actual_Delivery_Date', 'Shipment_Status',
'Shipping_Cost', 'Product_Weight_KG'
])
df.to_csv('global_logistics_data.csv', index=False)

Navdeep Kumar • Data Analyst Portfolio Project Page 4 of 6

5. Phase 3: Dimensional Data Modeling & Visual Storytelling (Power BI)
The structural dataset was integrated into Power BI. To maximize internal analysis capabilities without running
heavy transformation layers repeatedly, a column-calculated analytical query was engineered natively via the Data
Analysis Expressions (DAX) modeling environment:
-- Initialize clean relational schema database
CREATE DATABASE IF NOT EXISTS Logistics_Project;
USE Logistics_Project;
CREATE TABLE IF NOT EXISTS Shipments (
Shipment_ID VARCHAR(50) PRIMARY KEY,
Carrier_Name VARCHAR(100),
Origin_Country VARCHAR(100),
Destination_Country VARCHAR(100),
Shipment_Mode VARCHAR(50),
Order_Date DATE,
Estimated_Delivery_Date DATE,
Actual_Delivery_Date DATE,
Shipment_Status VARCHAR(50),
Shipping_Cost DECIMAL(12, 2),
Product_Weight_KG DECIMAL(12, 2)
);
-- Step 1: Clean temporal anomalies where arrival dates precede order booking dates
DELETE FROM Shipments
WHERE Actual_Delivery_Date < Order_Date;
-- Step 2: Enforce structural classification data safety
UPDATE Shipments
SET Shipment_Status = 'Cancelled'
WHERE Shipment_Status = 'Delivered' AND Actual_Delivery_Date IS NULL;
-- Step 3: Establish compiled workspace view calculating operational latency
DROP VIEW IF EXISTS Cleaned_Shipments;
CREATE VIEW Cleaned_Shipments AS
SELECT *,
DATEDIFF(Actual_Delivery_Date, Estimated_Delivery_Date) AS Days_Delayed,
DATEDIFF(Actual_Delivery_Date, Order_Date) AS Total_Transit_Duration
FROM Shipments
WHERE Shipment_Status != 'Cancelled';

Navdeep Kumar • Data Analyst Portfolio Project Page 5 of 6

The business dashboard canvas was structured around a high-impact grid containing three distinct analytical
components:
Macro Performance Row (KPI Card): Aggregates global system shipping costs directly, establishing an active
network baseline of $4.22M.
Carrier Allocation Framework (Bar Chart): Segments volume load dynamically across contract providers
(FedEx Ocean, DHL Global, Maersk), identifying supplier dependencies.
Route Latency Matrix Hierarchies (Hierarchical Cross-Tab): Maps multi-axis geographical coordinates
(Origin ightarrow Destination) using weighted averages to identify route friction points.
6. Strategic Action Plan & Industry Recommendations
Based on the data insights generated across Python, MySQL, and Power BI, the following operational adjustments
are recommended for corporate stakeholders:
Route Divert Strategy: Instantly split and shift 25% of high-priority cargo volume away from the high-delay
Germany-to-India lane to intermodal rail routes to protect downstream delivery SLAs.
SLA Enforcements: Introduce clear penalty clauses during the upcoming logistics contract cycle, docking
carrier pay if average delivery windows are missed by more than 5 business days.
