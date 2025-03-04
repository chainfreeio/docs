# Ethereum (ETH) API

This document covers the Ethereum JSON-RPC API endpoints available through ChainFree. Our Ethereum API follows the standard Ethereum JSON-RPC specification and provides access to the Ethereum blockchain.

## Base URL

All Ethereum API requests should be sent to:

```
https://api.chainfree.io/eth/YOUR_API_TOKEN
```

## Authentication

You can authenticate with the API in two ways:

1. **URL Path**: Include your API token in the URL path as shown above
2. **Authorization Header**: Use the `Authorization` header with a Bearer token
   ```
   Authorization: Bearer <API_TOKEN>
   ```

## Request Format

All Ethereum API requests use the JSON-RPC 2.0 format. Requests should be sent as POST requests with a JSON body containing:

```json
{
  "jsonrpc": "2.0",
  "method": "METHOD_NAME",
  "params": [PARAM1, PARAM2, ...],
  "id": REQUEST_ID
}
```

## Response Format

Responses follow the standard JSON-RPC format:

```json
{
  "jsonrpc": "2.0",
  "id": REQUEST_ID,
  "result": RESULT_DATA
}
```

In case of an error:

```json
{
  "jsonrpc": "2.0",
  "id": REQUEST_ID,
  "error": {
    "code": ERROR_CODE,
    "message": "ERROR_MESSAGE"
  }
}
```

## Available Methods

### Chain State Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| eth_blockNumber | Returns the number of the most recent block | None |
| eth_chainId | Returns the chain ID | None |
| eth_protocolVersion | Returns the Ethereum protocol version | None |
| eth_gasPrice | Returns the current gas price in wei | None |
| eth_getBalance | Returns the balance of the account | address, block |
| eth_getStorageAt | Returns the value from a storage position | address, position, block |
| eth_getTransactionCount | Returns the number of transactions sent from an address | address, block |
| eth_getCode | Returns code at a given address | address, block |
| eth_getBlockTransactionCountByHash | Returns the number of transactions in a block by hash | blockHash |
| eth_getBlockTransactionCountByNumber | Returns the number of transactions in a block by number | blockNumber |
| eth_getUncleCountByBlockHash | Returns the number of uncles in a block by hash | blockHash |
| eth_getUncleCountByBlockNumber | Returns the number of uncles in a block by number | blockNumber |

### Block and Transaction Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| eth_getBlockByHash | Returns information about a block by hash | blockHash, fullTransactions |
| eth_getBlockByNumber | Returns information about a block by number | blockNumber, fullTransactions |
| eth_getTransactionByHash | Returns transaction information by hash | transactionHash |
| eth_getTransactionByBlockHashAndIndex | Returns transaction by block hash and index | blockHash, index |
| eth_getTransactionByBlockNumberAndIndex | Returns transaction by block number and index | blockNumber, index |
| eth_getTransactionReceipt | Returns the receipt of a transaction | transactionHash |
| eth_getUncleByBlockHashAndIndex | Returns uncle block by block hash and index | blockHash, index |
| eth_getUncleByBlockNumberAndIndex | Returns uncle block by block number and index | blockNumber, index |

### Contract Interaction Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| eth_call | Executes a new message call without creating a transaction | transaction, block |
| eth_estimateGas | Estimates the gas needed for a transaction | transaction, block |

### Filter Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| eth_newFilter | Creates a filter to notify when the state changes | filterOptions |
| eth_newBlockFilter | Creates a filter to notify about new blocks | None |
| eth_newPendingTransactionFilter | Creates a filter to notify about pending transactions | None |
| eth_uninstallFilter | Uninstalls a filter | filterId |
| eth_getFilterChanges | Returns filter changes since last poll | filterId |
| eth_getFilterLogs | Returns all logs matching filter with given id | filterId |
| eth_getLogs | Returns logs matching the given filter | filterOptions |

### Transaction Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| eth_sendRawTransaction | Sends a signed transaction | signedTransactionData |
| eth_sendTransaction | Creates and sends a new transaction | transaction |
| eth_sign | Signs a message | address, message |
| eth_signTransaction | Signs a transaction | transaction |
| personal_sign | Signs a message with personal_sign prefix | message, address, password |
| personal_ecRecover | Recovers the address used to sign a message | message, signature |

### Network Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| net_version | Returns the current network ID | None |
| net_listening | Returns true if client is actively listening for network connections | None |
| net_peerCount | Returns number of peers currently connected to the client | None |
| web3_clientVersion | Returns the current client version | None |
| web3_sha3 | Returns Keccak-256 of the given data | data |

## Examples

### Example 1: Get Latest Block Number

**Request**:
```json
{
  "jsonrpc": "2.0",
  "method": "eth_blockNumber",
  "params": [],
  "id": 1
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0xc94fcb"
}
```

### Example 2: Get Account Balance

**Request**:
```json
{
  "jsonrpc": "2.0",
  "method": "eth_getBalance",
  "params": ["0x742d35Cc6634C0532925a3b844Bc454e4438f44e", "latest"],
  "id": 1
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x4563918244f40000"
}
```

### Example 3: Call Smart Contract Function

**Request**:
```json
{
  "jsonrpc": "2.0",
  "method": "eth_call",
  "params": [
    {
      "to": "0x6b175474e89094c44da98b954eedeac495271d0f",
      "data": "0x70a08231000000000000000000000000742d35cc6634c0532925a3b844bc454e4438f44e"
    },
    "latest"
  ],
  "id": 1
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x00000000000000000000000000000000000000000000000152d02c7e14af6800"
}
```

## Error Codes

| Code | Message | Description |
| ---- | ------- | ----------- |
| -32700 | Parse error | Invalid JSON |
| -32600 | Invalid request | The JSON sent is not a valid Request object |
| -32601 | Method not found | The method does not exist / is not available |
| -32602 | Invalid params | Invalid method parameters |
| -32603 | Internal error | Internal JSON-RPC error |
| -32000 to -32099 | Server error | Implementation-defined server errors |

## Best Practices

1. **Use batch requests** when making multiple related calls to reduce network overhead
2. **Include meaningful IDs** in your requests to help with debugging
3. **Implement client-side caching** for frequently accessed data
4. **Handle rate limits** gracefully with exponential backoff
5. **Implement robust error handling** to deal with temporary node issues

## Limitations

- Some methods that would send transactions (eth_sendTransaction) require an unlocked account on the node and may not be supported
- Very large requests or responses may be rejected
- Requests that require extensive computation may take longer to process
- Historical data access may be limited based on your subscription plan