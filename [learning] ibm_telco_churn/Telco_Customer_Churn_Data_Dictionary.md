# Telco Customer Churn — Data Dictionary

## Customer Information

| Field | Description | Values |
|-------|-------------|--------|
| **CustomerID** | A unique ID that identifies each customer. | — |
| **Count** | A value used in reporting/dashboarding to sum up the number of customers in a filtered set. | — |
| **Gender** | The customer's gender. | `Male`, `Female` |
| **Senior Citizen** | Indicates if the customer is 65 or older. | `Yes`, `No` |
| **Partner** | Indicates if the customer has a partner. | `Yes`, `No` |
| **Dependents** | Indicates if the customer lives with any dependents (children, parents, grandparents, etc.). | `Yes`, `No` |
| **Tenure Months** | Total months the customer has been with the company by end of the quarter. | — |

## Location

| Field | Description |
|-------|-------------|
| **Country** | The country of the customer's primary residence. |
| **State** | The state of the customer's primary residence. |
| **City** | The city of the customer's primary residence. |
| **Zip Code** | The zip code of the customer's primary residence. |
| **Lat Long** | The combined latitude and longitude of the customer's primary residence. |
| **Latitude** | The latitude of the customer's primary residence. |
| **Longitude** | The longitude of the customer's primary residence. |

## Services

| Field | Description | Values |
|-------|-------------|--------|
| **Phone Service** | Indicates if the customer subscribes to home phone service. | `Yes`, `No` |
| **Multiple Lines** | Indicates if the customer subscribes to multiple telephone lines. | `Yes`, `No` |
| **Internet Service** | Indicates if the customer subscribes to Internet service. | `No`, `DSL`, `Fiber Optic`, `Cable` |
| **Online Security** | Indicates if the customer subscribes to an additional online security service. | `Yes`, `No` |
| **Online Backup** | Indicates if the customer subscribes to an additional online backup service. | `Yes`, `No` |
| **Device Protection** | Indicates if the customer subscribes to an additional device protection plan for their Internet equipment. | `Yes`, `No` |
| **Tech Support** | Indicates if the customer subscribes to an additional technical support plan with reduced wait times. | `Yes`, `No` |
| **Streaming TV** | Indicates if the customer uses their Internet service to stream TV from a third-party provider. No additional fee charged. | `Yes`, `No` |
| **Streaming Movies** | Indicates if the customer uses their Internet service to stream movies from a third-party provider. No additional fee charged. | `Yes`, `No` |

## Billing

| Field | Description | Values |
|-------|-------------|--------|
| **Contract** | The customer's current contract type. | `Month-to-Month`, `One Year`, `Two Year` |
| **Paperless Billing** | Indicates if the customer has chosen paperless billing. | `Yes`, `No` |
| **Payment Method** | Indicates how the customer pays their bill. | `Bank Withdrawal`, `Credit Card`, `Mailed Check` |
| **Monthly Charge** | The customer's current total monthly charge for all services. | — |
| **Total Charges** | The customer's total charges, calculated to the end of the quarter. | — |

## Churn

| Field | Description | Values |
|-------|-------------|--------|
| **Churn Label** | Whether the customer left the company this quarter. Directly related to `Churn Value`. | `Yes`, `No` |
| **Churn Value** | Binary encoding of `Churn Label`. | `1` = churned, `0` = retained |
| **Churn Score** | A score from 0–100 calculated using IBM SPSS Modeler. Higher score = higher churn likelihood. | `0`–`100` |
| **Churn Reason** | The customer's specific reason for leaving. Directly related to `Churn Category`. | — |
| **CLTV** | Customer Lifetime Value — a predicted value calculated using corporate formulas. Higher value = more valuable customer. High-value customers should be monitored for churn. | — |
