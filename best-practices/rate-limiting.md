# Rate Limiting Best Practices

Effective rate limit management is crucial for maintaining reliable performance when using the ChainFree API. This guide provides strategies to handle rate limits gracefully and optimize your API usage.

## Understanding ChainFree Rate Limits

ChainFree implements two types of rate limits:

1. **Per-Second Rate Limits**: Maximum number of requests allowed per second
2. **Monthly Request Limits**: Total requests allowed per month

These limits vary by subscription plan:

| Plan | Requests/Second | Monthly Requests |
|------|----------------|------------------|
| Free | 50 | 3,000,000 |
| Developer | 100 | 10,000,000 |
| Professional | 250 | 50,000,000 |
| Unlimited | 500 | Unlimited |

## Identifying Rate Limit Issues

When you exceed rate limits, the API returns a `429 Too Many Requests` response with these headers:

```
HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1640995200
Retry-After: 30
```

The response body typically includes:

```json
{
  "error": "Too many requests",
  "message": "Rate limit exceeded. Please try again in 30 seconds."
}
```

## Implementing Effective Rate Limit Handling

### 1. Exponential Backoff

Implement an exponential backoff strategy to gracefully handle rate limit errors:

```javascript
async function fetchWithBackoff(url, options, maxRetries = 5) {
  let retries = 0;
  
  while (retries < maxRetries) {
    try {
      const response = await fetch(url, options);
      
      if (response.status !== 429) {
        return response;
      }
      
      // Get retry time from header or default to exponential backoff
      const retryAfter = response.headers.get('Retry-After');
      const waitTime = retryAfter ? parseInt(retryAfter, 10) * 1000 : Math.pow(2, retries) * 1000;
      
      console.log(`Rate limited. Retrying after ${waitTime}ms`);
      await new Promise(resolve => setTimeout(resolve, waitTime));
      
      retries++;
    } catch (error) {
      throw error;
    }
  }
  
  throw new Error('Max retries exceeded');
}
```

### 2. Request Queuing

Implement a request queue to control the rate of outgoing requests:

```javascript
class RequestQueue {
  constructor(requestsPerSecond) {
    this.requestsPerSecond = requestsPerSecond;
    this.queue = [];
    this.processing = false;
  }
  
  async add(requestFn) {
    return new Promise((resolve, reject) => {
      this.queue.push({ requestFn, resolve, reject });
      
      if (!this.processing) {
        this.processQueue();
      }
    });
  }
  
  async processQueue() {
    this.processing = true;
    
    while (this.queue.length > 0) {
      const requestsToProcess = Math.min(this.queue.length, this.requestsPerSecond);
      const currentBatch = this.queue.splice(0, requestsToProcess);
      
      const promises = currentBatch.map(async ({ requestFn, resolve, reject }) => {
        try {
          const result = await requestFn();
          resolve(result);
        } catch (error) {
          reject(error);
        }
      });
      
      await Promise.all(promises);
      
      // Wait for at least 1 second before processing the next batch
      await new Promise(resolve => setTimeout(resolve, 1000));
    }
    
    this.processing = false;
  }
}

// Usage:
const queue = new RequestQueue(50); // For Free tier
queue.add(() => fetch('/api/endpoint')).then(result => console.log(result));
```

### 3. Request Batching

Combine multiple operations into a single request when possible:

```javascript
// Instead of this:
const balance1 = await getBalance('address1');
const balance2 = await getBalance('address2');
const balance3 = await getBalance('address3');

// Do this:
const balances = await getBatchBalances(['address1', 'address2', 'address3']);
```

For Ethereum and other JSON-RPC compatible chains, you can use batch requests:

```javascript
const batchRequest = [
  {
    "jsonrpc": "2.0",
    "method": "eth_getBalance",
    "params": ["0x742d35Cc6634C0532925a3b844Bc454e4438f44e", "latest"],
    "id": 1
  },
  {
    "jsonrpc": "2.0",
    "method": "eth_getBalance",
    "params": ["0x742d35Cc6634C0532925a3b844Bc454e4438f44f", "latest"],
    "id": 2
  }
];

fetch('https://api.chainfree.io/eth/YOUR_API_TOKEN', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(batchRequest)
});
```

