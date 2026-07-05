# AI-Automation-project-2
# Smart Order Processing Workflow (n8n)

An n8n automation project that demonstrates conditional logic using **IF** and **Switch** nodes to process e-commerce orders from Google Sheets — routing each order based on payment status, country, order value, and customer type, then logging the final decision back to Google Sheets.

---

## 📋 Overview

This workflow reads order data from a Google Sheet and routes each order through a decision tree:

1. Checks if payment is completed
2. Routes by country (Pakistan / USA / UK / Other)
3. Flags high-value orders (> 10,000) for review
4. Routes by customer type (VIP / Regular / New) to determine the final action
5. Writes the final result back to a Google Sheet

---

## 🎯 Learning Goals

- Practice **IF node** logic (binary true/false routing)
- Practice **Switch node** logic (multi-branch routing)
- Practice **Merge node** usage (combining multiple branches back into one stream)
- Practice reading from and writing to **Google Sheets**

---

## 🗂️ Workflow Structure

```
Get Row(s) in Sheet
        │
        ▼
IF (Payment Check: paid?)
   ├─ FALSE → Set: Payment Pending
   └─ TRUE
        ▼
     Switch (Country)
        ├─ Pakistan → IF (Amount > 10000) → Set: Flag / No Flag
        ├─ USA      → IF (Amount > 10000) → Set: Flag / No Flag
        ├─ UK       → IF (Amount > 10000) → Set: Flag / No Flag
        └─ Default  → Set: Unsupported Region
                          │
                (all branches merged together)
                          ▼
                Switch (Customer Type)
                   ├─ VIP     → Set: Send VIP Discount Email
                   ├─ Regular → Set: Send Standard Invoice
                   ├─ New     → Set: Send Welcome Email
                   └─ Default → Set: Manual Review Needed
                          │
                          ▼
                Google Sheets: Append Row
                (writes final result to Order_Results tab)
```

---

## 🧩 Node List

| # | Node Name | Type | Purpose |
|---|---|---|---|
| 1 | Get row(s) in sheet | Google Sheets | Reads all orders from the source sheet |
| 2 | IF – Payment Check | IF | Splits paid vs. pending orders |
| 3 | Set – Payment Pending | Set | Marks unpaid orders |
| 4 | Switch – Country Router | Switch | Routes by `Country` |
| 5 | IF – High Value (Pakistan/USA/UK) | IF | Checks if `OrderAmount > 10000` |
| 6 | Edit Fields – High Value Flag | Set | Adds a review flag to high-value orders |
| 7 | Set – Unsupported Region | Set | Handles countries outside Pakistan/USA/UK |
| 8 | Merge (x5) | Merge | Combines all country/value branches into one stream |
| 9 | Switch – Customer Type Router | Switch | Routes by `CustomerType` |
| 10 | Set – VIP / Regular / New / Default Result | Set | Assigns the final action per customer type |
| 11 | Google Sheets – Append Row | Google Sheets | Writes final results to `Order_Results` tab |

---

## 📊 Sample Data Columns

Source sheet should have these columns:

```
OrderID, CustomerName, Country, OrderAmount, PaymentStatus, CustomerType, OrderDate
```

Results sheet (`Order_Results` tab) should have:

```
OrderID, CustomerName, Country, OrderAmount, PaymentStatus, CustomerType, FinalAction, ProcessedDate
```

---
## 📸 Workflow Preview

<img width="1344" height="621" alt="Screenshot 2026-07-05 125428" src="https://github.com/user-attachments/assets/4ab2977a-2588-40d7-ad09-fa2a15f22c2f" />

## Final result to Googlesheets

<img width="1108" height="632" alt="Screenshot 2026-07-05 125516" src="https://github.com/user-attachments/assets/252a6339-5f2f-4cc9-92a8-e3ed1d2df842" />


## ⚙️ Setup Instructions

1. **Google Sheets**
   - Create a sheet with sample order data (see columns above)
   - Add a second tab named `Order_Results` with the results header row
   - Share/connect the sheet with your n8n Google Sheets credential

2. **n8n**
   - Import or build the workflow following the node list above
   - Connect your Google account under each Google Sheets node's credential field
   - Set all IF/Switch conditions using `Equal` operations on `Country` and `CustomerType`
   - Enable **Fallback Output** on both Switch nodes to catch unmatched values

3. **Test**
   - Click **Test workflow**
   - Check each node's output tabs to confirm correct routing
   - Confirm `Order_Results` tab populates with all rows after a full run

---

## 🐛 Troubleshooting Notes

| Issue | Likely Cause | Fix |
|---|---|---|
| Only one Switch output shows data | You're only viewing one output tab | Click through all output tabs (Output 0, 1, 2, 3...) |
| Fallback output is empty but items are missing | Fallback not enabled | Set **Fallback Output** to "Extra Output" in Switch settings |
| Items don't match expected country/type | Spacing, casing, or wording mismatch | Check exact values in Google Sheet (e.g., `"USA"` vs `"usa"` vs `"United States"`) |
| Merge node item count looks wrong | Wrong Merge mode | Use **Append** mode, not "Combine" |

---

