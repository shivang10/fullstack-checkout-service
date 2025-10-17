# New Relic Browser Monitoring Setup Guide

## Overview

This application has been prepared for New Relic Browser Monitoring integration. The placeholder code is already in place in `static/index.html`, but you need to replace it with your actual New Relic Browser Agent snippet.

## Quick Setup

### Step 1: Create Browser Application in New Relic

1. Log into [New Relic One](https://one.newrelic.com/)
2. Navigate to **Browser** → **Add more**
3. Select **Copy/Paste JavaScript code**
4. Name your app: `fullstack-checkout-service-browser`
5. Copy the JavaScript snippet provided

### Step 2: Install the Browser Agent

1. Open `static/index.html` in your editor
2. Locate the New Relic Browser Monitoring section in the `<head>` tag (around line 8-25)
3. Replace the entire placeholder `<script>` block with your actual New Relic Browser Agent snippet
4. The snippet should look similar to this:

```html
<script type="text/javascript">
  window.NREUM||(NREUM={}),__nr_require=function(t,e,n){function r(n){if(!e[n]){var o=e[n]={exports:{}};t[n][0].call(o.exports,function(e){var o=t[n][1][e];return r(o||e)},o,o.exports)}return e[n].exports}if("function"==typeof __nr_require)return __nr_require;for(var o=0;o<n.length;o++)r(n[o]);return r}({1:[function(t,e,n){...
  /* Full New Relic Browser Agent code will be here */
</script>
```

### Step 3: Configure Custom Attributes (Optional)

After the New Relic agent script, you can add custom attributes to track additional information:

```javascript
// Track application metadata
NREUM.setCustomAttribute('app_name', 'fullstack-checkout-service');
NREUM.setCustomAttribute('environment', 'production'); // or 'development', 'staging'
NREUM.setCustomAttribute('version', '1.0.0');

// Track user information (add this in your authentication flow)
// IMPORTANT: For privacy compliance (GDPR, CCPA), use hashed/anonymized identifiers
// Do NOT send raw user IDs, emails, or other PII
// Example: Add to app.js after user logs in
if (currentUser) {
    // Use a hash function (SHA-256) to anonymize the user ID
    const userHash = await crypto.subtle.digest('SHA-256', 
        new TextEncoder().encode(currentUser.id)).then(
        h => Array.from(new Uint8Array(h)).map(b => b.toString(16).padStart(2, '0')).join('')
    );
    NREUM.setCustomAttribute('user_hash', userHash);
    NREUM.setCustomAttribute('user_type', 'customer');
}
```

### Step 4: Link to APM (Optional)

If you have a New Relic APM agent installed in your FastAPI backend:

1. The Browser agent will automatically link to APM
2. This provides end-to-end transaction tracing
3. You'll see the full user journey from browser to backend

To add APM monitoring to the FastAPI backend, add to `requirements.txt`:
```
newrelic
```

Then start your app with:
```bash
NEW_RELIC_CONFIG_FILE=newrelic.ini newrelic-admin run-program python main.py
```

### Step 5: Verify Installation

1. Deploy your changes
2. Visit your application at http://localhost:8000/shop
3. Check New Relic Browser → Page Views
4. Verify data is flowing (may take 5-10 minutes)

## Testing in Development

To test the integration:

1. Start the application:
   ```bash
   python main.py
   ```

2. Open http://localhost:8000/shop in your browser

3. Open browser developer tools (F12) and check the Console tab

4. Look for New Relic agent initialization messages

5. Check the Network tab for requests to New Relic's data collection endpoints (bam.nr-data.net)

## Custom Tracking Examples

### Track Checkout Events

Add custom events in `static/app.js`:

```javascript
// Track when user adds item to cart
function addToCart(productId) {
    const product = products.find(p => p.id === productId);
    // ... existing code ...
    
    // Track custom event
    if (typeof newrelic !== 'undefined') {
        newrelic.addPageAction('addToCart', {
            productId: productId,
            productName: product.name,
            price: product.price
        });
    }
}

// Track checkout completion
async function submitCheckout(event) {
    // ... existing code ...
    
    if (typeof newrelic !== 'undefined') {
        // Note: Avoid sending sensitive data like actual order IDs
        // Use session-based tracking or hashed identifiers instead
        newrelic.addPageAction('checkoutCompleted', {
            totalAmount: order.total_amount,
            itemCount: order.items.length,
            paymentMethod: order.payment_method
        });
    }
}
```

### Track Page Performance

```javascript
// Track custom page load timing
window.addEventListener('load', function() {
    if (typeof newrelic !== 'undefined') {
        const timing = performance.timing;
        const pageLoadTime = timing.loadEventEnd - timing.navigationStart;
        
        newrelic.addPageAction('pageLoadComplete', {
            loadTime: pageLoadTime,
            domContentLoaded: timing.domContentLoadedEventEnd - timing.navigationStart
        });
    }
});
```

## Monitoring Best Practices

### Data Privacy and Compliance

**IMPORTANT**: When implementing browser monitoring, be mindful of data privacy regulations:

1. **Never send PII (Personally Identifiable Information)**:
   - ❌ Raw user IDs, emails, names, phone numbers
   - ❌ Order IDs, transaction IDs, or other business-sensitive data
   - ✅ Use hashed/anonymized identifiers (SHA-256 recommended)
   - ✅ Use aggregate metrics (counts, totals) rather than specific identifiers

2. **Privacy Regulations**:
   - Comply with GDPR, CCPA, and other applicable privacy laws
   - Update your privacy policy to mention browser monitoring
   - Consider cookie consent requirements in your jurisdiction
   - Review New Relic's data processing agreements

3. **Data Minimization**:
   - Only track what's necessary for monitoring and debugging
   - Avoid capturing sensitive form data
   - Configure New Relic to exclude sensitive URLs or parameters
   - Regularly review and audit custom attributes

### Alert Configuration

1. **Set up Browser Alerts**: Create alerts for:
   - Page load time > 3 seconds
   - JavaScript errors > 5 per minute
   - AJAX errors > 10%

2. **Create Custom Dashboards**: Track:
   - Key user flows (browse → cart → checkout)
   - Most popular products
   - Checkout abandonment rate
   - Browser/device distribution

3. **Monitor Key Metrics**:
   - Page load time
   - Time to first byte
   - DOM processing time
   - JavaScript errors
   - AJAX response times

## Troubleshooting

### Agent Not Loading

- Check browser console for errors
- Verify the script tag is in the `<head>` section
- Ensure the New Relic snippet is complete and not truncated

### No Data in New Relic

- Wait 5-10 minutes for data to appear
- Check that you're looking at the correct time range
- Verify the application name matches
- Check browser console for blocked requests (ad blockers can interfere)

### Custom Attributes Not Showing

- Ensure `NREUM.setCustomAttribute()` is called after the agent loads
- Check the Browser Agent API is enabled in New Relic settings
- Verify attribute names follow naming conventions (no special characters)

## Resources

- [New Relic Browser Monitoring Documentation](https://docs.newrelic.com/docs/browser/browser-monitoring/getting-started/introduction-browser-monitoring/)
- [Browser Agent API Reference](https://docs.newrelic.com/docs/browser/new-relic-browser/browser-apis/using-browser-apis/)
- [Browser Monitoring Best Practices](https://docs.newrelic.com/docs/browser/new-relic-browser/guides/guide-to-browser-monitoring/)
- [SPA Monitoring](https://docs.newrelic.com/docs/browser/single-page-app-monitoring/get-started/introduction-single-page-app-monitoring/)

## Support

For issues or questions:
- New Relic Support: https://support.newrelic.com/
- Documentation: https://docs.newrelic.com/
- Community Forum: https://discuss.newrelic.com/
