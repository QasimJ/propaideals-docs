# Portfolio Tracker API Documentation

## Overview

The Portfolio Tracker API provides comprehensive endpoints for managing property investment portfolios, including property management, financial tracking, tenant management, compliance monitoring, and AI-powered analysis.

## Base URL

```
https://api.propertyestateai.com/api/v1/portfolio
```

## Authentication

All endpoints require authentication using Clerk JWT tokens:

```http
Authorization: Bearer <clerk_jwt_token>
```

## Rate Limits

- **Standard endpoints**: 100 requests per minute
- **AI analysis endpoints**: 10 requests per minute
- **File upload endpoints**: 20 requests per minute

## Plan Limits

Portfolio features are gated by subscription plans:

- **Starter Plan**: 1 property maximum
- **Premium Plan**: 5 properties maximum
- **Pro Plan**: Unlimited properties

## Portfolio Management Endpoints

### Get Portfolio Overview

```http
GET /overview
```

Returns comprehensive portfolio dashboard data including total value, performance metrics, and recent activity.

**Response:**

```json
{
  "total_properties": 5,
  "total_value": 1250000.0,
  "total_equity": 750000.0,
  "monthly_income": 4500.0,
  "monthly_expenses": 2100.0,
  "net_cash_flow": 2400.0,
  "average_yield": 4.32,
  "total_roi": 12.5,
  "properties_summary": [
    {
      "id": "prop-123",
      "address": "123 Main St, London",
      "current_value": 250000.0,
      "monthly_income": 900.0,
      "yield": 4.32
    }
  ],
  "recent_activity": [
    {
      "type": "rent_payment",
      "property": "123 Main St",
      "amount": 900.0,
      "date": "2025-01-15"
    }
  ]
}
```

### List Portfolio Properties

```http
GET /properties
```

**Query Parameters:**

- `page` (optional): Page number for pagination (default: 1)
- `limit` (optional): Items per page (default: 10, max: 50)
- `sort` (optional): Sort field (address, value, yield, created_at)
- `order` (optional): Sort order (asc, desc)

**Response:**

```json
{
  "properties": [
    {
      "id": "prop-123",
      "address": "123 Main St, London",
      "postcode": "SW1A 1AA",
      "property_type": "flat",
      "size_sqft": 850,
      "units_rooms": 2,
      "purchase_price": 200000.0,
      "purchase_date": "2023-06-15",
      "estimated_value": 250000.0,
      "monthly_income": 900.0,
      "monthly_expenses": 400.0,
      "net_cash_flow": 500.0,
      "yield": 4.32,
      "roi": 12.5,
      "created_at": "2023-06-15T10:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 5,
    "pages": 1
  }
}
```

### Add Property to Portfolio

```http
POST /properties
```

**Request Body:**

```json
{
  "property_id": 12345,
  "address": "123 Main St, London",
  "postcode": "SW1A 1AA",
  "property_type": "flat",
  "size_sqft": 850,
  "units_rooms": 2,
  "purchase_price": 200000.0,
  "purchase_date": "2023-06-15",
  "estimated_value": 250000.0,
  "mortgage_lender": "Halifax",
  "mortgage_amount": 150000.0,
  "mortgage_rate": 3.5,
  "mortgage_term_years": 25,
  "monthly_mortgage_payment": 750.0,
  "hmo_license_number": "HMO-123456",
  "hmo_license_expiry": "2025-12-31"
}
```

**Response:**

```json
{
  "id": "prop-123",
  "message": "Property added to portfolio successfully",
  "property": {
    "id": "prop-123",
    "address": "123 Main St, London",
    "estimated_value": 250000.0,
    "created_at": "2025-01-15T10:30:00Z"
  }
}
```

**Error Responses:**

```json
{
  "error": {
    "code": "PLAN_LIMIT_EXCEEDED",
    "message": "You've reached your plan limit of 5 properties",
    "details": {
      "current_count": 5,
      "plan_limit": 5,
      "upgrade_url": "/pricing"
    }
  }
}
```

### Get Property Details

