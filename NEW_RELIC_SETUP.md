# New Relic Monitoring Setup Guide

This document explains how New Relic monitoring has been integrated into the fullstack-checkout-service application.

## Overview

New Relic APM (Application Performance Monitoring) has been added to provide:
- Real-time performance monitoring
- Error tracking and alerting
- Distributed tracing
- Transaction analysis
- Custom instrumentation capabilities

## Files Added/Modified

### 1. `newrelic.ini` (New)
Configuration file for the New Relic Python agent. Contains:
- License key and app name configuration (via environment variables)
- Distributed tracing settings
- Transaction tracer configuration
- Error collector settings
- Environment-specific configurations

### 2. `requirements.txt` (Modified)
Added `newrelic==11.0.1` dependency.

### 3. `main.py` (Modified)
Added New Relic agent initialization at the top of the file with proper error handling:
```python
import os
import logging

try:
    import newrelic.agent
    newrelic.agent.initialize('newrelic.ini', os.getenv('NEW_RELIC_ENVIRONMENT', 'development'))
except Exception as e:
    logging.warning(f"New Relic agent initialization failed: {e}. Application will run without monitoring.")
```

### 4. `.env.example` (New)
Template file showing required environment variables.

### 5. `README.md` (Modified)
Added comprehensive New Relic documentation.

## Quick Start

### 1. Set Environment Variables

You need to set these environment variables before running the application:

```bash
export NEW_RELIC_LICENSE_KEY="your_actual_license_key"
export NEW_RELIC_APP_NAME="Fullstack Checkout Service"
export NEW_RELIC_ENVIRONMENT="production"  # Optional: development, staging, or production
```

Or create a `.env` file (based on `.env.example`):
```bash
cp .env.example .env
# Edit .env with your actual values
```

### 2. Get Your New Relic License Key

1. Sign up at https://newrelic.com (free tier available)
2. Navigate to: Account Settings > API Keys
3. Copy your license key (starts with a long string of characters)

### 3. Run the Application

```bash
pip install -r requirements.txt
python main.py
```

The application will:
- Initialize New Relic if credentials are provided
- Log a warning and continue if credentials are missing
- Start monitoring transactions, errors, and performance automatically

## What Gets Monitored

With New Relic enabled, you'll automatically get:

### Application Performance
- Response times for all API endpoints
- Throughput (requests per minute)
- Error rates
- Apdex score (user satisfaction metric)

### Transactions
- `/api/products` - Product catalog browsing
- `/api/products/{id}` - Individual product views
- `/api/checkout` - Checkout processing
- `/api/orders` - Order retrieval
- `/api/orders/{id}` - Individual order details

### Errors
- Unhandled exceptions
- HTTP error responses (4xx, 5xx)
- Stack traces and context

### Database/External Calls
- Currently uses in-memory storage, but New Relic will automatically detect:
  - Future database queries (PostgreSQL, MySQL, etc.)
  - External HTTP calls
  - Redis/cache operations

## Viewing Your Data

After starting the application with New Relic configured:

1. Log into https://one.newrelic.com
2. Navigate to "APM & Services"
3. Find your application (named according to `NEW_RELIC_APP_NAME`)
4. View:
   - **Summary**: Overall health and key metrics
   - **Transactions**: Detailed performance by endpoint
   - **Errors**: Error analytics and stack traces
   - **Distributed tracing**: Request flow visualization
   - **Logs**: Application logs in context

## Advanced Configuration

### Custom Instrumentation

You can add custom instrumentation for specific functions:

```python
import newrelic.agent

@newrelic.agent.function_trace()
def custom_function():
    # Your code here
    pass
```

### Custom Attributes

Add business context to transactions:

```python
import newrelic.agent

# In your endpoint
newrelic.agent.add_custom_attribute('customer_tier', 'premium')
newrelic.agent.add_custom_attribute('order_value', total_amount)
```

### Custom Events

Track business-specific events:

```python
import newrelic.agent

newrelic.agent.record_custom_event('OrderPlaced', {
    'order_id': order_id,
    'total_amount': total_amount,
    'item_count': len(items)
})
```

## Troubleshooting

### Application Won't Start
- Check that `newrelic.ini` exists in the project root
- Verify environment variables are set correctly
- Check logs for initialization errors

### No Data in New Relic
- Verify `NEW_RELIC_LICENSE_KEY` is correct
- Check that the application is receiving traffic
- Ensure `monitor_mode = true` in `newrelic.ini` for your environment
- Data may take 2-3 minutes to appear initially

### Running Without New Relic
The application is designed to work with or without New Relic:
- If New Relic is not configured, a warning is logged
- All functionality works normally
- No performance impact when not monitoring

## Production Considerations

### Environment Variables
- Never commit your license key to source control
- Use secure secret management in production (AWS Secrets Manager, Azure Key Vault, etc.)
- Set appropriate `NEW_RELIC_ENVIRONMENT` value

### Performance
- New Relic agent has minimal overhead (~1-2% CPU)
- Sampling can be configured in `newrelic.ini` if needed
- Consider using "high security mode" for sensitive applications

### Alerts
Set up alerts in New Relic UI for:
- Response time > 1 second
- Error rate > 5%
- Apdex score < 0.8
- Throughput drops

## Resources

- [New Relic Python Agent Documentation](https://docs.newrelic.com/docs/apm/agents/python-agent/)
- [FastAPI Instrumentation Guide](https://docs.newrelic.com/docs/apm/agents/python-agent/getting-started/instrumented-python-packages/#fastapi)
- [Best Practices Guide](https://docs.newrelic.com/docs/new-relic-solutions/best-practices-guides/)
- [NRQL Query Language](https://docs.newrelic.com/docs/query-your-data/nrql-new-relic-query-language/)

## Support

For issues with:
- **This integration**: Open an issue in this repository
- **New Relic agent**: https://support.newrelic.com
- **New Relic documentation**: https://docs.newrelic.com

---

*Last updated: 2025-10-17*
