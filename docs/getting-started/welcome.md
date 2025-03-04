# Welcome to ChainFree

ChainFree provides high-performance RPC nodes for developers and enterprises, offering reliable access to multiple blockchain networks with 99.99% uptime.

With our infrastructure, you can focus on building your Web3 applications without worrying about setting up and maintaining blockchain nodes.

## Getting Started

Getting started with ChainFree is simple:

1. Sign up for an account at [chainfree.io](https://chainfree.io)
2. Generate your API token
3. Choose your blockchain network
4. Start making API calls

### Quick Example: Ethereum Block Number

```bash
curl -X POST https://api.chainfree.io/eth/YOUR_API_TOKEN \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
```

## Key Features

### Multi-Chain Support

Access all major blockchain networks through a unified API:

- **Ethereum-compatible chains**: ETH, BSC, Polygon, Avalanche, Arbitrum, etc.
- **Cosmos-based chains**: ATOM, SEI
- **Other chains**: Solana, SUI, Kaspa, TRON

### Node Management

- **Automatic health monitoring** and scoring
- **Intelligent load balancing** based on node performance
- **Automatic failover** on node failures
- **Configurable maximum block height lag**
- **Response caching** with Redis

### Authentication & Security

- **JWT-based authentication**
- **API token management** with scope control
- **Multiple account tiers** (FREE, DEV, PRO, GOD)
- **Rate limiting** and usage tracking
- **Request analytics** and logging

### Monitoring & Metrics

- **Real-time usage monitoring**
- **Detailed analytics** on your API usage
- **Error tracking** and notifications
- **Customizable dashboards**

## Architecture Overview

ChainFree acts as an intelligent proxy between your application and blockchain nodes:

```
Your Application <---> ChainFree Proxy <---> Blockchain Nodes
```

The proxy handles:
- Node selection and load balancing
- Authentication and rate limiting
- Caching and optimization
- Error handling and failover
- Monitoring and analytics

## Available Blockchains

ChainFree currently supports the following blockchains:

| Chain | Type | Protocol | Networks |
|-------|------|----------|----------|
| Ethereum (ETH) | L1 | JSON-RPC | Mainnet |
| Binance Smart Chain (BSC) | L1 | JSON-RPC | Mainnet |
| Polygon (MATIC) | L2 | JSON-RPC | Mainnet |
| Avalanche (AVAX) | L1 | JSON-RPC | Mainnet |
| Arbitrum (ARB) | L2 | JSON-RPC | Mainnet |
| Optimism (OP) | L2 | JSON-RPC | Mainnet |
| Base | L2 | JSON-RPC | Mainnet |
| Scroll (SCR) | L2 | JSON-RPC | Mainnet |
| Solana (SOL) | L1 | HTTP | Mainnet |
| Cosmos (ATOM) | L1 | HTTP | Mainnet |
| Sei (SEI) | L1 | HTTP | Mainnet |
| Kaspa | L1 | HTTP | Mainnet |
| TRON (TRX) | L1 | HTTP | Mainnet |
| Sui | L1 | HTTP | Mainnet |

For detailed information on each blockchain, including supported methods and examples, see the [API Reference](/docs/api-reference/overview).

## Use Cases

ChainFree is ideal for a wide range of blockchain applications:

### DeFi Applications
Access on-chain data for trading, lending, and borrowing platforms with high reliability and low latency.

### NFT Platforms
Interact with marketplaces, mint NFTs, and query ownership information across multiple blockchains.

### Web3 Games
Build cross-chain gaming experiences with consistent API access to different networks.

### Data Analytics
Collect and analyze blockchain data for research, reporting, and business intelligence.

### Wallet Services
Provide users with a seamless multi-chain experience without managing separate node connections.

## Authentication Methods

ChainFree offers two methods for authentication:

### 1. URL Path
Include your API token in the URL path:
```
https://api.chainfree.io/<CHAIN>/<API_TOKEN>
```

### 2. Authorization Header
Use the Authorization header with a Bearer token:
```
Authorization: Bearer <API_TOKEN>
```

For detailed information on authentication and API tokens, see the [API Tokens Guide](/docs/guides/api-tokens).

## Rate Limiting

ChainFree implements rate limiting to ensure fair usage of our infrastructure:

- **Per-second rate limits** based on your plan
- **Monthly request limits** for each account
- **Custom rate limits** for individual API tokens

Rate limits are applied per API token and per account. When you exceed your rate limit, the API will return a `429 Too Many Requests` response with information about when you can retry.

For detailed information on rate limits and subscription plans, see the [Plans and Limits](/docs/getting-started/plans-limits) documentation.

## Best Practices

For optimal performance and reliability when using ChainFree:

1. **Implement caching** for frequently accessed data
2. **Use batch requests** when possible to reduce the number of API calls
3. **Handle rate limits** gracefully with exponential backoff
4. **Monitor your usage** regularly through the dashboard
5. **Set up alerts** for approaching limits or unusual patterns
6. **Use appropriate error handling** for different types of errors

For detailed guidance, see the [Best Practices](/docs/best-practices/overview) documentation.

## Next Steps

Now that you understand the basics of ChainFree, you can explore more detailed documentation:

- [Plans and Limits](/docs/getting-started/plans-limits) - Learn about our pricing tiers and request limits
- [Monitoring](/docs/getting-started/monitoring) - Understand how to monitor your API usage
- [API Tokens](/docs/guides/api-tokens) - Create and manage your API access tokens
- [API Reference](/docs/api-reference/overview) - Explore the technical details of our API

## Support

If you have any questions or need assistance, you can:

- Email us at support@chainfree.io
- Check our [FAQ](/docs/faq) for common questions
- Join our [Discord community](https://discord.gg/chainfree) for discussions
- Submit a ticket through the dashboard for technical issues