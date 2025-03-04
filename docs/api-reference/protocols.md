# HTTP vs JSON-RPC Protocols

ChainFree supports multiple blockchain networks that use different protocol types for API communication. This guide explains the differences between the two main protocols we support: JSON-RPC and HTTP REST.

## Protocol Overview

### JSON-RPC Protocol

JSON-RPC is a stateless, lightweight remote procedure call (RPC) protocol that uses JSON for data encoding. It's the standard protocol for Ethereum and Ethereum-compatible blockchains.

**Characteristics:**
- Uses POST HTTP requests
- Request and response bodies are in JSON format
- Each request includes a method name, parameters, and an ID
- Responses include the result (or error) and the same ID as the request

**Supported Chains:**
- Ethereum (ETH)
- Binance Smart Chain (BSC)
- Polygon (MATIC)
- Avalanche C-Chain (AVAX)
- Arbitrum (ARB)
- Optimism (OP)
- Base
- Scroll (SCR)
- And other EVM-compatible chains

### HTTP REST Protocol

HTTP REST uses standard HTTP methods and URL patterns to access resources. Some blockchain networks provide REST APIs rather than JSON-RPC interfaces.

**Characteristics:**
- Uses various HTTP methods (GET, POST, etc.)
- URL path specifies the resource or action
- Parameters can be in the URL path, query string, or request body
- Responses are typically in JSON format
- Follows REST principles (when implemented properly)

**Supported Chains:**
- Solana (SOL)
- Cosmos (ATOM)
- Sei (SEI)
- Kaspa
- TRON (TRX)
- Sui

## Protocol Usage Examples

### JSON-RPC Example (Ethereum)

**Request:**
```bash
curl -X POST https://api.chainfree.io/eth/YOUR_API_TOKEN \
  -H "Content-Type: application/json" \
  --data '{
    "jsonrpc": "2.0",
    "method": "eth_blockNumber",
    "params": [],
    "id": 1
  }'
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0xc94fcb"
}
```

### HTTP REST Example (Solana)

**Request:**
```bash
curl -X GET https://api.chainfree.io/sol/YOUR_API_TOKEN/getBlockHeight
```

**Response:**
```json
{
  "result": {
    "blockHeight": 12345678
  }
}
```

## JSON-RPC Format Details

### Request Structure

All JSON-RPC requests must include:

| Field    | Type             | Description                                   |
|----------|------------------|-----------------------------------------------|
| jsonrpc  | String           | JSON-RPC version (always "2.0")               |
| method   | String           | Name of the method to call                    |
| params   | Array or Object  | Parameters for the method (can be empty array)|
| id       | Number or String | Request identifier (returned in response)     |

### Response Structure

Successful responses include:

| Field    | Type             | Description                                   |
|----------|------------------|-----------------------------------------------|
| jsonrpc  | String           | JSON-RPC version (always "2.0")               |
| result   | Any              | Result of the method call                     |
| id       | Number or String | Same ID as in the request                     |

Error responses include:

| Field    | Type             | Description                                   |
|----------|------------------|-----------------------------------------------|
| jsonrpc  | String           | JSON-RPC version (always "2.0")               |
| error    | Object           | Error object with code and message            |
| id       | Number or String | Same ID as in the request                     |

Error object structure:

| Field    | Type             | Description                                   |
|----------|------------------|-----------------------------------------------|
| code     | Number           | Error code (standard or custom)               |
| message  | String           | Short error description                       |
| data     | Any (optional)   | Additional error information                  |

## HTTP REST Format Details

REST endpoints follow standard HTTP conventions:

- **GET** requests are used for reading data
- **POST** requests are used for submitting data or executing actions
- URL paths follow a resource/action pattern
- Response status codes follow HTTP standards (200 OK, 400 Bad Request, etc.)

## Protocol Conversion

ChainFree handles protocol conversion internally, but it's important to use the correct protocol for each blockchain network. For example, you should use JSON-RPC for Ethereum and HTTP REST for Solana.

## Authentication Across Protocols

Authentication is consistent across both protocols:

1. **URL Path**: Include your API token in the URL path:
   ```
   https://api.chainfree.io/<CHAIN>/<API_TOKEN>/...
   ```

2. **Authorization Header**:
   ```
   Authorization: Bearer <API_TOKEN>
   ```

## Protocol-Specific Best Practices

### JSON-RPC Best Practices

- Use batch requests when making multiple related calls to reduce network overhead
- Include meaningful IDs in your requests to help with debugging
- Handle error responses appropriately based on error codes

Example of a batch request:
```json
[
  {
    "jsonrpc": "2.0",
    "method": "eth_blockNumber",
    "params": [],
    "id": 1
  },
  {
    "jsonrpc": "2.0",
    "method": "eth_getBalance",
    "params": ["0x742d35Cc6634C0532925a3b844Bc454e4438f44e", "latest"],
    "id": 2
  }
]
```

### HTTP REST Best Practices

- Use appropriate HTTP methods for different operations
- Check HTTP status codes to determine if a request was successful
- Include query parameters correctly according to the endpoint documentation

## Chain-Specific Protocol Notes

Some chains have specific protocol behaviors to be aware of:

- **Solana**: Uses both REST endpoints and RPC-style POST requests
- **TRON**: Uses a REST API with some RPC-like POST endpoints
- **Cosmos**: Uses REST for most operations but has some gRPC endpoints

## Protocol Comparison

| Feature               | JSON-RPC                   | HTTP REST                       |
|-----------------------|----------------------------|----------------------------------|
| Transport             | HTTP POST                  | Various HTTP methods (GET, POST) |
| Message Format        | JSON                       | Typically JSON                   |
| Request Identification| ID field                   | None (stateless)                 |
| Error Handling        | Error objects with codes   | HTTP status codes                |
| Batch Requests        | Supported                  | Not standardized                 |
| Typical Use Case      | EVM chains                 | Non-EVM chains                   |

## Further Reading

- [Ethereum JSON-RPC Specification](https://ethereum.github.io/execution-apis/api-documentation/)
- [JSON-RPC 2.0 Specification](https://www.jsonrpc.org/specification)
- [RESTful API Design Principles](https://restfulapi.net/)