```http
GET /properties/{property_id}
```

**Response:**

```json
{
  "id": "prop-123",
  "address": "123 Main St, London",
  "postcode": "SW1A 1AA",
  "property_type": "flat",
  "size_sqft": 850,
  "units_rooms": 2,
  "purchase_price": 200000.0,
  "purchase_date": "2023-06-15",
  "estimated_value": 250000.0,
  "mortgage_details": {
    "lender": "Halifax",
    "amount": 150000.0,
    "rate": 3.5,
    "term_years": 25,
    "monthly_payment": 750.0
  },
  "hmo_details": {
    "license_number": "HMO-123456",
    "license_expiry": "2025-12-31"
  },
  "financial_summary": {
    "monthly_income": 900.0,
    "monthly_expenses": 400.0,
    "net_cash_flow": 500.0,
    "yield": 4.32,
    "roi": 12.5
  },
  "tenant_count": 2,
  "compliance_status": "compliant",
  "created_at": "2023-06-15T10:30:00Z",
  "updated_at": "2025-01-15T10:30:00Z"
}
```

### Update Property

```http
PUT /properties/{property_id}
```

**Request Body:** (partial updates supported)

```json
{
  "estimated_value": 260000.0,
  "mortgage_rate": 3.2
}
```

### Remove Property

```http
DELETE /properties/{property_id}
```

**Response:**

```json
{
  "message": "Property removed from portfolio successfully"
}
```

### Check Plan Limits

```http
GET /plan-limits
```

**Response:**

```json
{
  "current_plan": "premium",
  "property_limit": 5,
  "current_count": 3,
  "remaining": 2,
  "can_add_property": true,
  "upgrade_required": false,
  "upgrade_url": "/pricing"
}
```

## Financial Tracking Endpoints

### Get Financial Data

```http
GET /financials/{property_id}
```

**Query Parameters:**

- `start_date` (optional): Start date for filtering (YYYY-MM-DD)
- `end_date` (optional): End date for filtering (YYYY-MM-DD)
- `period` (optional): Aggregation period (monthly, quarterly, yearly)

**Response:**

```json
{
  "property_id": "prop-123",
  "financial_records": [
    {
      "month": "2025-01-01",
      "rent_income": 900.0,
      "other_income": 0.0,
      "operating_expenses": 150.0,
      "maintenance_costs": 100.0,
      "insurance_costs": 50.0,
      "property_tax": 80.0,
      "utilities": 20.0,
      "management_fees": 0.0,
      "total_expenses": 400.0,
      "cash_flow": 500.0,
      "capex": 0.0,
      "roi_percentage": 12.5,
      "net_yield_percentage": 4.32,
      "rental_yield_percentage": 5.4
    }
  ],
  "summary": {
    "total_income": 10800.0,
    "total_expenses": 4800.0,
    "net_cash_flow": 6000.0,
    "average_monthly_cash_flow": 500.0,
    "average_yield": 4.32
  }
}
```

### Record Monthly Financials

```http
POST /financials/{property_id}
```

**Request Body:**

```json
{
  "month": "2025-01-01",
  "rent_income": 900.0,
  "other_income": 0.0,
  "operating_expenses": 150.0,
  "maintenance_costs": 100.0,
  "insurance_costs": 50.0,
  "property_tax": 80.0,
  "utilities": 20.0,
  "management_fees": 0.0,
  "capex": 0.0
}
```

### Get Performance Metrics

```http
GET /performance/{property_id}
```

**Query Parameters:**

- `period` (optional): Analysis period (1m, 3m, 6m, 1y, all)

**Response:**

```json
{
  "property_id": "prop-123",
  "period": "1y",
  "metrics": {
    "total_return": 15.2,
    "capital_appreciation": 25000.0,
    "capital_appreciation_percentage": 12.5,
    "rental_yield": 4.32,
    "net_yield": 3.8,
    "cash_on_cash_return": 18.5,
    "total_roi": 15.2,
    "annualized_return": 15.2
  },
  "cash_flow_analysis": {
    "total_income": 10800.0,
    "total_expenses": 4800.0,
    "net_cash_flow": 6000.0,
    "monthly_average": 500.0,
    "cash_flow_trend": "increasing"
  },
  "comparison": {
    "market_average_yield": 3.8,
    "outperforming_market": true,
    "performance_vs_market": 13.7
  }
}
```

