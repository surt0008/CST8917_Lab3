# FleetBook – Serverless Booking Processing System

## Demo Video Link: 
https://youtu.be/KsW6gFwtO2w

## Overview

FleetBook is a serverless booking processing system built using Microsoft Azure services.  
The system processes booking requests, validates them, sends notifications, and routes results using Service Bus topics and subscriptions.

---


## Architecture Flow

1. User submits booking via `client.html`
2. Message is sent to **Service Bus Queue** (`booking-queue`)
3. **Logic App** is triggered by the queue
4. Logic App calls **Azure Function (check-booking)**
5. Function validates booking and returns result
6. Logic App:
   - Sends confirmation/rejection email
   - Publishes result to **Service Bus Topic** (`booking-results`)
7. Topic routes messages to:
   - `confirmed-sub`
   - `rejected-sub`

---

## Project Structure
FleetBook


├── function_app.py

├── requirements.txt

├── test-function.http

├── client.html

├── local.settings.example.json

└── README.md


---

## File Descriptions

### function_app.py
Azure Function code responsible for validating booking requests.

### requirements.txt
Contains required Python dependencies for the Azure Function runtime.

### test-function.http
REST Client test file used to test the Azure Function endpoint.

### client.html
Frontend web application for submitting booking requests and viewing responses.

### local.settings.example.json
Template configuration file containing placeholder values only (NO real secrets).

---

## Azure Resources Required

### 1. Azure Function App
- Hosting Plan: **Consumption**
- Runtime: Python

### 2. Azure Service Bus Namespace
- Queue:
  - `booking-queue`
- Topic:
  - `booking-results`
- Subscriptions:
  - `confirmed-sub`
    - Filter: `sys.Label = 'confirmed'`
  - `rejected-sub`
    - Filter: `sys.Label = 'rejected'`

### 3. Azure Logic App
Workflow must:
- Trigger from Service Bus Queue
- Call Azure Function
- Send email via Office 365 Outlook
- Publish result to Service Bus Topic
  - Label must be exactly:
    - `confirmed`
    - `rejected`

---

## Setup Instructions

### Step 1 - Clone Repository

```bash
git clone <your-github-repo-url>
cd FleetBook
```

### Step 2 – Configure Azure Function

- Deploy `function_app.py`
- Set required **Environment Variables** in Azure Portal
- Ensure **Hosting Plan** is set to **Consumption**
- Copy the **Function URL** (including function key)

### Step 3 – Configure Service Bus

#### Create the following:

- **Queue:** `booking-queue`
- **Topic:** `booking-results`

#### Create Subscriptions under the Topic:

- `confirmed-sub`
- `rejected-sub`

#### Apply SQL Filters:

- For confirmed subscription:
  ```sql
  sys.Label = 'confirmed'
  ``

### Step 4 – Configure Logic App

#### Workflow Steps

1. **Trigger** → Service Bus Queue (`booking-queue`)
2. **Call** → Azure Function
3. **Condition** → Check function result
4. **Send Email** → Office 365 Outlook
5. **Publish Message** → Topic (`booking-results`)

### Set Message Label

- `confirmed`
- `rejected`

---

### Step 5 – Run Application

#### Open

- `client.html`

#### Submit booking requests and verify

- Email notifications are received
- Messages appear in the correct subscription
- Booking result routing works correctly

## Conclusion

FleetBook demonstrates a complete serverless booking system using Azure Functions, Logic Apps, and Service Bus.  
The solution processes requests asynchronously, validates bookings, and routes results automatically.  
This project highlights scalable, event-driven cloud architecture using Microsoft Azure services.
