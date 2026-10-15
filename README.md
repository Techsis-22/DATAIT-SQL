### DATAIT-SQL
---------------------------------------------

The DATAIT SQL houses my sql queries for my final project during the cohort 5 period where i solved 10 problems the FCMG department faces with respect to their sales department and also ensured that the sales rose from 15% to about 75% profit margin

------------------------------------------
The dataset used for this project was created manually using sql scripts which led to the creation of five (5) tables, namely: Agents, Retailers, SalesOrders, SalesOrderLines, Payments. 


Here is a detailed, markdown-formatted schema documentation for your GitHub project repository. You can copy and paste this directly into your `README.md` file or save it as a separate `DATABASE_SCHEMA.md` file and link it.

---

# Database Schema Documentation

This document provides a detailed breakdown of the tables, columns, constraints, and data types used in the sales and distribution database. The database is designed to manage field agents, retail customers, sales orders, itemized order lines, and payment transactions.

---

## 1. `dbo.Agents`

Stores information about sales representatives, field personnel, and regional managers.

| Column Name | Data Type | Constraints / Attributes | Description |
| --- | --- | --- | --- |
| `AgentID` | `INT` | `IDENTITY(1,1)`, `PRIMARY KEY` | Unique surrogate key identifying each agent. |
| `AgentCode` | `VARCHAR(20)` | `NOT NULL`, `UNIQUE` | Unique business code or employee identifier for the agent. |
| `FullName` | `VARCHAR(120)` | `NOT NULL` | The full name of the sales agent or field representative. |
| `Phone` | `VARCHAR(20)` | `NULL` | Contact telephone number for the agent. |
| `HomeState` | `VARCHAR(60)` | `NULL` | The home state where the agent resides or operates from. |
| `HomeCity` | `VARCHAR(80)` | `NULL` | The home city of the agent. |
| `Role` | `VARCHAR(40)` | `NOT NULL` | The job designation or role (e.g., Sales Agent, City Head). |
| `HireDate` | `DATE` | `NOT NULL` | The date the agent was officially hired or onboarded. |
| `Status` | `VARCHAR(20)` | `NOT NULL` | Current operational status (e.g., Active, Suspended, Exited). |

---

## 2. `dbo.Retailers`

Stores profiles of retail stores, supermarkets, pharmacies, and commercial partners.

| Column Name | Data Type | Constraints / Attributes | Description |
| --- | --- | --- | --- |
| `RetailerID` | `INT` | `IDENTITY(1,1)`, `PRIMARY KEY` | Unique surrogate key identifying each retailer. |
| `BusinessName` | `VARCHAR(150)` | `NOT NULL` | The registered trading name or store name of the business. |
| `OwnerName` | `VARCHAR(120)` | `NULL` | Full name of the business owner or store manager. |
| `Phone` | `VARCHAR(20)` | `NULL` | Primary business or owner contact phone number. |
| `Channel` | `VARCHAR(40)` | `NOT NULL` | The trade channel classification (e.g., Retail, HoReCa, Supermarket, Pharmacy). |
| `PaymentPreference` | `VARCHAR(30)` | `NOT NULL` | The preferred transaction settlement method (e.g., Cash, Transfer, Wallet, POS). |
| `CreditLimit` | `DECIMAL(18,2)` | `NOT NULL`, `DEFAULT(0)` | Maximum credit line extended to the retailer. |
| `StateName` | `VARCHAR(80)` | `NOT NULL` | State location where the retail store is situated. |
| `CityName` | `VARCHAR(80)` | `NOT NULL` | City location where the retail store is situated. |
| `AddressLine` | `VARCHAR(200)` | `NULL` | Physical street address or landmark for delivery. |
| `CreatedAt` | `DATE` | `NOT NULL` | The date the retailer profile was registered in the system. |

---

## 3. `dbo.SalesOrders`

Tracks header-level details for orders placed by retailers, including fulfillment and agent allocation.

