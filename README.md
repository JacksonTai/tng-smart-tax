# AI-Powered Tax Relief Detection (TNG eWallet Demo)

---

## 1. Overview

This project demonstrates how a payment system (simulated TNG eWallet) can be extended beyond transaction processing into a financial intelligence layer using AI.

Traditional payment systems capture only:
- Merchant identity
- Transaction amount
- Timestamp

This system enhances that data by integrating:
- Invoice-level item data
- Merchant classification (MSIC)
- AI-based reasoning

The result is the ability to determine:
"What exactly was purchased, and is it eligible for tax relief?"

---

## 2. Core Problem

### 2.1 Lack of Item-Level Visibility

Payment systems fundamentally lack item-level granularity.

Example:

```json
{
  "merchant": "Senheng",
  "amount": 120
}
```

This does not reveal:
- Whether the purchase was a laptop, cable, or appliance
- Whether it qualifies for tax relief

---

### 2.2 SKU Data is Not Shared

Merchants do not expose SKU or item-level data to payment providers.

Reasons:
- Commercial sensitivity (product mix reveals strategy)
- Data privacy concerns
- Lack of integration between POS and payment rails
- No regulatory requirement

This creates a structural gap:
Payment providers cannot directly access "what was bought"

---

## 3. Solution Overview

The system solves this without requiring merchants to change behavior.

Instead of direct SKU sharing, it uses:

Invoice Matching via E-Invoicing Data

Key idea:
Reuse data already submitted for compliance purposes.

---

## 4. Invoice Data Acquisition

### 4.1 Source: E-Invoicing Providers

Merchants already submit invoices to e-invoicing platforms for tax compliance.

These include:

#### A. Sales Invoices (Output)
- Issued to customers
- Contains:
  - Item names
  - Quantity
  - Unit price
  - Total amount
  - Timestamp

#### B. Purchase Invoices (Input)
- Received from suppliers
- Contains:
  - Raw materials or goods purchased
  - Cost
  - Supplier identity

---

### 4.2 Data Flow

Merchant POS / Accounting System
        ↓
E-Invoicing Provider (LHDN-compliant)
        ↓
TNG Integration Layer
        ↓
Transaction Matching Engine
        ↓
AI Processing Layer

---

## 5. Transaction-to-Invoice Matching

### 5.1 Matching Inputs

Payment transaction provides:
- merchant_id
- amount
- timestamp

Invoice record provides:
- merchant_id
- total_amount
- timestamp
- items

---

### 5.2 Matching Logic

A transaction is matched to an invoice when:

- Merchant ID matches
- Total amount matches
- Timestamp falls within acceptable range

---

### 5.3 Example

Payment:

```json
{
  "merchant_id": "merchant_123",
  "amount": 450,
  "timestamp": "2026-04-25T10:00:00Z"
}
```

Invoice:

```json
{
  "timestamp": "2026-04-25T10:00:00Z",
  "total_amount": 450,
  "items": [
    { "item_name": "Yonex 100" }
  ]
}
```

Result:
- Successful match
- Item-level data retrieved

---

## 6. Data Enrichment

After matching, the system builds a structured input:

- Items from invoice
- Merchant category
- MSIC description

Example:

```json
{
  "items": [{ "item_name": "Yonex 100" }],
  "merchant_category": "Sports Equipment Seller",
  "msic_description": "Retail sale of sporting goods"
}
```

---

## 7. AI Processing

### 7.1 Objective

The AI model performs:
- Item type inference
- Tax category mapping
- Eligibility determination
- Reason generation

---

### 7.2 Reasoning Strategy

The system supports three reasoning modes:

#### Clear Case
Item name directly indicates category

Example:
"Math Textbook"

#### Model-Based Case
Item name is a product model

Example:
"Yonex 100"

Uses merchant context to infer meaning

#### Vague Case
Item name provides no meaning

Example:
"SKU12345"

Falls back to merchant category

---

## 8. AI Output Structure

Example output:

```json
{
  "count": 4,
  "data": [
    {
      "possible_tax_relief_category": "Sports Equipment & Gym Fees",
      "item_name": "Yonex 100",
      "merchant_name": "Al-Ikhsan Sports",
      "amount": "450",
      "transaction_id": "TXN_002",
      "merchant_type": "Sports Equipment Seller",
      "item_type": "Sports Equipment",
      "reasoning_case": "model",
      "reasoning": "Model name interpreted using merchant context"
    }
  ]
}
```

---

## 9. System Capabilities

- Item-level tax classification
- Explainable reasoning for each decision
- Aggregation into tax categories
- Post-transaction insights

---

## 10. Extended Value from Invoice Data

Once both sales and purchase invoices are available, deeper insights emerge.

---

### 10.1 Inventory Intelligence

Example:

"You purchased 5 packs of 10kg rice, but sold 30 fried rice."

Derived from:
- Supplier invoices (incoming stock)
- Customer invoices (outgoing sales)

Enables:
- Stock estimation
- Consumption tracking

---

### 10.2 Price Fluctuation Detection

Tracks:
- Supplier cost over time
- Changes in unit pricing

Example insight:

"Ingredient costs increased this month."

---

### 10.3 Behavioral Insights

- Purchase patterns
- Cost distribution
- Revenue vs expense relationships

---

## 11. Key Design Principle

The system does not introduce new data requirements.

It leverages:
Existing compliance workflows (e-invoicing)

---

## 12. Summary

This system represents a shift:

From:
Payment processing

To:
Financial intelligence

Core mechanism:
Invoice data + AI reasoning

Outcome:
Transactions become structured, explainable, and actionable insights.
