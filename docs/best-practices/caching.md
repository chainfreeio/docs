# Caching Best Practices

Implementing effective caching strategies can significantly reduce the number of API calls you make to ChainFree, resulting in faster application performance, lower costs, and better reliability. This guide explains when and how to implement caching for blockchain data.

## Why Cache Blockchain Data?

Blockchain data has unique characteristics that make it particularly suitable for caching:

1. **Immutability**: Once confirmed, blockchain data doesn't change (except for reorganizations)
2. **Frequent repetition**: Many applications repeatedly request the same data
3. **Predictable staleness**: For many use cases, slightly stale data is acceptable

By implementing client-side caching, you can:

- **Reduce API costs** by lowering the number of requests to ChainFree
- **Improve performance** by serving data from local memory or storage
- **Enhance reliability** by reducing dependency on network calls
- **Stay within rate limits** by avoiding unnecessary requests

## What to Cache

Not all blockchain data should be cached in the same way. Here are recommendations for different types of data:

### Highly Cacheable (Long TTL)
- Historical blocks and transactions (confirmed with many confirmations)
- Smart contract code (rarely changes)
- Token metadata (name, symbol, decimals)
- Historical token balances at specific blocks
- NFT metadata and content

### Moderately Cacheable (Medium TTL)
- Account balances for monitoring (not for transactions)
- Recent confirmed transactions (with sufficient confirmations)
- Token supply information
- Average gas prices (for non-critical operations)

### Minimally Cacheable (Short TTL or No Cache)
- Pending transactions
- Latest block information
- Account balances for transaction creation
- Mempool data
- Current gas prices (for time-sensitive transactions)

## Caching Strategies

### Time-Based Caching (TTL)

The simplest approach is to cache responses for a predetermined time (Time To Live):

```javascript
const cache = new Map();
const TTL = 60 * 1000; // 1 minute in milliseconds

async function fetchWithCache(url, params) {
  const cacheKey = `${url}:${JSON.stringify(params)}`;
  const now = Date.now();
  
  if (cache.has(cacheKey)) {
    const { data, timestamp } = cache.get(cacheKey);
    if (now - timestamp < TTL) {
      return data; // Return cached data if still valid
    }
  }
  
  // Fetch new data
  const result = await fetchFromAPI(url, params);
  
  // Cache the new data
  cache.set(cacheKey, {
    data: result,
    timestamp: now
  });
  
  return result;
}
```

### Block-Based Caching

For Ethereum and similar chains, you can cache based on block numbers:

```javascript
const cache = new Map();
let lastCheckedBlock = 0;

async function fetchWithBlockCache(method, params) {
  const cacheKey = `${method}:${JSON.stringify(params)}`;
  
  // Check current block number periodically
  const currentBlock = await getCurrentBlock();
  
  if (currentBlock === lastCheckedBlock && cache.has(cacheKey)) {
    return cache.get(cacheKey);
  }
  
  // Fetch new data
  const result = await callJsonRpc(method, params);
  
  // Cache the result
  cache.set(cacheKey, result);
  lastCheckedBlock = currentBlock;
  
  return result;
}
```

### Confirmation-Based Caching

For transaction data, consider caching based on confirmation count:

```javascript
async function getTransaction(txHash, minConfirmations = 12) {
  const cacheKey = `tx:${txHash}`;
  
  if (cache.has(cacheKey)) {
    const { tx, confirmations } = cache.get(cacheKey);
    if (confirmations >= minConfirmations) {
      return tx; // Return cached transaction if it has enough confirmations
    }
  }
  
  const tx = await fetchTransaction(txHash);
  const currentBlock = await getCurrentBlock();
  const confirmations = tx.blockNumber ? (currentBlock - tx.blockNumber) : 0;
  
  // Cache with current confirmation count
  cache.set(cacheKey, { tx, confirmations });
  
  return tx;
}
```

### Multi-Level Caching

For production applications, consider implementing multiple cache layers:

1. **In-memory cache**: Fastest access for frequently used data
2. **Local storage cache**: Persists between sessions for the same user
3. **Shared cache**: Redis or similar for sharing between instances
4. **CDN**: For public, static blockchain data like historical blocks

## Cache Invalidation

Even with immutable blockchains, cache invalidation is important to handle:

1. **Chain reorganizations**: Short reorganizations can occur, affecting recent blocks
2. **Contract upgrades**: Smart contract code might change (especially for proxy contracts)
3. **Token metadata updates**: Some token standards allow metadata changes

Consider these invalidation strategies:

- **Time-based expiry**: Simple but might serve stale data
- **Block-based expiry**: Invalidate specific data when new blocks arrive
- **Listening for events**: Invalidate cache when specific blockchain events occur
- **Adaptive TTL**: Use longer cache times for older data and shorter for recent data