### Get Cash Flow Analysis

```http
GET /cash-flow/{property_id}
```

**Query Parameters:**

- `period` (optional): Analysis period (1m, 3m, 6m, 1y, all)
- `granularity` (optional): Data granularity (monthly, quarterly)

**Response:**

```json
{
  "property_id": "prop-123",
  "period": "1y",
  "cash_flow_data": [
    {
      "period": "2024-01",
      "income": 900.0,
      "expenses": 400.0,
      "net_cash_flow": 500.0,
      "cumulative_cash_flow": 500.0
    }
  ],
  "summary": {
    "total_cash_flow": 6000.0,
    "average_monthly": 500.0,
    "best_month": {
      "period": "2024-06",
      "amount": 650.0
    },
    "worst_month": {
      "period": "2024-03",
      "amount": 350.0
    },
    "trend": "increasing",
    "volatility": "low"
  }
}
```

### Generate Financial Report

```http
POST /reports/financial
```

**Request Body:**

```json
{
  "property_ids": ["prop-123", "prop-456"],
  "start_date": "2024-01-01",
  "end_date": "2024-12-31",
  "format": "pdf",
  "include_charts": true,
  "report_type": "comprehensive"
}
```

**Response:**

```json
{
  "report_id": "report-789",
  "download_url": "/api/v1/portfolio/reports/download/report-789",
  "expires_at": "2025-01-22T10:30:00Z",
  "format": "pdf",
  "file_size": 2048576
}
```

## Tenant Management Endpoints

### List All Tenants

```http
GET /tenants
```

**Query Parameters:**

- `property_id` (optional): Filter by property
- `active_only` (optional): Show only active tenants (default: true)
- `page` (optional): Page number
- `limit` (optional): Items per page

**Response:**

```json
{
  "tenants": [
    {
      "id": "tenant-123",
      "property_id": "prop-123",
      "room_unit": "Room 1",
      "tenant_name": "John Smith",
      "contact_phone": "+44 7700 900123",
      "contact_email": "john.smith@email.com",
      "lease_start_date": "2024-01-01",
      "lease_end_date": "2024-12-31",
      "rent_amount": 450.0,
      "security_deposit": 450.0,
      "is_active": true,
      "days_until_lease_end": 45,
      "rent_status": "current"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 8,
    "pages": 1
  }
}
```

### Add New Tenant

```http
POST /tenants
```

**Request Body:**

```json
{
  "property_id": "prop-123",
  "room_unit": "Room 1",
  "tenant_name": "John Smith",
  "contact_phone": "+44 7700 900123",
  "contact_email": "john.smith@email.com",
  "emergency_contact_name": "Jane Smith",
  "emergency_contact_phone": "+44 7700 900124",
  "lease_start_date": "2024-01-01",
  "lease_end_date": "2024-12-31",
  "rent_amount": 450.0,
  "security_deposit": 450.0,
  "agency_fees": 50.0,
  "rent_review_date": "2024-07-01",
  "notes": "Preferred tenant, excellent references"
}
```

### Get Tenant Details

```http
GET /tenants/{tenant_id}
```

**Response:**

```json
{
  "id": "tenant-123",
  "property_id": "prop-123",
  "property_address": "123 Main St, London",
  "room_unit": "Room 1",
  "tenant_name": "John Smith",
  "contact_phone": "+44 7700 900123",
  "contact_email": "john.smith@email.com",
  "emergency_contact_name": "Jane Smith",
  "emergency_contact_phone": "+44 7700 900124",
  "lease_details": {
    "start_date": "2024-01-01",
    "end_date": "2024-12-31",
    "rent_amount": 450.0,
    "security_deposit": 450.0,
    "agency_fees": 50.0,
    "rent_review_date": "2024-07-01"
  },
  "rent_payment_history": [
    {
      "payment_date": "2025-01-01",
      "amount": 450.0,
      "status": "paid",
      "payment_method": "bank_transfer"
    }
  ],
  "is_active": true,
  "notes": "Preferred tenant, excellent references",
  "created_at": "2024-01-01T10:00:00Z",
  "updated_at": "2025-01-15T10:30:00Z"
}
```

