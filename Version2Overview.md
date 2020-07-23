# Payables data model
## Overview

![Payables model](https://github.com/CityofToronto/payables_mockup/raw/v2.0/PayableModel.png)

1. Payment - the payment details, including what was paid, when, which way
2. Requestor - information about person paying
3. Service - the service (and sometimes account) related to the payment
4. Payable - what the payment is for

## Generic model - all fields

```javascript
{
"Service":{                                             // Required - this references the business function, and optional account information
	"ServiceCode": "PropertyTaxCertificate", 			// Required - identifies specific business function that this payment is part of
	"AccountType": "PropertyTaxAccount",     			// Optional - if the payment is related to an account
	"AccountID":   "190448484848084084_348083434",      // Optional - if the payment is related to an account. For accounts with composite keys, separate with _ or -
	"ServiceCriteria":[ // Optional - Other IDs, criteria and credentials to find and retrieve payables for, that is used to find/identify the payables, and to correlate payments with the right records
		{"key":"HolderLastName",		"value":"Macedo"},
		{"key":"PostalCode",			"value":"M4M4M4"}
	],
	"UserAccountRole":   "PropertyOwner",
	"Payload":[{"key":"k1","value":"abc-1234-5235f-f135afa"},{"key":"k2","value":"2020-05-12T22:24:24-4000"},{"key":"k3","value":"A somehing more interesting, I'd say"}]
},
"Payable":{                                // Required - a City record of something that needs to be paid, for payment-result messages this segment specifies what payment was for
    "PayableTimestamp":"2020-07-22T19:40:40.000-4000"     // Required - timestamp of when this payable was determined by the system of record
    "PayableType":  "ParkingTicket",       // Required - type code for the payable
    "PayableID":    "ABCDE12456",          // Required - business specific unique ID for the payable, if it is a composite key in the backend, using - or _ to concatenate into a single ID
    "TotalAmountDue":50050.50,             // Required - the total amount due for this payable, which is 0 once the payable is paid
    "TotalAmount":50050.50,                // Optional - original total amount for this payable
    "DueDate":"2020-04-20",                // Optional - when this payment is due
    "ServiceRequest":{                     // Optional - If this payment is in the context of a Service Request
        "UserRole":          "CertificateRequestor",    // Optional - the role the current user is playing for this request.
        "RequestType": "PropertyTaxCertificate",        // Required - The type code for it
        "RequestID": "FG769JHG83-GFJH309GH3-3GH",       // Optional - The ID for it
        "CustomerRefNumber": "101023423423",            // Optional - Customer reference for it, if different than ID
        "IncidentDate":  "2020-05-05"                   // Optional - If related to an incident, the date of the incident
    },
    "Period":{                             // Optional - Period that this Payable is related to, such as a monthly installment, or quarterly Utility service use
      "Type":"MonthlyBillingInstallment",  // Optional - Type of periodical, if different than PayableType
      "Label":"January 2020 Bill",         // Required - label for period. For taxes, this is one of the 11 monthly installments, for utility billing one of the 2 month period billings, and for annual renewals the year
      "Year":2020,                         // Required - the year of this periodical
      "From":"1",                          // Required - start of the period. Can be a number for quarterly/monthly/bimonthly or dates for custom billing periods
      "To":"1",                            // Optional - end of the period, if different than start. Can be a number for quarterly/monthly/bimonthly or dates for custom billing periods
      "Frequency":                         // Required - one of yearly/quarterly/bimonthly/monthly/bi-weekly/weekly/custom
    },
    "Bill":{                            // Optional - Invoice/Bill for this payable, used for Property Taxes, Utility billing, City Planning applications, 
      "CreatedDate":    "2020-02-02",   // Required - when this document was created/generated/issued
      "ModifiedDate":   "2020-02-02",   // Optional - when this document was last updated/modified
      "DocType":       "TaxBill",       // Required - business type of document, e.g. TaxBill, UtilityBill, RegistrationInvoice, ApplicationFeeCalculation etc
      "DocID":        "fghjgfgh678",    // Optional - internal unique ID for the document
      "FileName":     "janbill.pdf",    // Required - name of the file, will be used 
      "Description":  "",
      "Format":       "PDF",
      "URL":          "https://secure.to.ca/somethign/janbill.pdf"
    },
    "Itemized":{                    // Optional - Details for how the TotalAmountDue is calculated
      "Items":[ // Optional - either Items or BaseAmount is required
        {"SortOrder":1,"Type":"CarryOver",  "Label":"Carry over from 2019",  "Quantity":1, "ShowQuantity":false, "Amount":20020.10, "TotalAmount": 20020.10},
        {"SortOrder":2,"Type":"Installment","Label":"January installment",   "Quantity":1, "ShowQuantity":false, "Amount":1010.44,  "TotalAmount": 1010.44},
        {"SortOrder":3,"Type":"Installment","Label":"February installment",  "Quantity":1, "ShowQuantity":false, "Amount":1010.44,  "TotalAmount": 1010.44}
      ]
      "BaseAmount": 30.0,               // Optional - either Items or BaseAmountis required 
      "BaseAmountLabel":"Principle",    // Optional - alternative label what to call the base amount for end-users. For instance, BaseAmount for a tax bill is called "Principle"
      "Fee":      0.0,  
      "LateFee":  100.0,
      "Discount": 0.0,
      "Interest": 0.0,
      "HST": 0.0,
      "NSFRefNo":"" // Optional - NSF Reference Number, if applicable
    },
	"Payload":[{"key":"k1","value":"abc-1234-5235f-f135afa"},{"key":"k2","value":"2020-05-12T22:24:24-4000"},{"key":"k3","value":"A somehing more interesting, I'd say"}]
}
``` 


### Property Tax account Example - applicable fields
```javascript
{
"Service":{                                             // Required - this references the business function, and optional account information
	"ServiceCode": "PropertyTax", 			// Required - identifies specific business function that this payment is part of
	"AccountType": "PropertyTaxAccount",     			// Optional - if the payment is related to an account
	"AccountID":   "191406329000500000000_453534",      // Optional - if the payment is related to an account. For accounts with composite keys, separate with _ or -
	"ServiceCriteria":[ // Optional - Other IDs, criteria and credentials to find and retrieve payables for, that is used to find/identify the payables, and to correlate payments with the right records
		{"key":"HolderLastName",		"value":"Macedo"},
		{"key":"PostalCode",			"value":"M4M4M4"},
		{"key":"ROLL_NUMBER", "value":"191406329000500000000"},
        {"key":"TENANT_NUMBER", "value":"453534"}
	],
	"UserAccountRole":   "PropertyOwner"
},
"Payable":{                                // Required - a City record of something that needs to be paid, for payment-result messages this segment specifies what payment was for
    "PayableTimestamp":"2020-07-22T19:40:40.000-4000"    // Required - timestamp of when this payable was determined by the system of record
    "PayableType":  "PropertyTaxInstallment",       // Required - type code for the payable
    "PayableID":    "2020-01",          // Required - business specific unique ID for the payable, if it is a composite key in the backend, using - or _ to concatenate into a single ID
    "TotalAmountDue":2000.50,             // Required - the total amount due for this payable, which is 0 once the payable is paid
    "TotalAmount":2000.50,                // Optional - original total amount for this payable
    "DueDate":"2020-04-20",                // Optional - when this payment is due
    "Period":{                             // Optional - Period that this Payable is related to, such as a monthly installment, or quarterly Utility service use
      "Type":"MonthlyBillingInstallment",  // Optional - Type of periodical, if different than PayableType
      "Label":"January 2020 Bill",         // Required - label for period. For taxes, this is one of the 11 monthly installments, for utility billing one of the 2 month period billings, and for annual renewals the year
      "Year":2020,                         // Required - the year of this periodical
      "From":"1",                          // Required - start of the period. Can be a number for quarterly/monthly/bimonthly or dates for custom billing periods
      "Frequency": "monthly"               // Required - one of yearly/quarterly/bimonthly/monthly/bi-weekly/weekly/custom
    },
    "Bill":{                            // Optional - Invoice/Bill for this payable, used for Property Taxes, Utility billing, City Planning applications, 
      "CreatedDate":    "2020-02-02",   // Required - when this document was created/generated/issued
      "ModifiedDate":   "2020-02-02",   // Optional - when this document was last updated/modified
      "DocType":       "TaxBill",       // Required - business type of document, e.g. TaxBill, UtilityBill, RegistrationInvoice, ApplicationFeeCalculation etc
      "DocID":        "fghjgfg4-h67-asdasaasd8",    // Optional - internal unique ID for the document. Same as BILL_NUMBER for PropertyTax bill
      "FileName":     "2020january_taxbill.pdf",    // Required - name of the file, will be used 
      "Format":       "PDF",
      "URL":          "https://secure.to.ca/somethign/2020january_taxbill.pdf"
    },
    "Itemized":{                    // Optional - Details for how the TotalAmountDue is calculated
      "BaseAmount": 1900.50, // Optional - either Items or PayableAmount is required. This is the Principle for Tax installment
      "BaseAmountLabel":"Principle",
      "Interest":  100.0,
      "Fee": 0.0
    }
}
}
```

### Parking ticket Example - applicable fields

```javascript
{
"Service":{                                // Required - this references the business function, and optional account information
	"ServiceCode": "ParkingTickets", 	   // Required - identifies specific business function that this payment is part of
	"AccountID":   "ABCD123"     		   // Optional - if the payment is related to an account. For accounts with composite keys, separate with _ or -
},
"Payable":{                                // Required - a City record of something that needs to be paid, for payment-result messages this segment specifies what payment was for
    "PayableTimestamp":                    // Required - timestamp of when this payable was determined by the system of record
    "PayableType":  "ParkingTicket",       // Required - type code for the payable
    "PayableID":    "AB12456789",          // Required - business specific unique ID for the payable, if it is a composite key in the backend, using - or _ to concatenate into a single ID
    "TotalAmountDue":40.00,                // Required - the total amount due for this payable, which is 0 once the payable is paid
    "DueDate":"2020-07-30",                // Optional - when this payment is due
    "ServiceRequest":{                     // Optional - If this payment is in the context of a Service Request
        "RequestType": "ParkingTicketPayment",          // Required - The type code for it
        "IncidentDate":  "2020-07-01"                   // Optional - If related to an incident, the date of the incident
    },
    "Itemized":{              // Optional - Details for how the TotalAmountDue is calculated
      "BaseAmount": 30.0,     // Optional - either Items or BaseAmount is required 
      "LateFee":  10.0,       // 
      "NSFRefNo":""           // Optional - NSF Reference Number, if applicable
    }
}
}
```