## Chain-Specific Considerations

### Ethereum and EVM-Compatible Chains

- Cache blocks with 12+ confirmations more aggressively
- For finality-aware chains (Ethereum post-merge), cache finalized data longer
- Use `eth_blockNumber` to detect new blocks and selectively invalidate cache

### Solana

- Cache confirmed blocks (those with `"finalized"` commitment)
- Use shorter cache times due to Solana's high throughput and lower block times
- Consider caching account data based on slot numbers

### Cosmos-Based Chains

- Cache data after it's been included in a finalized block
- Cache validator and delegation data with moderate TTL
- Consider chain governance when caching protocol parameters

## Implementation Examples

### Simple In-Memory Cache (JavaScript)

```javascript
class BlockchainCache {
  constructor(defaultTTL = 60000) {
    this.cache = new Map();
    this.defaultTTL = defaultTTL;
  }

  get(key, ttl = this.defaultTTL) {
    const cached = this.cache.get(key);
    if (!cached) return null;
    
    const now = Date.now();
    if (now - cached.timestamp > ttl) {
      this.cache.delete(key);
      return null;
    }
    
    return cached.data;
  }

  set(key, data) {
    this.cache.set(key, {
      data,
      timestamp: Date.now()
    });
  }

  invalidate(key) {
    this.cache.delete(key);
  }

  clear() {
    this.cache.clear();
  }
}

// Usage
const cache = new BlockchainCache();
async function getBalance(address) {
  // Check cache first with 5-minute TTL for balances
  const cachedBalance = cache.get(`balance:${address}`, 5 * 60 * 1000);
  if (cachedBalance !== null) {
    return cachedBalance;
  }
  
  const balance = await fetchBalanceFromAPI(address);
  cache.set(`balance:${address}`, balance);
  return balance;
}
```

### Persistence with LocalStorage (JavaScript)

```javascript
class PersistentCache {
  constructor(namespace = 'blockchain_cache') {
    this.namespace = namespace;
    this.memoryCache = new Map();
    this.loadFromStorage();
  }

  loadFromStorage() {
    try {
      const stored = localStorage.getItem(this.namespace);
      if (stored) {
        const data = JSON.parse(stored);
        Object.entries(data).forEach(([key, value]) => {
          this.memoryCache.set(key, value);
        });
      }
    } catch (error) {
      console.error('Failed to load cache from storage:', error);
    }
  }

  saveToStorage() {
    try {
      const data = Object.fromEntries(this.memoryCache.entries());
      localStorage.setItem(this.namespace, JSON.stringify(data));
    } catch (error) {
      console.error('Failed to save cache to storage:', error);
    }
  }

  get(key, ttl = 60000) {
    const cached = this.memoryCache.get(key);
    if (!cached) return null;
    
    const now = Date.now();
    if (now - cached.timestamp > ttl) {
      this.memoryCache.delete(key);
      this.saveToStorage();
      return null;
    }
    
    return cached.data;
  }

  set(key, data) {
    this.memoryCache.set(key, {
      data,
      timestamp: Date.now()
    });
    this.saveToStorage();
  }
}
```

### Redis Cache (Node.js)

```javascript
const Redis = require('ioredis');

class RedisBlockchainCache {
  constructor(options = {}) {
    this.client = new Redis(options.redisUrl || process.env.REDIS_URL);
    this.prefix = options.prefix || 'blockchain:';
    this.defaultTTL = options.defaultTTL || 60; // seconds
  }

  async get(key, ttl = this.defaultTTL) {
    const fullKey = this.prefix + key;
    const data = await this.client.get(fullKey);
    return data ? JSON.parse(data) : null;
  }

  async set(key, data, ttl = this.defaultTTL) {
    const fullKey = this.prefix + key;
    await this.client.set(fullKey, JSON.stringify(data), 'EX', ttl);
  }

  async invalidate(key) {
    const fullKey = this.prefix + key;
    await this.client.del(fullKey);
  }

  async close() {
    await this.client.quit();
  }
}
```

## Performance Considerations

### Cache Key Design

Design efficient cache keys:

```javascript
// Bad: Not specific enough
const key = `balance:${address}`;

// Better: Includes chain and block information
const key = `eth:balance:${address}:${blockNumber}`;

// Even better: Includes hash for complex parameters
const key = `eth:call:${contractAddress}:${sha256(callData)}`;
```

### Memory Management

For in-memory caches, implement strategies to prevent memory leaks:

1. **LRU (Least Recently Used) Policy**: Evict least used items first
2. **TTL Cleanup**: Periodically clean expired items
3. **Size Limits**: Set maximum number of cached items

Example LRU cache implementation:

