# End-to-End Billing Support Case Study

## 1. Overview

This case study demonstrates how a technical billing support specialist can manage a customer issue from initial contact through investigation, escalation, resolution and closure.

It combines customer communication, payment verification, SQL investigation, issue prioritisation and documentation.

All customer details, transactions and events in this case study are fictional.

## 2. Customer Support Ticket

**Ticket ID:** BILL-2026-001

**Category:** Billing and Payments

**Priority:** High

**Status:** Open

**Customer:** Northstar Technologies

**Issue:** Successful payment not reflected on account

### Customer's Initial Message

Hello Support Team,

We completed payment for our monthly subscription, but our account still shows an outstanding invoice.

Our payment provider confirmed that the transaction was successful. However, our account continues to display an unpaid balance, and we are concerned that our service may be interrupted.

Please investigate and advise.

Thank you.

## 3. Initial Support Assessment

### Expected Behaviour

Following successful payment verification and processing, the relevant invoice and account billing records should reflect the correct payment status.

### Actual Behaviour

The customer reports that:
- Payment was completed.
- The payment provider confirmed success.
- The invoice still shows as outstanding.
- The customer is concerned about possible service interruption.

### Initial Assessment

This is a billing discrepancy involving potentially inconsistent payment and invoice records.

The issue should be prioritised because it may affect the customer's access to the service.

However, the support specialist should not assume that the payment has been fully settled solely based on the customer's report.

## 4. Initial Customer Response

Hello,

Thank you for contacting Support.

We understand your concern regarding the payment that has not yet been reflected on your account.

We will review the transaction and associated invoice records to establish the cause of the discrepancy.

To help with our investigation, kindly provide the invoice reference, payment reference and approximate transaction date and time.

Please do not share your password, secret keys or full payment-card details.

We will keep you informed as the investigation progresses.

Best regards,  
Billing Support Team

## 5. Investigation Process

### Step 1: Gather Relevant Information

The customer provides the following fictional information:

| Field | Value |
|---|---|
| Customer ID | CUS-10001 |
| Invoice ID | INV-2026-001 |
| Payment Reference | PAY-2026-001 |
| Invoice Amount | NGN 50,000 |
| Payment Amount | NGN 50,000 |
| Payment Date | 6 November 2026 |
| Payment Provider Status | Successful |
| Application Invoice Status | Outstanding |

### Step 2: Identify the Possible Causes

The following possibilities should be investigated:

1. Payment confirmation was received but not processed by the billing system.
2. A payment notification or webhook failed.
3. Payment was recorded against an incorrect invoice reference.
4. The invoice status update was delayed.
5. The payment provider's initial status was not the final settlement status.

These are investigation hypotheses, not confirmed findings.

### Step 3: Validate the Invoice Record Using SQL

```sql
SELECT
    invoice_id,
    customer_id,
    invoice_amount,
    amount_paid,
    status
FROM Invoices
WHERE invoice_id = 'INV-2026-001';
```

**Purpose:** Confirm the invoice amount, recorded payment and current invoice status.

### Step 4: Validate the Payment Record

```sql
SELECT
    payment_id,
    invoice_id,
    payment_amount,
    payment_status
FROM Payments
WHERE payment_id = 'PAY-2026-001';
```

**Purpose:** Review the payment record and establish whether the transaction is recorded against the expected invoice.

### Step 5: Compare Invoice and Payment Records

```sql
SELECT
    i.invoice_id,
    i.invoice_amount,
    i.amount_paid,
    i.status,
    p.payment_id,
    p.payment_amount,
    p.payment_status
FROM Invoices i
LEFT JOIN Payments p
    ON i.invoice_id = p.invoice_id
WHERE i.invoice_id = 'INV-2026-001';
```

**Purpose:** Compare the relevant invoice and payment records.

### Step 6: Review Transaction Processing Evidence

The support specialist should also investigate:

- Payment-provider transaction status.
- Webhook or callback delivery logs, where applicable.
- Billing-system processing logs.
- Transaction timestamps.
- Payment reference matching.
- Any retry or failed processing events.

Only authorised personnel should access restricted production logs or payment records.

## 6. Simulated Investigation Findings

For this exercise, assume the following evidence is obtained:

- The payment provider confirms the transaction as successful.
- The payment reference matches the customer's invoice.
- The payment amount matches the invoice amount.
- The payment notification was not successfully processed by the billing application.
- The invoice status therefore remains outstanding.

**Conclusion:** The evidence points to a payment notification processing failure rather than an incorrect payment amount.

This is a simulated finding created for the exercise.

## 7. Technical Escalation

**Escalation Team:** Billing Engineering

**Issue:** Payment notification processing failure

**Customer Reference:** CUS-10001

**Invoice Reference:** INV-2026-001

**Payment Reference:** PAY-2026-001

**Expected Behaviour:** A verified successful payment notification should be processed and reflected in the relevant billing records.

**Actual Behaviour:** The payment provider confirms success, but the billing application still displays the invoice as outstanding.

**Investigation Performed:**
- Customer and invoice references verified.
- Payment amount compared with invoice amount.
- Payment reference matched.
- Payment-provider status checked.
- Application invoice status reviewed.
- Payment notification processing evidence reviewed.

**Finding:** The payment notification was not successfully processed by the billing application.

**Requested Action:**
- Investigate the failed notification processing.
- Confirm whether the notification can be safely retried.
- Verify idempotency to prevent duplicate payment processing.
- Confirm successful invoice reconciliation.
- Advise on any required customer account adjustment.

## 8. Resolution and Verification

For this simulated case, assume Engineering confirms that the failed notification can be safely reprocessed.

The support specialist should:

1. Confirm that the payment is valid and has not already been applied.
2. Ensure that reprocessing will not create a duplicate payment record.
3. Obtain the necessary authorisation.
4. Confirm successful processing.
5. Recheck the invoice and payment records.
6. Verify that the customer account reflects the correct status.
7. Confirm that the customer's service access is not unnecessarily interrupted.

**Simulated Resolution:** The payment notification is successfully reprocessed, and the invoice status is updated to Paid.

## 9. Customer Resolution Message

Hello,

Thank you for your patience while we investigated the payment discrepancy.

Our investigation identified an issue with the processing of the payment notification received by our billing application.

The issue has now been addressed, and we have verified that the payment is reflected against the relevant invoice.

Your invoice status has been updated accordingly.

We apologise for the inconvenience and appreciate your understanding.

Best regards,  
Billing Support Team

## 10. Ticket Closure Notes

**Ticket ID:** BILL-2026-001

**Final Status:** Resolved

**Root Cause:** Failed payment notification processing.

**Resolution:** Payment notification successfully reprocessed following verification and authorisation.

**Customer Impact:** Invoice incorrectly displayed as outstanding despite a successful payment.

**Preventive Recommendation:**
- Monitor failed payment notifications.
- Implement appropriate retry mechanisms.
- Maintain idempotency controls.
- Alert support teams to unresolved payment notifications.
- Improve reconciliation monitoring between payment and billing records.

## 11. Lessons Learned

This case demonstrates the importance of:

- Understanding customer concerns before investigating.
- Gathering sufficient evidence.
- Separating customer-reported information from verified findings.
- Using SQL to investigate related records.
- Understanding payment processing and notification workflows.
- Escalating technical issues with clear evidence.
- Verifying the outcome before communicating resolution.
- Maintaining accurate case documentation.
- Preventing duplicate processing during payment recovery.

## 12. Portfolio Disclaimer

This is a simulated end-to-end billing support case study created for professional learning and portfolio demonstration.

The customer, invoice, payment reference, technical findings and resolution are fictional. This case study does not represent an actual customer incident or claim of direct production SaaS billing experience.