| Column Name | Data Type | Constraints / Attributes | Description |
| --- | --- | --- | --- |
| `OrderID` | `INT` | `IDENTITY(1,1)`, `PRIMARY KEY` | Unique surrogate key identifying each sales order. |
| `OrderNumber` | `VARCHAR(30)` | `NOT NULL`, `UNIQUE` | Human-readable unique identifier or transaction reference for tracking. |
| `RetailerID` | `INT` | `NOT NULL`, `FOREIGN KEY` | References `RetailerID` in the `Retailers` table (places the order). |
| `AgentID` | `INT` | `NULL`, `FOREIGN KEY` | References `AgentID` in the `Agents` table (manages/secures the order; nullable to support unassigned orders). |
| `OrderDate` | `DATETIME2` | `NOT NULL` | Exact timestamp when the sales order was placed. |
| `FulfillmentType` | `VARCHAR(20)` | `NOT NULL` | Method of fulfillment (e.g., Delivery, SelfPickup). |
| `StockPoint` | `VARCHAR(120)` | `NOT NULL` | Origin warehouse, hub, or inventory stock point (e.g., OmniHub Agege Lagos - Deobell). |
| `Status` | `VARCHAR(20)` | `NOT NULL` | Current lifecycle status (e.g., Created, Confirmed, Packed, Dispatched, Delivered, Cancelled). |
| `CancelReason` | `VARCHAR(200)` | `NULL` | Explanation or reason logged if the order status is cancelled. |
| `NetAmount` | `DECIMAL(18,2)` | `NOT NULL`, `DEFAULT(0)` | Total net financial value of the order. |
| `LastUpdatedAt` | `DATETIME2` | `NULL` | Timestamp marking the most recent modification or status change. |

*Indexes:*

* `IX_SalesOrders_OrderDate` on `OrderDate` for time-range reporting.
* `IX_SalesOrders_Status` on `Status` for filtering active vs. completed workflows.

---

## 4. `dbo.SalesOrderLines`

Itemized line details associated with each sales order, breaking down quantities and individual pricing per Stock Keeping Unit (SKU).

| Column Name | Data Type | Constraints / Attributes | Description |
| --- | --- | --- | --- |
| `LineID` | `INT` | `IDENTITY(1,1)`, `PRIMARY KEY` | Unique surrogate key for the order line item. |
| `OrderID` | `INT` | `NOT NULL`, `FOREIGN KEY` | References `OrderID` in the `SalesOrders` table (parent order). |
| `SKUCode` | `VARCHAR(30)` | `NOT NULL` | Stock Keeping Unit code representing the product. |
| `SKUName` | `VARCHAR(200)` | `NOT NULL` | Descriptive name of the product or SKU. |
| `Category` | `VARCHAR(80)` | `NOT NULL` | Product category or department. |
| `UOM` | `VARCHAR(20)` | `NOT NULL` | Unit of Measure for the quantity ordered (e.g., Carton, Pack, Piece). |
| `Qty` | `INT` | `NOT NULL` | Number of units ordered. |
| `UnitPrice` | `DECIMAL(18,2)` | `NOT NULL` | Price per single unit of the SKU at the time of sale. |
| `LineTotal` | `DECIMAL(18,2)` | `PERSISTED COMPUTED` | Automatically calculated column (`Qty * UnitPrice`). |

*Indexes:*

* `IX_OrderLines_OrderID` on `OrderID` to optimize joins with the `SalesOrders` table.

---

## 5. `dbo.Payments`

Records financial transactions and payment settlements linked to sales orders.

| Column Name | Data Type | Constraints / Attributes | Description |
| --- | --- | --- | --- |
| `PaymentID` | `INT` | `IDENTITY(1,1)`, `PRIMARY KEY` | Unique surrogate key for the payment record. |
| `OrderID` | `INT` | `NOT NULL`, `FOREIGN KEY` | References `OrderID` in the `SalesOrders` table being paid for. |
| `PaymentDate` | `DATETIME2` | `NOT NULL` | Exact timestamp when the payment transaction occurred. |
| `Amount` | `DECIMAL(18,2)` | `NOT NULL` | Monetary value paid in the transaction. |
| `Method` | `VARCHAR(20)` | `NOT NULL` | Mode of payment used (e.g., Cash, Transfer, Wallet, POS). |
| `Reference` | `VARCHAR(60)` | `NOT NULL`, `UNIQUE` | Unique transaction reference code or payment gateway confirmation ID. |
| `Status` | `VARCHAR(20)` | `NOT NULL` | Settlement status (e.g., Pending, Confirmed, Reversed). |
| `CreatedAt` | `DATETIME2` | `NOT NULL`, `DEFAULT(SYSDATETIME())` | System timestamp when the payment record was inserted. |

*Indexes:*

* `IX_Payments_OrderID` on `OrderID` for fast lookup of payments associated with specific orders.

---





---------------------------------------------
DataIT Beginner SQL Course — FMCG Distribution Dataset (SQL Server)
Tables: Agents, Retailers, SalesOrders, SalesOrderLines, Payments
Includes: seed data generator (70+ rows per table)
NOTE: Updated to use a 15-SKU catalog + UOM fix (SKUs appear across multiple UOMs).

--------------------------------------------