### Update Tenant

```http
PUT /tenants/{tenant_id}
```

### Remove Tenant

```http
DELETE /tenants/{tenant_id}
```

### Record Rent Payment

```http
POST /rent-payments
```

**Request Body:**

```json
{
  "tenant_id": "tenant-123",
  "payment_date": "2025-01-01",
  "amount": 450.0,
  "payment_method": "bank_transfer",
  "reference": "RENT-JAN-2025",
  "status": "paid",
  "notes": "On time payment"
}
```

### Get Occupancy Data

```http
GET /occupancy/{property_id}
```

**Response:**

```json
{
  "property_id": "prop-123",
  "current_occupancy_rate": 85.0,
  "total_units": 4,
  "occupied_units": 3,
  "vacant_units": 1,
  "vacancy_periods": [
    {
      "room_unit": "Room 2",
      "vacancy_start_date": "2024-12-15",
      "vacancy_end_date": null,
      "days_vacant": 31,
      "action_taken": "Listed on SpareRoom, viewings scheduled"
    }
  ],
  "occupancy_history": [
    {
      "month": "2024-12",
      "occupancy_rate": 75.0,
      "vacant_days": 31
    }
  ],
  "average_vacancy_period": 21,
  "tenant_turnover_rate": 25.0
}
```

## Compliance Tracking Endpoints

### Get Compliance Overview

```http
GET /compliance
```

**Response:**

```json
{
  "overall_status": "warning",
  "properties_compliant": 4,
  "properties_warning": 1,
  "properties_non_compliant": 0,
  "expiring_soon": [
    {
      "property_id": "prop-123",
      "address": "123 Main St, London",
      "certificate_type": "gas_safety",
      "expiry_date": "2025-02-15",
      "days_until_expiry": 31,
      "status": "warning"
    }
  ],
  "overdue": [],
  "upcoming_renewals": [
    {
      "property_id": "prop-456",
      "certificate_type": "electrical",
      "expiry_date": "2025-03-01",
      "days_until_expiry": 45
    }
  ]
}
```

### Get Property Compliance Status

```http
GET /compliance/{property_id}
```

**Response:**

```json
{
  "property_id": "prop-123",
  "address": "123 Main St, London",
  "compliance_status": "warning",
  "last_updated": "2025-01-15T10:30:00Z",
  "insurance": {
    "type": "Buildings & Contents",
    "policy_number": "INS-123456",
    "expiry_date": "2025-06-30",
    "status": "compliant",
    "days_until_expiry": 166
  },
  "safety_checks": {
    "fire_safety_check_date": "2024-12-01",
    "smoke_detector_check_date": "2024-12-01",
    "co2_detector_check_date": "2024-12-01",
    "next_safety_check": "2025-06-01"
  },
  "certificates": {
    "electrical": {
      "expiry_date": "2025-08-15",
      "status": "compliant",
      "days_until_expiry": 212
    },
    "gas_safety": {
      "expiry_date": "2025-02-15",
      "status": "warning",
      "days_until_expiry": 31
    },
    "hmo_license": {
      "expiry_date": "2025-12-31",
      "status": "compliant",
      "days_until_expiry": 350
    }
  }
}
```

### Add Certificate

```http
POST /certificates
```

**Request Body:**

```json
{
  "property_id": "prop-123",
  "certificate_type": "gas_safety",
  "issue_date": "2024-02-15",
  "expiry_date": "2025-02-15",
  "certificate_number": "GAS-123456",
  "contractor_name": "Gas Safe Engineer Ltd",
  "contractor_contact": "+44 7700 900999",
  "notes": "Annual gas safety check completed"
}
```

