# Billing Support Exercise: Invoice Discrepancy and Reconciliation Investigation

## 1. Objective

This exercise demonstrates a structured approach to investigating billing discrepancies, payment confirmation issues and invoice reconciliation using SQL and support investigation techniques.

The scenarios and records are fictional. They demonstrate transferable skills relevant to financial systems, technical support and SaaS billing operations.

## 2. Business Scenario

A customer contacts the support team with the following complaint:

"I completed payment for my invoice, but my account still shows an outstanding balance. I have also noticed that the amount displayed on my invoice differs from what I expected."

The support team is required to investigate both issues, validate the available records and determine the appropriate next action.

## 3. Sample Data Structure

The investigation uses three fictional database tables.

### Customers

| customer_id | customer_name |
|---|---|
| CUS-001 | Alpha Technologies |
| CUS-002 | Bright Systems |

### Invoices

| invoice_id | customer_id | invoice_amount | amount_paid | status |
|---|---|---:|---:|---|
| INV-001 | CUS-001 | 50000 | 0 | Outstanding |
| INV-002 | CUS-002 | 75000 | 75000 | Paid |

### Payments

| payment_id | invoice_id | payment_amount | payment_status |
|---|---|---:|---|
| PAY-001 | INV-001 | 50000 | Successful |
| PAY-002 | INV-002 | 75000 | Successful |

All amounts are fictional and denominated in NGN.

## 4. SQL Investigation

The following examples use standard SQL syntax. Minor adjustments may be required depending on the database platform.

### Investigation A: Identify invoices still showing an outstanding balance

```sql
SELECT
    invoice_id,
    customer_id,
    invoice_amount,
    amount_paid,
    status
FROM Invoices
WHERE status = 'Outstanding';
```

**Purpose:** Identify invoices currently recorded as outstanding.

### Investigation B: Compare successful payments against invoice records

```sql
SELECT
    i.invoice_id,
    i.customer_id,
    i.invoice_amount,
    i.amount_paid,
    p.payment_amount,
    p.payment_status
FROM Invoices i
INNER JOIN Payments p
    ON i.invoice_id = p.invoice_id
WHERE p.payment_status = 'Successful';
```

**Purpose:** Compare invoice and payment records to identify possible inconsistencies.

### Investigation C: Identify differences between invoice amounts and successful payments

```sql
SELECT
    i.invoice_id,
    i.invoice_amount,
    SUM(
        CASE
            WHEN p.payment_status = 'Successful'
            THEN p.payment_amount
            ELSE 0
        END
    ) AS total_successful_payments
FROM Invoices i
LEFT JOIN Payments p
    ON i.invoice_id = p.invoice_id
GROUP BY
    i.invoice_id,
    i.invoice_amount
HAVING i.invoice_amount <>
    SUM(
        CASE
            WHEN p.payment_status = 'Successful'
            THEN p.payment_amount
            ELSE 0
        END
    );
```

**Purpose:** Identify invoices where the recorded invoice amount differs from the total successful payments.

Note: This query compares invoice totals with successful payments. It does not, by itself, establish that an invoice should be marked as paid. Refunds, partial payments, duplicate records, credits and payment allocation rules must also be considered.

### Investigation D: Identify payment records without a matching invoice

```sql
SELECT
    p.payment_id,
    p.invoice_id,
    p.payment_amount,
    p.payment_status
FROM Payments p
LEFT JOIN Invoices i
    ON p.invoice_id = i.invoice_id
WHERE i.invoice_id IS NULL;
```

**Purpose:** Identify possible orphaned payment records requiring further investigation.

## 5. Simulated Support Cases

### Case A: Successful payment, outstanding invoice

**Customer complaint:** The customer paid the invoice, but the dashboard still displays an outstanding balance.

**Investigation steps:**
- Confirm the invoice reference.
- Locate the payment record.
- Verify the payment status.
- Compare the payment amount with the invoice amount.
- Check whether the payment has been allocated to the correct invoice.
- Review the transaction timestamp and any relevant processing logs.
- Determine whether a synchronisation or status-update delay may exist.

**Possible finding:** The payment record is successful, but the invoice status has not been updated.

