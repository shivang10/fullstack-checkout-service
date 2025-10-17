# New Relic Browser Monitoring Setup Guide

This guide will help you configure New Relic Browser Monitoring for the fullstack-checkout-service application.

## 📋 Overview

New Relic Browser Monitoring has been integrated into the application with a placeholder configuration in `static/index.html`. Follow this guide to complete the setup with your actual New Relic credentials.

## 🎯 Benefits of Browser Monitoring

Browser monitoring provides:
- ✅ **Real User Monitoring (RUM)**: Actual user experience metrics
- ✅ **Page Load Performance**: Detailed timing breakdown of page loads
- ✅ **JavaScript Errors**: Frontend error tracking with stack traces
- ✅ **AJAX Performance**: API call monitoring from the browser
- ✅ **Session Traces**: Visual timeline of user sessions
- ✅ **Geographic Data**: User location and performance by region

## 🚀 Step-by-Step Setup

### Step 1: Create Browser Application in New Relic

1. **Log into New Relic One**
   - Visit: https://one.newrelic.com/
   - Sign in with your New Relic account

2. **Navigate to Browser Monitoring**
   - Click on "Browser" in the left navigation menu
   - Click "Add more" or "Add a Browser app"

3. **Select Deployment Method**
   - Choose **"Copy/Paste JavaScript code"** method
   - This is the recommended approach for applications without backend integration

4. **Configure Your Application**
   - **App name**: `fullstack-checkout-service-browser`
   - **Enable distributed tracing**: Yes (if you have APM installed)
   - Click "Enable"

5. **Copy the JavaScript Snippet**
   - New Relic will generate a JavaScript snippet
   - This snippet is unique to your application
   - Copy the entire snippet (it will look like the example below)

### Step 2: Update Your Application

1. **Open the HTML File**
   ```bash
   nano static/index.html
   ```
   Or use your preferred editor.

2. **Locate the Placeholder**
   - Find the New Relic Browser Agent section in the `<head>` tag
   - Look for the placeholder script with `YOUR_NEW_RELIC_LICENSE_KEY`

3. **Replace the Placeholder**
   - Remove the placeholder script entirely
   - Paste your actual New Relic Browser agent snippet
   - The real snippet should look similar to this:
   
   ```html
   <script type="text/javascript">
       window.NREUM||(NREUM={});
       NREUM.init={
           privacy:{cookies_enabled:true},
           ajax:{deny_list:["bam.nr-data.net"]},
           distributed_tracing:{enabled:true,cors_use_newrelic_header:true,cors_use_tracecontext_headers:true,allowed_origins:[]}
       };
       NREUM.loader_config={
           accountID:"YOUR_ACCOUNT_ID",
           trustKey:"YOUR_TRUST_KEY",
           agentID:"YOUR_AGENT_ID",
           licenseKey:"YOUR_LICENSE_KEY",
           applicationID:"YOUR_APP_ID"
       };
       NREUM.info={
           beacon:"bam.nr-data.net",
           errorBeacon:"bam.nr-data.net",
           licenseKey:"YOUR_LICENSE_KEY",
           applicationID:"YOUR_APP_ID",
           sa:1
       };
       // ... additional New Relic code ...
   </script>
   ```

### Step 3: Add Custom Attributes (Optional)

After the New Relic Browser agent script, you can add custom attributes to enhance monitoring:

```html
<script type="text/javascript">
    // Custom attributes for enhanced monitoring
    if (window.NREUM && NREUM.setCustomAttribute) {
        NREUM.setCustomAttribute('environment', 'production'); // or 'staging', 'development'
        NREUM.setCustomAttribute('appVersion', '1.0.0');
        NREUM.setCustomAttribute('service', 'checkout-service');
        
        // Add user-specific attributes when user is logged in
        // NREUM.setCustomAttribute('user_id', userId);
        // NREUM.setCustomAttribute('user_tier', 'premium');
    }
</script>
```

### Step 4: Test in Development

1. **Start the Application**
   ```bash
   python main.py
   ```

2. **Open in Browser**
   - Visit: http://localhost:8000/shop
   - Open browser DevTools (F12)
   - Check the Console tab for any New Relic-related messages

3. **Verify Script Loading**
   - In DevTools Network tab, look for requests to `bam.nr-data.net`
   - These indicate that New Relic Browser agent is active

### Step 5: Verify Data in New Relic

1. **Wait for Data**
   - It may take 5-10 minutes for data to appear in New Relic
   - Generate some activity by browsing the application

2. **Check New Relic Dashboard**
   - Go to New Relic One → Browser
   - Select your application: `fullstack-checkout-service-browser`
   - You should see:
     - Page views
     - AJAX requests
     - JavaScript errors (if any)
     - Page load times

3. **Explore Key Metrics**
   - **Page Views**: See which pages users visit most
   - **Session Traces**: View detailed timeline of user sessions
   - **AJAX Requests**: Monitor API calls to `/api/products`, `/api/checkout`
   - **JavaScript Errors**: Track frontend errors and exceptions
   - **Geo Data**: See where your users are located

### Step 6: Set Up Alerts (Recommended)

1. **Create Alert Policies**
   - Navigate to Alerts & AI → Alert policies
   - Create a new policy for Browser monitoring

2. **Recommended Alerts**
   - **Page Load Time**: Alert when page load > 5 seconds
   - **JavaScript Error Rate**: Alert when error rate > 5%
   - **AJAX Response Time**: Alert when API calls > 2 seconds
   - **Page Views Drop**: Alert when page views drop by 50%

