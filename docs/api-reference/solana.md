# Solana (SOL) API

This document covers the Solana API endpoints available through ChainFree. Our Solana API provides access to the Solana blockchain and follows the Solana JSON-RPC specification.

## Base URL

All Solana API requests should be sent to:

```
https://api.chainfree.io/sol/YOUR_API_TOKEN
```

## Authentication

You can authenticate with the API in two ways:

1. **URL Path**: Include your API token in the URL path as shown above
2. **Authorization Header**: Use the `Authorization` header with a Bearer token
   ```
   Authorization: Bearer <API_TOKEN>
   ```

## Request Format

The Solana API uses JSON-RPC 2.0 over HTTP POST. Requests should have the following format:

```json
{
  "jsonrpc": "2.0",
  "id": REQUEST_ID,
  "method": "METHOD_NAME",
  "params": [PARAM1, PARAM2, ...]
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

### Cluster Information Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| getVersion | Returns the current solana version | None |
| getHealth | Returns the current health of the node | None |
| getIdentity | Returns the identity pubkey for the current node | None |
| getClusterNodes | Returns information about all the nodes in the cluster | None |
| getEpochInfo | Returns information about the current epoch | [commitment: string] |
| getEpochSchedule | Returns epoch schedule information | None |
| getGenesisHash | Returns the genesis hash | None |
| getSlot | Returns the current slot | [commitment: string] |
| getSlotLeader | Returns the current slot leader | [commitment: string] |
| getBlockHeight | Returns the current block height | [commitment: string] |

### Account Information Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| getAccountInfo | Returns all information associated with the account | account: string, [commitment: string] |
| getBalance | Returns the balance of the account | account: string, [commitment: string] |
| getTokenAccountBalance | Returns the token balance of an SPL Token account | account: string, [commitment: string] |
| getTokenAccountsByDelegate | Returns all SPL Token accounts by approved Delegate | delegate: string, [commitment: string] |
| getTokenAccountsByOwner | Returns all SPL Token accounts by token owner | owner: string, [commitment: string] |
| getTokenSupply | Returns the total supply of an SPL Token | mint: string, [commitment: string] |
| getParsedAccountInfo | Returns parsed account information | account: string, [commitment: string] |
| getParsedTokenAccountsByOwner | Returns parsed token accounts by owner | owner: string, programId: string, [commitment: string] |

### Block and Transaction Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| getBlock | Returns identity and transaction information about a confirmed block | slot: number, [commitment: string] |
| getBlockTime | Returns the estimated production time of a block | slot: number |
| getBlocks | Returns a list of confirmed blocks between start and end slots | startSlot: number, endSlot: number, [commitment: string] |
| getBlockProduction | Returns recent block production information | [identity: string], [range: object] |
| getTransaction | Returns transaction details for a confirmed transaction | signature: string, [commitment: string] |
| getConfirmedTransaction | Returns transaction details for a confirmed transaction | signature: string, [commitment: string] |
| getSignatureStatuses | Returns the statuses of a list of signatures | signatures: string[], [searchTransactionHistory: boolean] |
| getConfirmedSignaturesForAddress2 | Returns confirmed signatures for transactions involving an address | address: string, [limit: number], [before: string], [until: string] |
| getFirstAvailableBlock | Returns the slot of the lowest confirmed block that has not been purged from the ledger | None |

### Program Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| getProgramAccounts | Returns all accounts owned by the provided program | programId: string, [filters: array], [commitment: string] |
| getMinimumBalanceForRentExemption | Returns minimum balance required to make account rent exempt | size: number, [commitment: string] |

### Transaction Submission Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| sendTransaction | Submits a signed transaction | signedTransaction: string, [options: object] |
| simulateTransaction | Simulates sending a transaction | signedTransaction: string, [options: object] |

### Staking Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| getStakeActivation | Returns the activation status of a stake account | stakeAccount: string, [commitment: string], [epoch: number] |
| getStakeMinimumDelegation | Returns the minimum delegation amount | [commitment: string] |
| getVoteAccounts | Returns current vote accounts | [commitment: string] |

### NFT Methods

| Method | Description | Parameters |
| ------ | ----------- | ---------- |
| getTokenLargestAccounts | Returns 20 largest accounts of the specified SPL Token | mint: string, [commitment: string] |
| getMetadata | Returns metadata for an NFT | mint: string, [commitment: string] |

## Examples

### Example 1: Get Account Balance

**Request**:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "getBalance",
  "params": ["83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri"]
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 93687744
    },
    "value": 498179432
  },
  "id": 1
}
```

### Example 2: Get Block Height

**Request**:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "getBlockHeight",
  "params": []
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "result": 93687745,
  "id": 1
}
```

### Example 3: Get Transaction Details

**Request**:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "getTransaction",
  "params": [
    "4eEZfmF6pVFMQTTFhYxUMDnPx6c9wC6PGsnqKgJ7TBm9xN3UqKK7StGEQeP5hY8JLRLgnBVno9vTuGKL5khxmSZA",
    {
      "encoding": "json",
      "commitment": "confirmed"
    }
  ]
}
```

**Response**:
```json
{
  "jsonrpc": "2.0",
  "result": {
    "meta": {
      "err": null,
      "fee": 5000,
      "innerInstructions": [],
      "postBalances": [499999995000, 10000000, 1],
      "preBalances": [500000000000, 0, 1],
      "status": {
        "Ok": null
      }
    },
    "slot": 93687100,
    "transaction": {
      "message": {
        "accountKeys": [
          "83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri",
          "9B5XszUGdMaxCZ7uSQhPzdks5ZQSmWxrmzCSvtJ6Ns6g",
          "11111111111111111111111111111111"
        ],
        "header": {
          "numReadonlySignedAccounts": 0,
          "numReadonlyUnsignedAccounts": 1,
          "numRequiredSignatures": 1
        },
        "instructions": [
          {
            "accounts": [
              0,
              1
            ],
            "data": "3Bxs4Bc3VYuGVB19",
            "programIdIndex": 2
          }
        ],
        "recentBlockhash": "GH5Xzw7HqQUbGpneBzxGBRMHEpBfUcrYM6mZRHRyCLG4"
      },
      "signatures": [
        "4eEZfmF6pVFMQTTFhYxUMDnPx6c9wC6PGsnqKgJ7TBm9xN3UqKK7StGEQeP5hY8JLRLgnBVno9vTuGKL5khxmSZA"
      ]
    }
  },
  "id": 1
}
```

## Commitment Levels

Many methods take an optional `commitment` parameter that specifies how finalized a block should be:

- `processed`: The node will query its most recent block.
- `confirmed`: The node will query the most recent block that has been voted on by supermajority of the cluster.
- `finalized`: The node will query the most recent block that has been finalized by the cluster.

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

1. **Use the appropriate commitment level** for your use case (finalized for maximum security, processed for lowest latency)
2. **Batch related requests** to reduce network overhead
3. **Implement client-side caching** for frequently accessed data
4. **Handle rate limits** gracefully with exponential backoff
5. **Include appropriate filters** when querying program accounts to reduce data transfer and processing time

## Limitations

- Historical data access may be limited based on your subscription plan
- Some methods that require extensive computation may take longer to process
- Very large requests or responses may be rejected