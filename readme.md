Telecom Billing System Case Study in Golang
Scenario

A telecom company wants to implement a simple billing system in Go to manage customer accounts, track call records, and generate bills.

System Features
Customer Management (CRUD)
Add, Update, Retrieve, and Delete customer details.
Call Records (CRUD)
Store and retrieve call details.
Billing
Calculate bill amount based on call duration.
Error Handling
Handle errors like missing customers, duplicate records, or invalid data.
Validation
Ensure valid phone numbers and call durations.
Usage of Maps & Slices
Efficiently store customer and call records.

Implementation
Let's break it down into key components:

1. Defining the Customer Struct

Each customer has:

ID
Name
PhoneNumber
Balance

Defining CallRecord Struct

Each call record includes:

CallID
CustomerID
Duration (in minutes)
CostPerMinute
Timestamp

A Telecom Billing System in Golang that:

Manages Customers (CRUD)
Handles Call Records (CRUD)
Calculates Billing for Customers
Uses Structs, Interfaces, Slices, Maps, and Error Handling
Implements Validations and Constraints
Uses a Clean Project Structure for Maintainability

telecom-billing-system/
│── main.go
│── go.mod
│── go.sum
│
├── config/ # Configuration settings
│ ├── config.go
│
├── models/ # Data structures
│ ├── customer.go
│ ├── call_record.go
│
├── services/ # Business logic
│ ├── customer_service.go
│ ├── call_service.go
│ ├── billing_service.go
│
├── repository/ # Data storage & operations
│ ├── customer_repo.go
│ ├── call_repo.go
│
├── utils/ # Utility functions
│ ├── validation.go
│
└── tests/ # Test cases
├── customer_test.go
├── call_test.go
├── billing_test.go

📌 Requirements

Functional Requirements
✅ Add, Retrieve, Update, and Delete Customers
✅ Add, Retrieve, and Delete Call Records
✅ Generate Bill for a Customer
✅ Validate Phone Numbers and Call Durations
✅ Handle Errors for Invalid Inputs

Non-Functional Requirements
✅ Efficient data retrieval using maps for fast lookups
✅ Modular project structure following Go best practices
✅ Proper error handling for data consistency
✅ Scalability for future integration with a database

📌 Constraints

Constraint Description
Unique Customer ID Each customer must have a unique ID.
Phone Number Format Must be a 10-digit number.
Call Duration Must be a positive integer (in minutes).
Customer Balance Cannot be negative.