### Update Certificate

```http
PUT /certificates/{certificate_id}
```

### Get Expiring Certificates

```http
GET /certificates/expiring
```

**Query Parameters:**

- `days_ahead` (optional): Days to look ahead (default: 30)

**Response:**

```json
{
  "expiring_certificates": [
    {
      "id": "cert-123",
      "property_id": "prop-123",
      "address": "123 Main St, London",
      "certificate_type": "gas_safety",
      "expiry_date": "2025-02-15",
      "days_until_expiry": 31,
      "contractor_name": "Gas Safe Engineer Ltd",
      "contractor_contact": "+44 7700 900999"
    }
  ],
  "total_expiring": 1
}
```

### Upload Document

```http
POST /documents
```

**Request Body:** (multipart/form-data)

```
property_id: prop-123
document_type: certificate
document_name: Gas Safety Certificate 2025
description: Annual gas safety certificate
file: [binary file data]
```

**Response:**

```json
{
  "id": "doc-123",
  "document_name": "Gas Safety Certificate 2025",
  "document_type": "certificate",
  "file_size": 1048576,
  "upload_url": "/api/v1/portfolio/documents/download/doc-123",
  "created_at": "2025-01-15T10:30:00Z"
}
```

## AI Analysis Endpoints

### Get AI Property Analysis

```http
GET /ai/analysis/{property_id}
```

**Response:**

```json
{
  "property_id": "prop-123",
  "analysis_date": "2025-01-15T10:30:00Z",
  "overall_score": 8.2,
  "performance_rating": "excellent",
  "key_insights": [
    "Property is outperforming local market by 13.7%",
    "Rental yield is above average for the area",
    "Low maintenance costs indicate good property condition"
  ],
  "recommendations": [
    {
      "type": "rent_optimization",
      "priority": "high",
      "title": "Consider rent increase",
      "description": "Market analysis suggests rent could be increased by 5-8% based on local comparables",
      "potential_impact": "£40-65 additional monthly income",
      "confidence": 0.85
    }
  ],
  "risk_factors": [
    {
      "type": "compliance",
      "severity": "medium",
      "description": "Gas safety certificate expires in 31 days",
      "action_required": "Schedule renewal with certified engineer"
    }
  ],
  "market_analysis": {
    "local_yield_average": 3.8,
    "property_yield": 4.32,
    "market_trend": "increasing",
    "comparable_properties": 12,
    "price_per_sqft": 294.12
  }
}
```

### Get AI Recommendations

```http
GET /ai/recommendations/{property_id}
```

**Query Parameters:**

- `category` (optional): Filter by category (rent, maintenance, compliance, market)
- `priority` (optional): Filter by priority (high, medium, low)

**Response:**

```json
{
  "property_id": "prop-123",
  "recommendations": [
    {
      "id": "rec-123",
      "type": "rent_optimization",
      "category": "rent",
      "priority": "high",
      "title": "Optimize rental income",
      "description": "Based on market analysis, consider increasing rent by 5-8%",
      "potential_impact": "£40-65 additional monthly income",
      "confidence": 0.85,
      "supporting_data": {
        "current_rent": 450.0,
        "suggested_rent_range": [472.5, 486.0],
        "market_comparables": 8,
        "last_increase": "2023-07-01"
      },
      "action_steps": [
        "Review local comparable properties",
        "Check lease terms for rent review clause",
        "Provide 1-month notice to tenant",
        "Document market evidence"
      ],
      "created_at": "2025-01-15T10:30:00Z"
    }
  ],
  "total_recommendations": 3,
  "high_priority": 1,
  "medium_priority": 2,
  "low_priority": 0
}
```

### Get Portfolio-Wide AI Insights

```http
GET /ai/portfolio-insights
```

**Response:**

