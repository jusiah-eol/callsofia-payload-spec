# CallSofia - Litify Payload Specification

## Overview

Before wrapping up a call, CallSofia sends a single JSON payload containing two objects: **Party** and **Intake**. Below is the schema and a sample payload ready for implementation.

---

## Sample Payload

```json
{
  "lead": {
    "id": "863c4bb1-6f45-443b-bdc9-6bcf3a616bb2"
  },
  "party": {
    "Phone": "5551234567",
    "litify_pm__Phone_Other__c": "5559876543",
    "litify_pm__First_Name__c": "John",
    "litify_pm__Last_Name__c": "Doe",
    "BillingStreet": "123 Main St",
    "BillingCity": "Newark",
    "BillingState": "NJ",
    "BillingPostalCode": "07102",
    "BillingCountry": "USA",
    "litify_pm__Date_of_birth__c": "1985-06-15"
  },
  "intake": {
    "Incident_Date_Time__c": "2026-03-10T16:44:00.000+0000",
    "Injured__c": "Yes",
    "Is_PNC_alive__c": "Yes",
    "litify_pm__Description__c": "Caller reports rear-end collision on Route 21 southbound. Caller was stopped at red light when struck from behind. Experiencing neck and back pain. Police report filed, report #2026-04821.",
    "litify_pm__Case_Address_1__c": "Route 21 Southbound near Exit 6",
    "litify_pm__Case_City__c": "Newark",
    "litify_pm__Case_State__c": "NJ New Jersey",
    "litify_pm__Case_Postal_Code__c": "07102",
    "Employer_Name__c": "Acme Corp",
    "Child_Injured_Party_Name__c": "Jane Doe",
    "Injured_Party_Date_of_Birth__c": "2015-09-22",
    "Signor_Relationship__c": "Parent",
    "Case_Type__c": "a035f0000034VT8AAM"
  }
}
```

---

## Field Rules

### Party (Salesforce Object: Account)

| Field | Type | Max Length | Notes |
|-------|------|-----------|-------|
| Phone | string | 40 | Primary phone |
| litify_pm__Phone_Other__c | string | 40 | Alternate phone |
| litify_pm__First_Name__c | string | 255 | |
| litify_pm__Last_Name__c | string | 255 | |
| BillingStreet | string | 255 | |
| BillingCity | string | 40 | |
| BillingState | string | 80 | 2-char code (e.g. "NJ") |
| BillingPostalCode | string | 20 | 5-digit ZIP |
| BillingCountry | string | 80 | Default to "USA" if not collected |
| litify_pm__Date_of_birth__c | date | - | Format: YYYY-MM-DD |

### Intake (Salesforce Object: litify_pm__Intake__c)

| Field | Type | Max Length | Notes |
|-------|------|-----------|-------|
| Incident_Date_Time__c | datetime | - | ISO 8601 UTC (e.g. "2026-03-10T16:44:00.000+0000") |
| Injured__c | string | - | Enum: "Yes" or "No" only |
| Is_PNC_alive__c | string | - | Enum: "Yes" or "No" only |
| litify_pm__Description__c | string | 131072 | Full intake questionnaire content from AI agent |
| litify_pm__Case_Address_1__c | string | 255 | Incident location address |
| litify_pm__Case_City__c | string | 255 | Incident location city |
| litify_pm__Case_State__c | string | - | Format: "XX State Name" (e.g. "NJ New Jersey"). See appendix for valid values |
| litify_pm__Case_Postal_Code__c | string | 255 | |
| Employer_Name__c | string | 255 | If employed and not a minor. Otherwise send "N/A" |
| Child_Injured_Party_Name__c | string | - | Only if caller is not the injured party |
| Injured_Party_Date_of_Birth__c | date | - | Only if caller is not the injured party. Format: YYYY-MM-DD |
| Signor_Relationship__c | string | - | Only if caller is not the injured party (e.g. "Parent", "Spouse") |
| Case_Type__c | string | - | Record Type ID. See mapping below |

---

## Case Type Mapping

Map the identified case type to the corresponding Salesforce Record Type ID:

```json
{
  "Workers Compensation": "a035f0000034VT9AAM",
  "Automobile Accident":  "a035f0000034VT8AAM",
  "Slip & Fall":          "a035f0000034VTEAA2",
  "Animal Incident":      "a035f0000034VTSAA2",
  "Bed Bugs":             "a035f0000034VTRAA2",
  "Product Liability":    "a035f0000034VStAAM",
  "Medical Malpractice":  "a035f0000034VSwAAM"
}
```

---

## Case State - Valid Values

```json
[
  "AK Alaska","AL Alabama","AR Arkansas","AZ Arizona",
  "CA California","CO Colorado","CT Connecticut","DC District of Columbia",
  "DE Delaware","FL Florida","GA Georgia","HI Hawaii",
  "IA Iowa","ID Idaho","IL Illinois","IN Indiana",
  "KS Kansas","KY Kentucky","LA Louisiana","MA Massachusetts",
  "MD Maryland","ME Maine","MI Michigan","MN Minnesota",
  "MO Missouri","MS Mississippi","MT Montana","NC North Carolina",
  "ND North Dakota","NE Nebraska","NH New Hampshire","NJ New Jersey",
  "NM New Mexico","NV Nevada","NY New York","OH Ohio",
  "OK Oklahoma","OR Oregon","PA Pennsylvania","RI Rhode Island",
  "SC South Carolina","SD South Dakota","TN Tennessee","TX Texas",
  "UT Utah","VA Virginia","VT Vermont","WA Washington",
  "WI Wisconsin","WV West Virginia","WY Wyoming","N/A"
]
```