**Recommended action:** Escalate for authorised reconciliation or billing-system investigation. Do not manually change invoice status without the required approval and verification.

### Case B: Invoice amount differs from expected usage

**Customer complaint:** The customer believes the invoice is higher than the services consumed.

**Investigation steps:**
- Confirm the invoice period.
- Review the usage records for that billing period.
- Check the applicable pricing plan and rate.
- Identify additional charges, credits, adjustments or taxes.
- Compare the invoice calculation with the available usage records.
- Document any unexplained difference.

**Possible finding:** The invoice may include usage or charges that require further validation.

**Recommended action:** Provide a breakdown of the charges or escalate the discrepancy to the billing team.

### Case C: Duplicate payment concern

**Customer complaint:** The customer believes they were charged twice for one invoice.

**Investigation steps:**
- Identify the invoice reference.
- Search for all associated payment records.
- Compare payment references and timestamps.
- Verify transaction statuses with the payment provider.
- Check whether one transaction is pending, reversed, refunded or settled.
- Avoid concluding that a duplicate charge occurred based only on similar amounts.

**Possible finding:** Two payment records exist, but their final settlement status has not yet been confirmed.

**Recommended action:** Escalate for payment-provider confirmation and follow the applicable refund or reversal process.

### Case D: Partial payment

**Customer complaint:** The customer made a payment but still has an outstanding balance.

**Investigation steps:**
- Confirm the invoice amount.
- Verify the payment amount and status.
- Check whether partial payments are supported.
- Review the remaining balance.
- Confirm whether additional payments or credits have been recorded.

**Possible finding:** The payment was successful but did not cover the full invoice amount.

**Recommended action:** Explain the outstanding balance and provide the customer with the verified payment breakdown.

## 6. Investigation and Evidence Checklist

Before resolving or escalating a billing case, collect:

- Customer or account reference.
- Invoice number.
- Payment reference.
- Invoice amount and currency.
- Payment amount and currency.
- Invoice date and billing period.
- Payment date and timestamp.
- Payment status.
- Invoice status.
- Relevant usage or transaction records.
- Sanitised error messages or application logs.
- Steps taken to reproduce the issue.

Never request passwords, secret keys, full payment-card details or unnecessary personal information.

## 7. Customer Communication Example

**Subject: Update on Your Invoice Payment Investigation**

Hello,

Thank you for bringing this to our attention.

We have commenced an investigation into the discrepancy between your payment record and the invoice status displayed on your account.

Our review will cover the payment reference, invoice details and the associated transaction records to establish the cause of the discrepancy.

We will provide an update once the necessary verification has been completed.

Thank you for your patience.

Best regards,  
Support Team

## 8. Technical Escalation Template

**Issue:** Invoice and payment status discrepancy

**Customer Reference:** [Fictional or authorised reference]

**Invoice Reference:** [Invoice ID]

**Payment Reference:** [Payment ID]

**Invoice Amount:** [Amount and currency]

**Payment Amount:** [Amount and currency]

**Expected Behaviour:** A verified successful payment should be reflected correctly in the applicable billing records.

**Actual Behaviour:** [Describe the discrepancy]

**Investigation Performed:**
- Invoice record reviewed.
- Payment record reviewed.
- Payment status verified where possible.
- Amounts compared.
- Related transaction records checked.
- Possible reconciliation or synchronisation issue investigated.

**Findings:** [Document verified facts separately from assumptions.]

**Action Required:** [Specify the required billing, payment-provider or engineering investigation.]

## 9. Key Learning Points

- A successful payment response does not automatically establish invoice settlement.
- Invoice status and payment status are separate records and may require reconciliation.
- Usage-based billing requires validation of the billing period, pricing rules and recorded consumption.
- Duplicate payment investigations require transaction-level verification.
- SQL can help identify discrepancies, but business rules determine the correct resolution.
- Read-only queries should be used for investigation wherever possible.
- Any financial adjustment or manual record update must follow authorised procedures.
- Clear customer communication and evidence-based escalation are essential.

## 10. Disclaimer

This is a simulated billing support exercise created for professional learning and portfolio demonstration. All customer records, payment references, amounts and scenarios are fictional. It does not represent a real customer incident, production database or actual payment investigation.