3. **Configure Notification Channels**
   - Email, Slack, PagerDuty, etc.
   - Set up team notification preferences

## 🔗 Linking with APM (Optional)

If you have New Relic APM monitoring your FastAPI backend, the Browser agent can automatically link to it:

1. **Ensure APM is Installed**
   - Install New Relic Python agent: `pip install newrelic`
   - Configure APM in your backend

2. **Enable Distributed Tracing**
   - Both Browser and APM must have distributed tracing enabled
   - This provides end-to-end transaction tracing

3. **View Connected Data**
   - In New Relic, you'll see the full journey:
     - Browser → AJAX call → Backend API → Database
   - This helps identify bottlenecks across the stack

## 📊 Custom Dashboards

Create custom dashboards to track key metrics:

### E-commerce Specific Metrics

```javascript
// In your app.js, add custom events
// Note: These tracking functions are already implemented in the app.js file.
// Below are examples showing how they are structured:
// These functions assume access to the global 'cart' array variable defined in app.js

// Track product views
function trackProductView(productId, productName, price) {
    if (window.NREUM && NREUM.addPageAction) {
        NREUM.addPageAction('ProductView', {
            productId: productId,
            productName: productName,
            price: price
        });
    }
}

// Track add to cart events
// Note: This function accesses the global 'cart' variable to calculate metrics
function trackAddToCart(productId, productName, quantity, price) {
    if (window.NREUM && NREUM.addPageAction) {
        NREUM.addPageAction('AddToCart', {
            productId: productId,
            productName: productName,
            quantity: quantity,
            price: price,
            // Calculate total items across all cart entries
            totalCartItems: cart.reduce((sum, item) => sum + item.quantity, 0),
            // Count number of unique products in cart
            uniqueProductsInCart: cart.length
        });
    }
}

// Track checkout started
// Note: itemCount (total quantity) and totalAmount are passed as parameters
// cartItems (number of unique products) uses global cart variable
function trackCheckoutStarted(itemCount, totalAmount) {
    if (window.NREUM && NREUM.addPageAction) {
        NREUM.addPageAction('CheckoutStarted', {
            itemCount: itemCount,          // Total quantity of all items
            totalAmount: totalAmount,       // Total cart value
            cartItems: cart.length          // Number of unique products in cart
        });
    }
}

// Track order completed
function trackOrderCompleted(orderId, totalAmount, itemCount) {
    if (window.NREUM && NREUM.addPageAction) {
        NREUM.addPageAction('OrderCompleted', {
            orderId: orderId,
            totalAmount: totalAmount,
            itemCount: itemCount
        });
    }
}
```

Then create NRQL queries in New Relic dashboards:

```sql
-- Page views over time
SELECT count(*) FROM PageView SINCE 1 day ago TIMESERIES

-- Average page load time
SELECT average(duration) FROM PageView SINCE 1 day ago

-- JavaScript errors
SELECT count(*) FROM JavaScriptError SINCE 1 day ago FACET errorMessage

-- AJAX performance
SELECT average(duration) FROM AjaxRequest SINCE 1 day ago FACET requestUrl

-- Custom page actions (if implemented)
SELECT count(*) FROM PageAction WHERE actionName = 'OrderCompleted' SINCE 1 day ago
```

## 🔧 Troubleshooting

### No Data Appearing in New Relic

1. **Check Script Loading**
   - Verify New Relic script is in the `<head>` section
   - Check browser console for errors
   - Ensure no ad blockers are interfering

2. **Verify Credentials**
   - Double-check license key and application ID
   - Ensure you copied the complete snippet from New Relic

3. **Check Network Requests**
   - Open DevTools → Network tab
   - Look for requests to `bam.nr-data.net`
   - Check if they're successful (200 status)

### High Performance Impact

1. **Use Async Loading**
   - New Relic loads asynchronously by default
   - Should have minimal impact on page load

2. **Adjust Sample Rate**
   - In New Relic UI, you can adjust sampling if needed
   - Default is usually fine for most applications

### CORS Issues

- New Relic domains are whitelisted in most configurations
- If you see CORS errors, check your server's CORS settings

## 📚 Additional Resources

- [New Relic Browser Monitoring Documentation](https://docs.newrelic.com/docs/browser/browser-monitoring/getting-started/introduction-browser-monitoring/)
- [Browser Agent API Reference](https://docs.newrelic.com/docs/browser/new-relic-browser/browser-apis/using-browser-apis/)
- [Browser Monitoring Best Practices](https://docs.newrelic.com/docs/browser/new-relic-browser/guides/guide-to-browser-monitoring/)
- [NRQL Query Language](https://docs.newrelic.com/docs/query-your-data/nrql-new-relic-query-language/get-started/introduction-nrql-new-relics-query-language/)

## 🔒 Security Considerations

1. **License Key Protection**
   - Browser agent license keys are safe to expose in client-side code
   - They are write-only keys and cannot be used to read data

2. **Sensitive Data**
   - Avoid sending PII (Personally Identifiable Information) in custom attributes
   - New Relic has data privacy controls to help with compliance

3. **Environment Separation**
   - Use different Browser applications for dev, staging, and production
   - This helps keep data separate and makes debugging easier

## 🤝 Support

For issues or questions:
- New Relic Support: https://support.newrelic.com/
- New Relic Community Forum: https://discuss.newrelic.com/
- Internal team: Contact your DevOps/monitoring team

---

**Note**: This setup was automatically prepared as part of the New Relic Browser Monitoring integration. Once you've added your actual New Relic credentials, this application will have full browser monitoring capabilities.
