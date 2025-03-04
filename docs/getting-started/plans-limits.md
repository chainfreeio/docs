# Plans and Limits

ChainFree offers a variety of plans to meet the needs of different users, from individual developers to large enterprises. Each plan comes with specific request limits and features.

## Available Plans

### Free
- **Price**: $0/month
- **Request Limit**: 3,000,000 requests/month
- **Rate Limit**: 50 requests/second
- **Features**:
  - Access to all chains
  - Basic analytics
  - Standard support

### Developer
- **Price**: $29/month
- **Request Limit**: 10,000,000 requests/month
- **Rate Limit**: 100 requests/second
- **Features**:
  - Access to all chains
  - Enhanced analytics
  - Email support
  - Additional API tokens

### Professional
- **Price**: $129/month
- **Request Limit**: 50,000,000 requests/month
- **Rate Limit**: 250 requests/second
- **Features**:
  - Priority access to all chains
  - Advanced analytics and monitoring
  - Premium support
  - Custom rate limiting options
  - Enhanced error tracking

### Unlimited
- **Price**: $329/month
- **Request Limit**: Unlimited requests
- **Rate Limit**: 500 requests/second
- **Features**:
  - Highest priority access
  - Enterprise-grade analytics
  - 24/7 dedicated support
  - Custom SLA available
  - Advanced monitoring

## Request Limits

Request limits are calculated on a monthly basis. The counter resets at the beginning of each billing cycle. For free accounts, this is the first day of each calendar month.

| Plan | Monthly Requests | Rate Limits |
|------|------------------|-------------|
| Free | 3,000,000 | 50 requests/second |
| Developer | 10,000,000 | 100 requests/second |
| Professional | 50,000,000 | 250 requests/second |
| Unlimited | Unlimited | 500 requests/second |

## Rate Limiting

In addition to monthly request limits, we implement rate limiting to ensure fair usage of our infrastructure. Rate limits are applied per API token.

When you exceed your rate limit, the API will return a `429 Too Many Requests` response. The response headers will include information about when you can retry the request:

```
HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1640995200
Retry-After: 30

{
  "error": "Too many requests",
  "message": "Rate limit exceeded. Please try again in 30 seconds."
}
```

## Checking Your Limits Programmatically

All API responses include rate limit headers to help you monitor your usage:

```
X-RateLimit-Limit: 100                  # Requests per second allowed
X-RateLimit-Remaining: 95              # Requests remaining in the current window
X-RateLimit-Reset: 1640995200          # Timestamp when the limit resets
X-MonthlyLimit-Limit: 10000000         # Monthly request limit
X-MonthlyLimit-Remaining: 9876543      # Requests remaining this month
X-MonthlyLimit-Reset: 1643673600       # Timestamp when the monthly limit resets
```

## Managing Your Usage

You can monitor your API usage in real-time through the ChainFree dashboard. This allows you to:

- Track your current usage against your monthly limit
- View usage patterns by chain and endpoint
- Set up alerts when approaching usage limits
- Upgrade your plan if you need more requests

## Custom Enterprise Plans

For large-scale applications and businesses with specific requirements, we offer custom enterprise plans that can include:

- Higher rate limits
- Custom SLAs with guaranteed uptime
- Dedicated infrastructure
- Account management support
- Custom billing options

[Contact our sales team](mailto:sales@chainfree.io) to discuss enterprise options.

## Feature Comparison

| Feature | Free | Developer | Professional | Unlimited |
|---------|------|-----------|--------------|-----------|
| Monthly Requests | 3M | 10M | 50M | Unlimited |
| Rate Limit (req/s) | 50 | 100 | 250 | 500 |
| API Tokens | 2 | 5 | 20 | Unlimited |
| Analytics History | 7 days | 30 days | 90 days | 1 year |
| Support | Email | Email, Chat | Priority | Dedicated |
| Custom Rate Limits | - | ✓ | ✓ | ✓ |
| Token-specific Analytics | Limited | ✓ | ✓ | ✓ |
| Historical Data Access | Limited | Standard | Extended | Full |
| Dedicated Nodes | - | - | - | Optional |
| SLA | - | 99.9% | 99.95% | 99.99% |
| Response Time | Best Effort | < 200ms | < 100ms | < 50ms |

## Plan Changes and Billing

### Upgrades

You can upgrade your plan at any time. The change takes effect immediately, and your billing will be prorated for the remainder of the cycle.

### Downgrades

Plan downgrades take effect at the start of your next billing cycle to ensure uninterrupted service.

### Overages

If you exceed your monthly request limit:

- **Free Plan**: API calls will receive a `429 Too Many Requests` response until the next billing cycle.
- **Paid Plans**: You will be charged for overages at the following rates:
  - Developer: $1 per 1,000,000 additional requests
  - Professional: $0.80 per 1,000,000 additional requests
  - Unlimited: N/A (unlimited requests)

## Frequently Asked Questions

### What happens if I exceed my monthly limit?

If you exceed your monthly request limit, your API calls will start receiving a `429 Too Many Requests` response until the next billing cycle. To avoid service interruption, you can upgrade to a higher tier plan at any time.

### Can I change plans in the middle of a billing cycle?

Yes, you can upgrade your plan at any time. The change takes effect immediately, and your billing will be prorated for the remainder of the cycle. When downgrading, changes will take effect at the start of your next billing cycle.

### Do unused requests roll over to the next month?

No, unused requests do not roll over. Your request count resets at the beginning of each billing cycle.

### Can I have multiple API tokens with different limits?

Yes, you can create multiple API tokens under your account, each with its own rate limit settings. However, all tokens share your account's monthly request limit.

### How are rate limits calculated?

Rate limits are calculated on a per-second basis using a sliding window algorithm. This means that at any point in time, you can make the specified number of requests within a one-second window.

### Is there a way to get notified before reaching my limits?

Yes, you can set up alerts in the dashboard to be notified when your usage reaches certain thresholds (e.g., 80% of your monthly limit).