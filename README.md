# Data-Entry
# DATA ENTRY PROJECT README

## Project Overview

**Project Name:** Nexora Commerce & Logistics - Multi-Source Data Consolidation  
**Objective:** Transform unstructured, messy business data from 6 different sources into clean, structured datasets ready for SQL analysis and business intelligence reporting.  
**Data Sources:** Invoice text files, customer emails, WhatsApp chat logs, JSON API exports, logistics tracking logs, and support ticket records.

---

## 📋 Table of Contents

- [Key Decision: Multi-Dataset Architecture](#key-decision-multi-dataset-architecture)
- [File 1: Invoices](#file-1-invoices-file_1_invoicestxt)
- [File 2: Emails](#file-2-emails-file_2_emailstxt)
- [File 3: WhatsApp](#file-3-whatsapp-file_3_whatsapptxt)
- [File 4: API JSON](#file-4-api-json-file_4_api_jsonjson)
- [File 5: Logistics](#file-5-logistics-file_5_logisticstxt)
- [File 6: Support Tickets](#file-6-support-tickets-file_6_support_ticketstxt)
- [Data Quality & Cleaning Standards](#data-quality--cleaning-standards)
- [Final Dataset Summary](#final-dataset-summary)
- [SQL-Ready Structure](#sql-ready-structure)
- [Key Learnings & Best Practices](#key-learnings--best-practices)
- [Next Steps: SQL Analysis Pipeline](#next-steps-sql-analysis-pipeline)
- [Tools Used](#tools-used)
- [Project Metadata](#project-metadata)

---

## 🎯 Key Decision: Multi-Dataset Architecture

### Strategic Choice
Each file was converted into a **separate dataset** due to fundamental differences in data structure and business purpose.

### Rationale

- **Invoices** = Transactional sales data (product-level granularity)
- **Emails** = Unstructured customer communication (request-level tracking)
- **WhatsApp** = Message-by-message communication logs (event-level tracking)
- **API JSON** = Structured order data with nested objects (order-line detail)
- **Logistics** = Shipment tracking events (delivery lifecycle tracking)
- **Support Tickets** = Issue resolution workflow (ticket-level tracking)

Each dataset was labeled according to its distinct structure and optimized for SQL querying, joining, and analysis.

---

## 📄 FILE 1: INVOICES (`FILE_1_INVOICES.txt`)

### Data Entry Process

#### Step 1: Structure Identification
- Analyzed invoice text format (OCR-style semi-structured data)
- Identified key fields: `Invoice_ID`, `Customer_Name`, `Order_Date`, `Product_Name`, `Quantity`, `Unit_Price`, `Line_Total`, `Order_Total`, `Address`, `Data_Quality_Notes`

#### Step 2: Form Creation
- Manually inputted the first three invoice records
- Used this initial data to create an Excel Form for faster data entry
- Form fields mapped directly to column headers

#### Step 3: Data Format Recognition
- Identified mixed date formats (American MM-DD-YYYY, European DD/MM/YYYY, ISO YYYY-MM-DD, text formats)
- Recognized data represented global sales across multiple countries despite American formatting tendencies
- Decided to preserve raw dates during entry for cleaning phase

#### Step 4: Bulk Data Entry
- Used the form to input remaining 22 invoices
- **Result:** Significantly reduced work time and increased data input speed vs. manual cell-by-cell entry

#### Step 5: Manual Corrections
Manually edited information that Excel auto-formatting couldn't handle:
- OCR errors (e.g., `Cust0mer` → `Customer`, `12,O00` → `12,000`)
- Incomplete addresses
- Calculation errors in totals

#### Step 6: Data Cleaning & Export
- Manually fixed mismatched date formats
- Used `=PROPER()` formula to standardize customer names (e.g., `sarah CHEN` → `Sarah Chen`)
- Used `=TRIM()` to remove extra spaces
- Validated calculation errors (flagged in `Data_Quality_Notes` column)
- Exported clean dataset

#### Key Design Decision
Multi-item invoices entered as **multiple rows** (one row per product line) sharing the same `Invoice_ID` to enable product-level analysis in SQL.

---

## 📧 FILE 2: EMAILS (`FILE_2_EMAILS.txt`)

### Data Entry Process

#### Step 1: Structure Definition
**Columns:** `Email_Subject`, `Customer_Name`, `Order_Type`, `Order_ID`, `Product`, `Address`, `Full_Email_Content`

#### Step 2: Form Creation
- Manually inputted the first three email records
- Created Excel Form based on initial entries

#### Step 3: Bulk Data Entry
- Used form to input remaining 17 emails
- Standardized `Order_Type` categories:
  - New Order
  - Refund Request
  - Complaint
  - Inquiry
  - Cancellation
  - Address Change
  - Quality Issue

#### Step 4: Data Cleaning
- Standardized customer names using `=PROPER()`
- Extracted `Order_IDs` where mentioned using text search
- Cleaned and exported dataset

---

## 💬 FILE 3: WHATSAPP (`FILE_3_WHATSAPP.txt`)

### Data Entry Process

#### Step 1: Entry Method Confirmation
**Critical Decision:** Confirmed that **one message per row** was the industry-standard best practice for chat data entry

**Rationale:** Facilitates proper SQL analysis for response time calculations, message frequency analysis, and sentiment tracking

#### Step 2: Manual Entry Strategy
**Chose NOT to use forms** because:
- Drop-down shortcuts for `Role` (Customer/Support) were faster with direct cell entry
- Copy-paste efficiency was higher without form interface
- Timestamp patterns made cell autofill more practical

#### Step 3: Column Structure
**Columns:** `Timestamp`, `Customer Name`, `Support Name`, `Message`, `Product Mentioned`, `Order Status`


#### Step 4: Bulk Data Entry
- Manually inputted all 101 message records
- Used Excel autofill for repetitive names (Marcus, James, Sarah, etc.)
- Applied data validation dropdown for `Customer/Support` column

#### Step 5: Final Cleaning
- Standardized timestamp format to `YYYY-MM-DD HH:MM`
- Verified Role assignments
- Ensured message text preserved original abbreviations and tone (`u`, `pls`, `2day`)
- Exported dataset

---

## 🔌 FILE 4: API JSON (`FILE_4_API_JSON.json`)

### Data Entry Process

#### Step 1: Structure Analysis
Identified nested JSON structure with inconsistent key naming:
- Some records: `customerName`, others: `customer_name`
- Some records: `delivery.date`, others: `delivery_date`
- Extracted 12 distinct fields from JSON objects

#### Step 2: Enhanced Schema Design
**Added 3 extra columns** not present in original JSON:
- `tracking_id` - For linking to logistics data
- `refunded` - TRUE/FALSE flag (some records had this, standardized for all)
- `disputed` - TRUE/FALSE flag (for order disputes)

**Rationale:** These fields enable critical business analysis (refund rates, dispute tracking, delivery correlation)

#### Step 3: Form-Based Entry
- Created form with all standardized columns
- Manually entered 25 JSON records using form
- Normalized inconsistent field names during entry

#### Step 4: Multi-Product Handling
- JSON records with multiple items in the `items` array entered as **multiple rows**
- Each product = separate row with same `order_id`
- Enables product-level revenue analysis in SQL

#### Step 5: Data Validation & Export
- Standardized `customer_name` field (resolved customerName vs. customer_name inconsistency)
- Validated TRUE/FALSE values for boolean flags
- Exported dataset

---

## 📦 FILE 5: LOGISTICS (`FILE_5_LOGISTICS.txt`)

### Data Entry Process

#### Step 1: Structure Identification
**Columns:** `Tracking_ID`, `Order_ID`, `Status`, `Courier`, `Update Timestamp`, `Notes`, `Destination`

#### Step 2: Form Creation
- Manually inputted first three tracking records
- Created Excel Form for remaining entries

#### Step 3: Bulk Data Entry
- Used form to input remaining 17 logistics records
- Applied data validation dropdown for `Status`:
  - PENDING
  - SHIPPED
  - IN_TRANSIT
  - DELIVERED
  - RETURNED
  - CANCELLED
- Applied data validation dropdown for `Courier`:
  - SwiftMove
  - QuickShip
  - GlobalExpress
  - RapidCourier
  - AirFreight Plus

#### Step 4: Final Cleaning & Export
- Standardized date format to `YYYY-MM-DD`
- Standardized time format to `HH:MM` (24-hour)
- Exported dataset

---

## 🎫 FILE 6: SUPPORT TICKETS (`FILE_6_SUPPORT_TICKETS.txt`)

### Data Entry Process

#### Step 1: Structure Definition
**Columns:** `Ticket`, `Customer`, `Date`, `Category`, `Severity`, `Issue Description`, `Status`, `Assigned`, 

#### Step 2: Form Creation
- Manually inputted first three support tickets
- Created Excel Form with dropdown validations

#### Step 3: Data Validation Setup
**Category dropdown:**
- Refund Request
- Wrong Item
- Delivery Delay
- Cancellation
- Damaged Goods
- Missing Package
- Billing Error
- Faulty Product
- Quality Issue
- Product Inquiry
- Address Change
- Order Confirmation
- Bulk Order
- Same Day Delivery

**Severity dropdown:**
- High
- Medium
- Low

**Status dropdown:**
- Open
- Resolved
- Closed
- Investigating
- Pending
- Pending Approval

#### Step 4: Bulk Data Entry
- Used form to input remaining 12 support tickets
- Extracted `Related_Order_ID` from `Issue_Description` where mentioned
- Left `Assigned_To` blank where "(unassigned)" appeared in source
- Left `Category` blank where "(missing)" appeared (preserved data quality issue)

#### Step 5: Final Cleaning & Export
- Standardized customer names using `=PROPER()`
- Standardized date format to `YYYY-MM-DD`
- Cross-referenced `Related_Order_ID` against Invoices/API datasets for validation
- Exported dataset
---

## 🧹 Data Quality & Cleaning Standards

### Cleaning Techniques Applied Across All Files

#### 1. Name Standardization
```excel
=PROPER(TRIM(cell))
```
**Result:** `sarah CHEN` → `Sarah Chen`, `JACKSON William` → `Jackson William`

#### 2. Date Standardization
- All dates converted to ISO format: `YYYY-MM-DD`
- Mixed source formats (`DD/MM/YYYY`, `MM-DD-YYYY`, `"March 18th 2026"`) unified

#### 3. Duplicate Flagging
```excel
=IF(COUNTIF($A$2:$A$100,A2)>1,"DUPLICATE","OK")
```
Applied to `Order_IDs`, `Tracking_IDs`, `Invoice_IDs`

#### 4. Calculation Validation
```excel
=IF(Quantity*Unit_Price=Line_Total,"OK","ERROR")
```
Flagged arithmetic errors in source invoices

#### 5. Missing Data Handling
- Left cells blank (not "N/A" or "Unknown")
- Documented in separate `Data_Quality_Notes` column where critical

#### 6. OCR Error Preservation
- Entered errors exactly as they appeared during initial entry
- Fixed during cleaning phase with manual review
- Examples: `Cust0mer` corrected to `Customer`, `Prot3ctor` corrected to `Protector`

---

## 📊 Final Dataset Summary

**Total Records Processed:** 222 rows across 6 datasets  
**Total Data Fields:** 63 unique columns  
**Time Saved Using Forms:** ~60% reduction in data entry time vs. manual cell entry

---

## 🗄️ SQL-Ready Structure

All datasets are optimized for SQL querying with:

- ✅ **Consistent column naming** (snake_case format)
- ✅ **Proper data types** (dates as DATE, numbers as INT/DECIMAL, text as VARCHAR)
- ✅ **Primary keys identified** for unique record identification
- ✅ **Foreign keys documented** for cross-dataset joins
- ✅ **No merged cells or complex formatting** that breaks SQL imports
- ✅ **One entity per row** (normalized structure)

---

## 💡 Key Learnings & Best Practices

1. **Form-based entry is 60% faster** than manual cell-by-cell entry for structured data
2. **One message per row** is the industry standard for communication data (confirmed via professional database design research)
3. **One product line per row** is standard for transactional data (enables product-level analysis)
4. **Preserve raw data during entry**, clean systematically afterward (prevents premature data loss)
5. **Data validation dropdowns** prevent typos and ensure categorical consistency
6. **Multi-dataset architecture** is superior to forcing different structures into one table

---

## 🚀 Next Steps: SQL Analysis Pipeline

These datasets are now ready for:

- SQL database import (PostgreSQL, MySQL, SQLite)
- Cross-dataset joins (e.g., Orders → Logistics → Support Tickets)
- Business intelligence reporting (revenue analysis, customer issue tracking)
- Data visualization (Tableau, Power BI)
- Advanced analytics (customer lifetime value, product performance, support efficiency metrics)

---

## 🛠️ Tools Used

- **Microsoft Excel** - Data entry, forms, cleaning, validation
- **Excel Functions:** `PROPER()`, `TRIM()`, `COUNTIF()`, `IF()`, `TEXT()`, `DATEVALUE()`
- **Data Validation:** Dropdowns, list constraints
- **Manual Review:** OCR error correction, address standardization, quality control

---

## ⚠️ Important Notes

- **OCR errors** were intentionally preserved during initial entry to document source data quality issues
- **Duplicate records** (e.g., `Tracking_ID` NX-DEL-7782) were retained and flagged rather than deleted to maintain data integrity
- **Missing data** was left blank rather than filled with placeholder values to avoid introducing false information
- **Calculation errors** in source invoices were flagged but not auto-corrected to preserve audit trail
- **This project demonstrates end-to-end data pipeline skills**: extraction from messy sources → structuring → cleaning → validation → export for analysis

---


---
## This data was forked from my practice account 'saoenc' and was performed by solomon ayodele ogunyeye 

**⭐ If you found this project helpful, please give it a star!**
