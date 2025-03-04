# Monitoring and Analytics

ChainFree provides comprehensive monitoring and analytics tools to help you track your API usage, understand performance patterns, and ensure you're getting the most out of our service.

## Dashboard Overview

The ChainFree dashboard provides a central location to view all your usage metrics. Log in to your account and navigate to the dashboard to see:

### Usage Statistics
Track your total requests, success rates, and usage patterns across different chains and API endpoints.

### Performance Metrics
Monitor response times, error rates, and overall system performance across all your API calls.

### Usage History
View historical usage data and trends to better understand your usage patterns over time.

### Alerts
Set up custom alerts for usage thresholds, error rates, and other important metrics.

> **Note**: All data in the dashboard is updated in real-time, with a delay of no more than 60 seconds.

## Usage Statistics

The Usage Statistics section provides detailed information about your API usage. This includes:

- Total number of requests made during the current billing period
- Requests remaining before hitting your plan's limit
- Breakdown of requests by blockchain network
- Success and error rates
- Most frequently used API methods

### Understanding the Usage Graph

The main usage graph shows your API calls over time. You can:

- Adjust the time range (day, week, month, custom)
- Filter by specific blockchain networks
- Filter by API method
- Toggle between total requests, successful requests, or error counts

### Identifying Usage Patterns

Pay attention to usage patterns to optimize your API usage. For example:

- Sudden spikes may indicate inefficient batching or potential issues in your application
- Regular patterns can help with capacity planning
- Low usage periods might be good times to schedule maintenance

## Performance Metrics

Performance metrics help you understand the reliability and speed of your API calls. Key metrics include:

| Metric | Description | Target |
|--------|-------------|--------|
| Response Time | Average time to process your request and return a response | < 100ms |
| Success Rate | Percentage of requests that complete successfully | > 99.9% |
| Error Rate | Percentage of requests that result in errors | < 0.1% |
| Node Latency | Time for our nodes to process blockchain requests | < 50ms |

These metrics are displayed in real-time on your dashboard and can be filtered by blockchain network, API method, and time range.

## Setting Up Alerts

ChainFree allows you to set up custom alerts to monitor your usage and performance. This helps you stay informed about important events without constantly checking the dashboard.

### Available Alert Types

- Usage threshold alerts (e.g., 80% of monthly limit reached)
- Error rate alerts (e.g., error rate exceeds 1%)
- Response time alerts (e.g., average response time exceeds 200ms)
- Rate limiting alerts (when approaching or hitting rate limits)

### Creating an Alert

To create a new alert:

1. Navigate to the Alerts section in your dashboard
2. Click "Add Alert"
3. Select the alert type
4. Configure the alert threshold and conditions
5. Set up notification methods (email, webhook, etc.)
6. Save the alert

### Alert Notification Methods

Alerts can be delivered through various channels:

- Email notifications
- Webhook notifications to your own systems
- Slack notifications
- PagerDuty integration (Professional and Unlimited plans)
- SMS notifications (Unlimited plan only)

## API Token Analytics

If you use multiple API tokens, you can track the usage of each token separately. This is especially useful for organizations with multiple applications or teams using the same account.

To view token-specific analytics:

1. Navigate to the "API Tokens" section in your dashboard
2. Select the token you want to analyze
3. View detailed usage metrics for that specific token

## Error Analysis

The Error Analysis section helps you identify and troubleshoot issues with your API requests. It provides detailed information about errors, including:

- Error types and frequencies
- Error messages and codes
- Timestamps and patterns
- Associated blockchain networks and methods

Common error types include:

| Error Code | Description | Common Causes |
|------------|-------------|---------------|
| 401 | Unauthorized | Invalid or expired API token |
| 429 | Too Many Requests | Rate limit or monthly quota exceeded |
| 500 | Internal Server Error | Issue with our infrastructure |
| 502 | Bad Gateway | Problems with underlying blockchain node |

## Dashboard Navigation Tips

Here are some tips to help you navigate the dashboard effectively:

### Time Range Selector
Use the time range selector in the top-right corner to adjust the time period for all charts and metrics. Predefined ranges include: 24 hours, 7 days, 30 days, and custom range.

### Auto-Refresh
The dashboard auto-refreshes every 60 seconds. You can manually refresh by clicking the refresh button in the top-right corner of each panel.

### Dashboard Customization
You can customize your dashboard by rearranging panels, adding new panels, or removing existing ones. Click the "Customize" button in the top-right corner to enter customization mode.

## Exporting Data

You can export your monitoring data for further analysis or reporting:

- CSV export for raw data
- PDF export for dashboard snapshots
- JSON export for integration with other tools

To export data:

1. Navigate to the panel or view you want to export
2. Click the export icon in the top-right corner
3. Select your preferred export format
4. Configure any export options (time range, data granularity, etc.)
5. Download the exported file

## API for Metrics

In addition to the dashboard, you can access your metrics programmatically through our Metrics API. This allows you to integrate ChainFree usage data into your own monitoring systems.

To access the Metrics API:

```bash
# Get overall usage metrics
curl -X GET "https://api.chainfree.io/api/v1/metrics/user" \
  -H "Authorization: Bearer YOUR_API_TOKEN"

# Get metrics for a specific time period
curl -X GET "https://api.chainfree.io/api/v1/metrics/user?start=2023-01-01T00:00:00Z&end=2023-01-31T23:59:59Z" \
  -H "Authorization: Bearer YOUR_API_TOKEN"

# Get metrics for a specific blockchain
curl -X GET "https://api.chainfree.io/api/v1/metrics/user?chain=eth" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

The API returns metrics in JSON format, which can be easily integrated into dashboards like Grafana, Datadog, or your custom monitoring solution.

## Best Practices for Monitoring

To get the most out of ChainFree's monitoring capabilities:

- Set up alerts for critical thresholds to avoid surprises
- Regularly review your usage patterns to optimize API calls
- Monitor error rates and investigate spikes promptly
- Track performance across different blockchain networks
- Use token-specific analytics for multi-application accounts
- Export historical data for long-term trend analysis

## Monitoring Metrics by Plan

Different subscription plans have access to different levels of monitoring metrics:

| Feature | Free | Developer | Professional | Unlimited |
|---------|------|-----------|--------------|-----------|
| Basic Usage Stats | ✓ | ✓ | ✓ | ✓ |
| Historical Data | 7 days | 30 days | 90 days | 1 year |
| Token-Level Analytics | Limited | ✓ | ✓ | ✓ |
| Custom Dashboards | - | Limited | ✓ | ✓ |
| Alert Notifications | Email only | Email, Webhook | Email, Webhook, Slack | All channels |
| Metrics API | - | Limited | ✓ | ✓ |
| Export Formats | CSV | CSV, JSON | All formats | All formats |

## Troubleshooting

If you encounter issues with the monitoring dashboard:

### Dashboard Not Loading
Ensure you're using a supported browser (Chrome, Firefox, Safari, Edge) and have JavaScript enabled. Try clearing your browser cache and cookies.

### Missing Data
Check your time range settings. Data might be outside the currently selected time period. Also, there might be a slight delay (up to 60 seconds) for the latest data to appear.

### Alerts Not Triggering
Verify your alert configuration, including thresholds and notification settings. Check your email spam folder for alert notifications.

## Need Help?

If you need assistance with monitoring or analytics:

- Check our [FAQ section](/docs/faq)
- Email us at support@chainfree.io
- Professional and Unlimited plans can access priority support