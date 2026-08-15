# Data Schemas — Expected Input Formats

Source files are not included in this repository (proprietary data).
Below are the expected column schemas for each input file.

---

## `accounts.csv`
Salesforce account export.

| Column | Type | Description |
|---|---|---|
| `Id` | string | Unique Salesforce account ID |
| `Name` | string | Account name |
| `Local_Name__c` | string | Local/alternate name |
| `Status__c` | string | Account status: `Customer`, `Old Customer`, `Prospect`, `Not Applicable` |
| `BillingCountry` | string | Billing country |
| `CreatedDate` | date | Account creation date |
| `LastModifiedDate` | date | Last modification date |
| `AccountClosed__c` | boolean | Whether the account is marked closed |
| `Code_Fusion__c` | string | Fusion code linking to ERP |
| `Sage_ID__c` | string | Direct ERP ID (when available) |
| `Company__r.Sage_ID__c` | string | Parent company ERP ID |
| `Owner.Name` | string | Account owner (sales rep) |

---

## `opportunities.csv`
Salesforce opportunities export.

| Column | Type | Description |
|---|---|---|
| `Id` | string | Opportunity ID |
| `AccountId` | string | Linked account ID (join key) |
| `StageName` | string | Opportunity stage |
| `CreatedDate` | date | Creation date |
| `LastModifiedDate` | date | Last modification date |

---

## `invoice_history.csv`
ERP billing history export.

| Column | Type | Description |
|---|---|---|
| `BUSINESS PARTNER N°` | string | ERP partner code (= `Code_Fusion__c` in CRM) |
| `INVOICE DATE` | date | Invoice date (dd/MM/yyyy) |
| `INVOICE N°` | string | Invoice number |
| `AMOUNT` | float | Invoice amount |

---

## `active_invoices.csv`
Currently unpaid invoices.

| Column | Type | Description |
|---|---|---|
| `BUSINESS PARTNER N°` | string | ERP partner code |
| `INVOICE DATE` | date | Invoice date |
| `AMOUNT` | float | Outstanding amount |

---

## `last_contact.xlsx`
Last CRM contact per account.

| Column | Type | Description |
|---|---|---|
| `AccountId` | string | Linked account ID (join key) |
| `CreatedDate` | datetime | Date of last contact |

---

## `events.xlsx`
Last CRM event per account.

| Column | Type | Description |
|---|---|---|
| `AccountId` | string | Linked account ID (join key) |
| `CreatedDate` | datetime | Date of last event |

---

## `erp_clients.xlsx`
ERP client reference base.

| Column | Type | Description |
|---|---|---|
| `ERP_CLIENT_CODE` | string | ERP client code (output match key) |
| `ERP_ID` | string | Direct ID linking to Salesforce |
| `FUSION_CODE_1` | string | Primary fusion/grouping code |
| `FUSION_CODE_2` | string | Secondary fusion/grouping code |
| `CLIENT_NAME` | string | Reporting client name |
| `CLIENT_LEGAL_NAME` | string | Legal entity name |
