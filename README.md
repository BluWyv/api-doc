# BluWyv Madfoo3 API Documentation

## Version 1.0.0

---

## Table of Contents

1. [Introduction](#introduction)
2. [API Overview](#api-overview)
3. [Authentication & Security](#authentication--security)
4. [Base URL](#base-url)
5. [Content Type](#content-type)
6. [API Endpoints](#api-endpoints)
   - [Verify Client](#verify-client)
   - [Confirm Payment](#confirm-payment)
   - [Cancel Payment](#cancel-payment)
7. [Data Models](#data-models)
8. [Response Status Codes](#response-status-codes)
9. [Error Handling](#error-handling)
10. [Best Practices](#best-practices)

---

## Introduction

The BluWyv Madfoo3 API provides a comprehensive payment processing solution that enables merchants and agents to verify clients, confirm payments, and manage payment cancellations. This RESTful API uses XML for data exchange and follows industry-standard practices for secure payment processing.

## API Overview

The BluWyv Madfoo3 API consists of three core operations:

- **Client Verification**: Validate client identity and retrieve client details
- **Payment Confirmation**: Process and confirm payment transactions
- **Payment Cancellation**: Cancel previously confirmed payments with reason tracking

## Authentication & Security

*Note: Authentication details should be obtained from your BluWyv account manager. Ensure all API calls are made over HTTPS to maintain data security.*

## Base URL

```
https://api.bluwyv.com/madfoo3/v1
```

*Note: Replace with actual production or sandbox URL as provided by BluWyv.*

## Content Type

All requests and responses use XML format:
- Request Content-Type: `text/xml`
- Response Content-Type: `text/xml`

---

## API Endpoints

### Verify Client

Validates client identity and retrieves detailed client information.

**Endpoint:** `POST /verify`

**Summary:** Verify Client

**Request Body:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<VerifyRequest>
    <messageId>MSG-2024-001234</messageId>
    <nationalId>1234567890123</nationalId>
    <clientReferenceType>agent</clientReferenceType>
    <clientReferenceValue>AGT-5678</clientReferenceValue>
</VerifyRequest>
```

**Request Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| messageId | string | Yes | Unique identifier for the request message |
| nationalId | string | Yes | National identification number of the client |
| clientReferenceType | enum | Yes | Type of client reference (`agent` or `merchant`) |
| clientReferenceValue | string | No | Additional reference value for the client |

**Response - Success (200 OK):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<VerifyResponse>
    <messageId>MSG-2024-001234</messageId>
    <status>0</status>
    <clientDetail>
        <agent>
            <id>AGT-5678</id>
            <name>John Smith</name>
            <code>JS001</code>
            <cellNumber>+27821234567</cellNumber>
            <nationalId>1234567890123</nationalId>
            <companyName>BluWyv Payments Ltd</companyName>
        </agent>
    </clientDetail>
</VerifyResponse>
```

**Response - Not Found (200 OK with error status):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<VerifyResponse>
    <messageId>MSG-2024-001234</messageId>
    <status>404</status>
    <clientDetail/>
</VerifyResponse>
```

---

### Confirm Payment

Processes and confirms a payment transaction with location tracking.

**Endpoint:** `POST /confirmPayment`

**Request Body:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ConfirmPaymentRequest>
    <messageId>MSG-2024-005678</messageId>
    <clientReferenceType>merchant</clientReferenceType>
    <clientReferenceValue>MER-9012</clientReferenceValue>
    <referenceNumber>REF-20240315-001</referenceNumber>
    <amount>1500.50</amount>
    <geoLocation>-26.2041,28.0473</geoLocation>
    <city>Johannesburg</city>
    <province>Gauteng</province>
</ConfirmPaymentRequest>
```

**Request Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| messageId | string | Yes | Unique identifier for the request message |
| clientReferenceType | enum | Yes | Type of client reference (`agent` or `merchant`) |
| clientReferenceValue | string | Yes | Reference value identifying the client |
| referenceNumber | string | Yes | Unique payment reference number |
| amount | number | Yes | Payment amount (decimal value) |
| geoLocation | string | Yes | GPS coordinates of payment location (latitude,longitude) |
| city | string | Yes | City where the payment was received |
| province | string | Yes | Province where the payment was collected |

**Response - Success (200 OK):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ConfirmPaymentResponse>
    <messageId>MSG-2024-005678</messageId>
    <status>0</status>
    <transactionId>TXN-2024-789012</transactionId>
    <message>Payment confirmed successfully</message>
</ConfirmPaymentResponse>
```

**Response - Agent Not Found (200 OK with error status):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ConfirmPaymentResponse>
    <messageId>MSG-2024-005678</messageId>
    <status>404</status>
    <transactionId></transactionId>
    <message>Agent not found in the system</message>
</ConfirmPaymentResponse>
```

---

### Cancel Payment

Cancels a previously confirmed payment with reason tracking.

**Endpoint:** `POST /cancelPayment`

**Request Body:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cancelPaymentRequest>
    <messageId>MSG-2024-009012</messageId>
    <refrenceNumber>REF-20240315-001</refrenceNumber>
    <reasonCode>1</reasonCode>
</cancelPaymentRequest>
```

**Request Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| messageId | string | Yes | Unique identifier for the request message |
| refrenceNumber | string | Yes | Reference number of the payment to cancel |
| reasonCode | integer | Yes | Reason for cancellation (1 = payment made by mistake) |

**Response - Success (200 OK):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cancelPaymentResponse>
    <messageId>MSG-2024-009012</messageId>
    <status>0</status>
    <transactionId>TXN-2024-789012</transactionId>
</cancelPaymentResponse>
```

**Response - Pending (200 OK with pending status):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cancelPaymentResponse>
    <messageId>MSG-2024-009012</messageId>
    <status>1</status>
    <transactionId>TXN-2024-789012</transactionId>
</cancelPaymentResponse>
```

---

## Data Models

### Agent

Represents an agent or merchant in the system.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | string | Yes | Unique identifier for the agent |
| name | string | Yes | Full name of the agent |
| code | string | Yes | Agent code |
| cellNumber | string | Yes | Mobile phone number |
| nationalId | string | Yes | National identification number |
| companyName | string | Yes | Name of the associated company |

### ClientReferenceType

Enumeration defining the type of client reference.

**Valid Values:**
- `agent` - Represents an agent client
- `merchant` - Represents a merchant client

---

## Response Status Codes

### Verify Endpoint Status Codes

| Code | Description |
|------|-------------|
| 0 | Success - Client verified successfully |
| 404 | Not Found - Client not found in the system |
| 500 | Error - Internal server error occurred |

### Confirm Payment Endpoint Status Codes

| Code | Description |
|------|-------------|
| 0 | Success - Payment confirmed successfully |
| 404 | Agent Not Found - Agent not registered in the system |
| 500 | Error - Internal server error occurred |

### Cancel Payment Endpoint Status Codes

| Code | Description |
|------|-------------|
| 0 | Success - Payment cancelled successfully |
| 1 | Pending - Cancellation request is being processed |
| 404 | Payment Not Found - Original payment not found |
| 500 | Error - Internal server error occurred |

---

## Error Handling

The API uses HTTP status code 200 for all responses, with the actual operation status indicated in the response body's `status` field. When an error occurs:

1. Check the `status` field in the response
2. For payment operations, review the `message` field for additional details
3. Ensure the `messageId` matches your request for correlation

### Example Error Response

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ConfirmPaymentResponse>
    <messageId>MSG-2024-005678</messageId>
    <status>500</status>
    <transactionId></transactionId>
    <message>Database connection timeout - please retry</message>
</ConfirmPaymentResponse>
```

---

## Best Practices

### Message ID Generation

- Use unique, traceable message IDs for each request
- Recommended format: `MSG-YYYY-NNNNNN` where YYYY is the year and NNNNNN is a sequential number
- Store message IDs for audit and troubleshooting purposes

### Payment Reference Numbers

- Ensure reference numbers are unique across your system
- Include date/time stamps in reference numbers for easier tracking
- Format suggestion: `REF-YYYYMMDD-NNNN`

### Geolocation Data

- Provide accurate GPS coordinates in decimal degrees format
- Format: `latitude,longitude` (e.g., `30.0444,31.2357`)
- Ensure location services are enabled for accurate tracking

### Error Recovery

- Implement retry logic with exponential backoff for status 500 errors
- For pending cancellations (status 1), poll periodically for final status
- Maintain idempotency by using the same messageId for retries

### Security Recommendations

- Always use HTTPS for API communications
- Implement request signing/authentication as specified by BluWyv
- Never log sensitive information like national IDs in plain text
- Implement rate limiting on your client side to avoid overwhelming the API

### Testing

- Use sandbox environment for development and testing
- Test all error scenarios including network failures
- Verify XML schema compliance before sending requests
- Implement comprehensive logging for production troubleshooting

---

*This documentation is subject to change. Please refer to the latest version available on the developer portal.*

**Document Version:** 1.0.0  
**Last Updated:** March 2024  
**OpenAPI Specification:** 3.0.2