```json
{
  "portfolio_score": 7.8,
  "total_properties": 5,
  "performance_summary": {
    "top_performer": {
      "property_id": "prop-123",
      "address": "123 Main St, London",
      "score": 8.2,
      "yield": 4.32
    },
    "underperformer": {
      "property_id": "prop-456",
      "address": "456 Oak Ave, Manchester",
      "score": 6.1,
      "yield": 2.8
    }
  },
  "portfolio_recommendations": [
    {
      "type": "diversification",
      "priority": "medium",
      "title": "Consider geographic diversification",
      "description": "60% of portfolio is concentrated in London. Consider properties in other high-yield areas.",
      "potential_impact": "Reduced risk and potentially higher yields"
    }
  ],
  "market_opportunities": [
    {
      "location": "Birmingham",
      "opportunity_type": "emerging_market",
      "yield_potential": 5.2,
      "risk_level": "medium",
      "description": "Strong rental demand and property price growth expected"
    }
  ],
  "risk_assessment": {
    "overall_risk": "low",
    "concentration_risk": "medium",
    "market_risk": "low",
    "compliance_risk": "low"
  }
}
```

### Request New AI Analysis

```http
POST /ai/analyze
```

**Request Body:**

```json
{
  "property_id": "prop-123",
  "analysis_type": "comprehensive",
  "include_market_data": true,
  "include_predictions": true
}
```

**Response:**

```json
{
  "analysis_id": "analysis-789",
  "status": "processing",
  "estimated_completion": "2025-01-15T10:35:00Z",
  "webhook_url": "/api/v1/portfolio/ai/analysis-complete"
}
```

## Error Codes

| Code                     | Description                                    |
| ------------------------ | ---------------------------------------------- |
| `PLAN_LIMIT_EXCEEDED`    | User has reached their subscription plan limit |
| `PROPERTY_NOT_FOUND`     | Requested property does not exist              |
| `TENANT_NOT_FOUND`       | Requested tenant does not exist                |
| `INVALID_DATE_RANGE`     | Date range parameters are invalid              |
| `FILE_TOO_LARGE`         | Uploaded file exceeds size limit               |
| `UNSUPPORTED_FILE_TYPE`  | File type not supported                        |
| `AI_SERVICE_UNAVAILABLE` | AI analysis service is temporarily unavailable |
| `INSUFFICIENT_DATA`      | Not enough data for requested analysis         |
| `DUPLICATE_PROPERTY`     | Property already exists in portfolio           |
| `INVALID_FINANCIAL_DATA` | Financial data validation failed               |

## Webhooks

### Analysis Complete

Triggered when AI analysis is completed:

```json
{
  "event": "ai.analysis.completed",
  "analysis_id": "analysis-789",
  "property_id": "prop-123",
  "status": "completed",
  "results_url": "/api/v1/portfolio/ai/analysis/analysis-789"
}
```

### Certificate Expiry Alert

Triggered when certificates are approaching expiry:

```json
{
  "event": "compliance.certificate.expiring",
  "property_id": "prop-123",
  "certificate_type": "gas_safety",
  "expiry_date": "2025-02-15",
  "days_until_expiry": 7
}
```

## SDKs and Libraries

### JavaScript/TypeScript

```bash
npm install @propertyestateai/portfolio-sdk
```

```typescript
import { PortfolioClient } from '@propertyestateai/portfolio-sdk'

const client = new PortfolioClient({
  apiKey: 'your-api-key',
  baseUrl: 'https://api.propertyestateai.com',
})

// Get portfolio overview
const overview = await client.portfolio.getOverview()

// Add property
const property = await client.properties.add({
  address: '123 Main St, London',
  purchasePrice: 200000,
  // ... other fields
})
```

### Python

```bash
pip install propertyestateai-portfolio
```

```python
from propertyestateai import PortfolioClient

client = PortfolioClient(
    api_key='your-api-key',
    base_url='https://api.propertyestateai.com'
)

# Get portfolio overview
overview = client.portfolio.get_overview()

# Add property
property = client.properties.add(
    address='123 Main St, London',
    purchase_price=200000,
    # ... other fields
)
```

## Support

For API support, please contact:

- Email: api-support@propertyestateai.com
- Documentation: https://docs.propertyestateai.com
- Status Page: https://status.propertyestateai.com