```javascript
const LRU = require('lru-cache');

const options = {
  max: 500, // Maximum items in cache
  ttl: 1000 * 60 * 5, // 5 minutes
  updateAgeOnGet: true, // Update "recently used" status on get
};

const cache = new LRU(options);
```

### Cache Warming

For critical data, implement cache warming:

```javascript
async function warmCache() {
  // Prefetch common token information
  const commonTokens = ['0x...', '0x...', '0x...'];
  await Promise.all(commonTokens.map(async (address) => {
    const tokenInfo = await fetchTokenInfo(address);
    cache.set(`token:${address}`, tokenInfo, 24 * 60 * 60 * 1000); // 24 hour TTL
  }));
  
  // Prefetch latest block
  const latestBlock = await fetchLatestBlock();
  cache.set('latestBlock', latestBlock, 12 * 1000); // 12 second TTL
}
```

## Monitoring Cache Effectiveness

Track cache performance with metrics:

1. **Hit rate**: Percentage of requests served from cache
2. **Miss rate**: Percentage of requests requiring API calls
3. **Cache size**: Memory usage of the cache
4. **Load times**: Performance difference between cached and uncached requests

Example implementation:

```javascript
class MonitoredCache {
  constructor() {
    this.cache = new Map();
    this.metrics = {
      hits: 0,
      misses: 0,
      sets: 0,
    };
  }

  get(key) {
    if (this.cache.has(key)) {
      this.metrics.hits++;
      return this.cache.get(key);
    }
    this.metrics.misses++;
    return null;
  }

  set(key, value) {
    this.cache.set(key, value);
    this.metrics.sets++;
  }

  getMetrics() {
    const total = this.metrics.hits + this.metrics.misses;
    const hitRate = total > 0 ? (this.metrics.hits / total) * 100 : 0;
    
    return {
      ...this.metrics,
      hitRate: `${hitRate.toFixed(2)}%`,
      size: this.cache.size,
    };
  }
}
```

## Common Pitfalls and Solutions

### Stale Data Issues

**Problem**: Caching can lead to stale data in rapidly changing conditions.

**Solution**: Use adaptive TTL based on data volatility:

```javascript
function getAdaptiveTTL(dataType) {
  switch (dataType) {
    case 'block': return 12 * 1000; // 12 seconds
    case 'balance': return 2 * 60 * 1000; // 2 minutes
    case 'transaction': return 5 * 60 * 1000; // 5 minutes
    case 'code': return 12 * 60 * 60 * 1000; // 12 hours
    default: return 60 * 1000; // 1 minute
  }
}
```

### Cache Stampede

**Problem**: Multiple simultaneous requests for the same uncached data.

**Solution**: Implement request coalescing:

```javascript
const pendingRequests = new Map();

async function fetchWithCoalescing(key, fetchFn) {
  // If data is in cache, return it
  const cachedData = cache.get(key);
  if (cachedData) return cachedData;
  
  // If there's already a pending request for this key, wait for it
  if (pendingRequests.has(key)) {
    return pendingRequests.get(key);
  }
  
  // Create new request promise
  const requestPromise = fetchFn().then(data => {
    cache.set(key, data);
    pendingRequests.delete(key);
    return data;
  }).catch(error => {
    pendingRequests.delete(key);
    throw error;
  });
  
  // Store the pending request
  pendingRequests.set(key, requestPromise);
  
  return requestPromise;
}
```

### Blockchain Reorganizations

**Problem**: Chain reorganizations can invalidate recently cached data.

**Solution**: Add confirmation thresholds for sensitive data:

```javascript
async function getDataWithConfirmations(txHash, minConfirmations = 6) {
  const cachedData = cache.get(`tx:${txHash}`);
  
  if (cachedData) {
    const currentBlock = await getCurrentBlock();
    const txBlock = cachedData.blockNumber;
    
    if (currentBlock - txBlock >= minConfirmations) {
      return cachedData;
    }
  }
  
  // Fetch fresh data
  const data = await fetchTransaction(txHash);
  cache.set(`tx:${txHash}`, data);
  return data;
}
```

## Conclusion

Effective caching is essential for building performant and cost-efficient blockchain applications. By understanding the characteristics of blockchain data and implementing appropriate caching strategies, you can significantly reduce your API usage while improving your application's performance and reliability.

Remember these key principles:

1. **Cache immutable data longer**: Historical blocks, transactions, and contract code
2. **Use shorter TTLs for volatile data**: Latest blocks, pending transactions, current prices
3. **Implement multi-level caching** for optimal performance
4. **Monitor cache effectiveness** to fine-tune your strategy
5. **Consider chain-specific characteristics** when designing your caching logic

By following these guidelines, you'll make the most efficient use of ChainFree's API while providing a better experience for your users.