### 4. Implement Caching

Cache responses to reduce repeat requests:

```javascript
const cache = new Map();
const CACHE_TTL = 60000; // 1 minute in milliseconds

async function fetchWithCache(url, options, cacheKey) {
  const now = Date.now();
  
  if (cache.has(cacheKey)) {
    const { data, expiry } = cache.get(cacheKey);
    if (now < expiry) {
      return data;
    }
    cache.delete(cacheKey);
  }
  
  const response = await fetch(url, options);
  const data = await response.json();
  
  cache.set(cacheKey, {
    data,
    expiry: now + CACHE_TTL
  });
  
  return data;
}
```

Consider varying cache durations based on data type:
- Block numbers: 5-10 seconds
- Account balances: 1-5 minutes
- Historical data: 30+ minutes

### 5. Distribute Tokens and Workloads

For high-volume applications:

1. **Use multiple API tokens** to distribute workload
2. **Segment your traffic** by token based on request type or priority
3. **Set different rate limits** for different tokens

Example distribution strategy:

| Token | Purpose | Custom Rate Limit |
|-------|---------|-------------------|
| Token 1 | Critical user-facing operations | Higher limit |
| Token 2 | Background indexing | Lower limit |
| Token 3 | Analytics and monitoring | Lowest limit |

### 6. Monitor Your Usage

Proactively monitor your API usage to avoid hitting limits:

1. **Track usage metrics** in your application
2. **Set up alerts** for approaching limits (e.g., at 75% and 90%)
3. **Use ChainFree dashboard** to monitor usage patterns

## Rate Limit Response Headers

Every API response includes these headers to help you monitor your usage:

```
X-RateLimit-Limit: 100                  # Requests per second allowed
X-RateLimit-Remaining: 95               # Requests remaining in the current window
X-RateLimit-Reset: 1640995200           # Timestamp when the limit resets
X-MonthlyLimit-Limit: 10000000          # Monthly request limit
X-MonthlyLimit-Remaining: 9876543       # Requests remaining this month
X-MonthlyLimit-Reset: 1643673600        # Timestamp when the monthly limit resets
```

Check these headers to adapt your request rate dynamically.

## Custom Rate Limits for API Tokens

Paid plans allow setting custom rate limits for individual API tokens:

1. Go to the API Tokens section in your dashboard
2. Click on the token you want to modify
3. Under "Rate Limit Settings," set your custom limits
4. Click "Save Changes"

This feature allows you to reserve capacity for critical operations and better distribute your allocation.

## Plan Upgrades for Higher Limits

If you consistently hit rate limits despite optimization, consider upgrading your plan:

1. From Free to Developer: 2x rate limit increase (50 → 100 req/s)
2. From Developer to Professional: 2.5x rate limit increase (100 → 250 req/s)
3. From Professional to Unlimited: 2x rate limit increase (250 → 500 req/s)

Contact sales for custom enterprise plans with even higher limits.

## Common Rate Limiting Mistakes

1. **Not implementing backoff**: Always use backoff strategies with `Retry-After` headers
2. **Synchronous requests**: Avoid blocking request patterns that could create backlogs
3. **Insufficient caching**: Cache frequently accessed data to reduce request volume
4. **Ignoring rate limit headers**: Use headers proactively to adjust request rates
5. **Over-polling**: Avoid polling for data that doesn't change frequently

## Rate Limiting FAQs

**Q: Do rate limits apply to all chains equally?**  
A: Yes, rate limits are applied across all chains for your account.

**Q: Can I get temporary rate limit increases?**  
A: Contact support for temporary increases for special events or migrations.

**Q: How does ChainFree handle burst traffic?**  
A: Some short bursts may be accommodated, but consistent overages will be rate-limited.

**Q: Do failed requests count against my limits?**  
A: Yes, all requests count against rate limits, regardless of success or failure.