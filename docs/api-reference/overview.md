# API Overview

ChainFree provides a unified API that allows you to access multiple blockchain networks through a single endpoint. This overview will explain the key concepts and general structure of our API.

## API Endpoints

All ChainFree API requests are made to endpoints with the following structure:

```
https://api.chainfree.io/<CHAIN>/<API_TOKEN>
```

Where:
- `<CHAIN>` is the blockchain identifier (e.g., `eth`, `sol`, `bsc`)
- `<API_TOKEN>` is your ChainFree API token

## Authentication

You can authenticate with the API in two ways:

1. **URL Path**: Include your API token in the URL path as shown above
2. **Authorization Header**: Use the `Authorization` header with a Bearer token
   ```
   Authorization: Bearer <API_TOKEN>
   ```

## Protocol Types

ChainFree supports two main protocol types depending on the blockchain network:

### JSON-RPC

Used for Ethereum and compatible chains (ETH, BSC, AVAX, etc.)
- Communication via POST requests
- Standardized request/response format
- Method-based API calls

### HTTP REST

Used for non-EVM chains like Solana, Cosmos, etc.
- Standard REST API patterns
- GET/POST endpoints for different operations
- Path-based resource access

For detailed information about the differences between these protocols and how to use them, see our [HTTP vs JSON-RPC](protocols.md) documentation.

## Request Format

### JSON-RPC Requests

For Ethereum and EVM-compatible chains, requests follow the JSON-RPC 2.0 format:

```
POST https://api.chainfree.io/eth/YOUR_API_TOKEN

{
  "jsonrpc": "2.0",
  "method": "eth_blockNumber",
  "params": [],
  "id": 1
}
```

### HTTP REST Requests

For non-EVM chains like Solana, requests follow standard HTTP conventions:

```
GET https://api.chainfree.io/sol/YOUR_API_TOKEN/getBlockHeight
```

## Response Format

### JSON-RPC Responses

Responses for JSON-RPC requests follow the standard format:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0xc94fcb"  // Hexadecimal representation of the block number
}
```

In case of an error:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "code": -32602,
    "message": "Invalid params"
  }
}
```

### HTTP REST Responses

Responses for HTTP REST requests typically return JSON data:

```json
{
  "result": {
    "blockHeight": 12345678
  }
}
```

## Rate Limiting

All API requests are subject to rate limiting based on your subscription plan. Rate limit information is included in the response headers:

```
X-RateLimit-Limit: 100                  # Requests per second allowed
X-RateLimit-Remaining: 95              # Requests remaining in the current window
X-RateLimit-Reset: 1640995200          # Timestamp when the limit resets
X-MonthlyLimit-Limit: 10000000         # Monthly request limit
X-MonthlyLimit-Remaining: 9876543      # Requests remaining this month
X-MonthlyLimit-Reset: 1643673600       # Timestamp when the monthly limit resets
```

If you exceed your rate limit, you'll receive a `429 Too Many Requests` response with information about when you can retry the request.

### Rate Limit Best Practices

- Implement backoff logic when receiving 429 responses
- Use the `Retry-After` header to determine when to retry
- Batch requests when possible to reduce API calls
- Implement client-side caching for frequently accessed data

## Error Handling

ChainFree API errors are returned with appropriate HTTP status codes and detailed error messages.

| Status Code | Description | Example |
|-------------|-------------|---------|
| 400 | Bad Request - Invalid request format | Missing required parameters |
| 401 | Unauthorized - Invalid API token | API token not provided or expired |
| 404 | Not Found - Resource not found | Invalid endpoint or blockchain |
| 429 | Too Many Requests - Rate limit exceeded | Exceeded requests per second or monthly limit |
| 500 | Internal Server Error - Server-side issue | Unexpected error in processing request |
| 502 | Bad Gateway - Node connection issue | Error communicating with underlying blockchain |
| 503 | Service Unavailable - Temporary outage | Service temporarily unavailable |

## Supported Blockchains

ChainFree supports a wide range of blockchain networks. Each blockchain has its own dedicated API endpoint and methods. Here are some of the major supported blockchains:

### Ethereum (ETH)
- Protocol: JSON-RPC
- Documentation: [Ethereum API](ethereum.md)

### Solana (SOL)
- Protocol: HTTP REST
- Documentation: [Solana API](solana.md)

### Binance Smart Chain (BSC)
- Protocol: JSON-RPC
- Documentation: [Binance Smart Chain API](binance.md)

### Avalanche (AVAX)
- Protocol: JSON-RPC
- Documentation: [Avalanche API](avalanche.md)

### Polygon (MATIC)
- Protocol: JSON-RPC
- Documentation: [Polygon API](polygon.md)

For a complete list, see [All Supported Chains](protocols.md#supported-chains).

## Common Use Cases

Here are some common use cases for the ChainFree API:

### Reading Blockchain Data

Query blockchain state, including account balances, transaction history, and block information.

```
// Get account balance on Ethereum
POST https://api.chainfree.io/eth/YOUR_API_TOKEN

{
  "jsonrpc": "2.0",
  "method": "eth_getBalance",
  "params": ["0x742d35Cc6634C0532925a3b844Bc454e4438f44e", "latest"],
  "id": 1
}
```

### Monitoring Transaction Status

Track the status of transactions on the blockchain, including confirmations and receipts.

```
// Get transaction receipt on Ethereum
POST https://api.chainfree.io/eth/YOUR_API_TOKEN

{
  "jsonrpc": "2.0",
  "method": "eth_getTransactionReceipt",
  "params": ["0x85d995eba9763907fdf35cd2034144dd9d53ce32cb6134472fb25161e4b317af"],
  "id": 1
}
```

### Smart Contract Interaction

Interact with smart contracts on EVM-compatible chains by calling view functions or sending transactions.

```
// Call a contract method on Ethereum
POST https://api.chainfree.io/eth/YOUR_API_TOKEN

{
  "jsonrpc": "2.0",
  "method": "eth_call",
  "params": [
    {
      "to": "0x6b175474e89094c44da98b954eedeac495271d0f", // DAI contract
      "data": "0x70a08231000000000000000000000000742d35cc6634c0532925a3b844bc454e4438f44e" // balanceOf
    },
    "latest"
  ],
  "id": 1
}
```

## Client Libraries

ChainFree works with standard blockchain client libraries. Here are some popular options:

### JavaScript/TypeScript
- **ethers.js** - For Ethereum and EVM chains
- **web3.js** - For Ethereum and EVM chains
- **@solana/web3.js** - For Solana

### Python
- **web3.py** - For Ethereum and EVM chains
- **solana-py** - For Solana

### Go
- **go-ethereum** - For Ethereum and EVM chains
- **solana-go** - For Solana

For detailed examples of how to use these libraries with ChainFree, see our [API usage guides](../guides/api-usage/overview.md).

## Next Steps

Now that you understand the basics of the ChainFree API, you can:

- Learn about the [differences between HTTP and JSON-RPC](protocols.md)
- Explore the API reference for specific blockchains
- Follow our [practical guides](../guides/api-usage/overview.md) for working with the API
- [Generate an API token](../guides/api-tokens.md) to start making requests