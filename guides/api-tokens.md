# Generating and Managing API Tokens

API tokens are essential for authenticating requests to the ChainFree API. This guide covers how to create, manage, and secure your API tokens.

## What are API Tokens?

API tokens are unique identifiers that authenticate your requests to the ChainFree API. Each token is associated with your account and inherits the permissions and rate limits of your subscription plan.

Key features of API tokens:
- Secure authentication mechanism
- Can be revoked at any time
- Usage can be tracked individually
- Optional custom rate limits

## Creating an API Token

To create a new API token:

1. **Log in** to your ChainFree account at [dashboard.chainfree.io](https://dashboard.chainfree.io)
2. Navigate to the **API Tokens** section in the sidebar
3. Click the **Create Token** button
4. Fill in the following information:
   - **Name**: A descriptive name to identify the token's purpose (e.g., "Production Backend" or "Testing Environment")
   - **Description** (optional): Additional details about the token's use
   - **Custom Monthly Limit** (optional): Set a specific monthly request limit for this token, lower than your account's total limit
   - **Expiration** (optional): Set an expiration date for the token
5. Click **Create**

Once created, the token will be displayed **only once**. Make sure to copy it and store it securely, as you won't be able to view the full token again.

Example token format:
```
chf_1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
```

## Using API Tokens

You can use your API token in two ways:

### 1. In the URL Path

Include the token directly in the URL path:

```
https://api.chainfree.io/<CHAIN>/<API_TOKEN>
```

Example:
```bash
curl -X POST https://api.chainfree.io/eth/chf_1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
```

### 2. In the Authorization Header

Use the `Authorization` header with a Bearer token:

```
Authorization: Bearer <API_TOKEN>
```

Example:
```bash
curl -X POST https://api.chainfree.io/eth \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer chf_1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t" \
  --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
```

## Managing API Tokens

### Viewing Tokens

The API Tokens page in your dashboard displays all your active tokens along with:
- Token name and description
- Creation date
- Last used date
- Current month's usage
- Expiration date (if set)

### Token Analytics

Click on any token to view detailed analytics:
- Usage over time
- Distribution by blockchain network
- Success and error rates
- Average response times

### Revoking Tokens

To revoke a token:
1. Navigate to the API Tokens section
2. Find the token you want to revoke
3. Click the **Revoke** button
4. Confirm the action

Once revoked, a token cannot be reinstated. Any requests using the revoked token will receive a `401 Unauthorized` response.

## Token Security Best Practices

To keep your API tokens secure:

1. **Never share tokens publicly** - Don't commit tokens to public repositories or include them in client-side code.

2. **Use environment variables** - Store tokens in environment variables rather than hardcoding them:
   ```python
   import os
   api_token = os.environ.get("CHAINFREE_API_TOKEN")
   ```

3. **Implement token rotation** - Regularly generate new tokens and phase out old ones.

4. **Use specific tokens for specific purposes** - Create separate tokens for different applications or environments.

5. **Set expiration dates** - Use expiration dates for temporary access or integrations.

6. **Monitor token usage** - Regularly check the usage patterns of your tokens to detect unusual activity.

7. **Revoke unused tokens** - Remove tokens that are no longer needed.

## Rate Limiting

Each API token inherits the rate limits of your subscription plan but can also have custom limits:

- **Monthly Request Limit**: Maximum number of requests allowed per month
- **Rate Limit**: Maximum number of requests per second

When setting custom limits for tokens, you can:
- Limit specific tokens to a portion of your total monthly allowance
- Distribute your monthly allowance across multiple applications
- Reserve capacity for critical systems

## Token Usage Alerts

Set up alerts to be notified when:
- A token reaches a certain percentage of its monthly limit
- A token experiences unusual traffic patterns
- A token encounters a high error rate

To configure alerts:
1. Go to the **Alerts** section in your dashboard
2. Click **Add Alert**
3. Select **Token Usage** as the alert type
4. Configure the alert conditions and notification method
5. Save the alert

## Token Limits by Plan

| Plan       | Max Tokens | Custom Limits | Expiration Setting |
|------------|------------|---------------|-------------------|
| Free       | 2          | No            | No                |
| Developer  | 5          | Yes           | Yes               |
| Professional | 20       | Yes           | Yes               |
| Unlimited  | Unlimited  | Yes           | Yes               |

## Programmatic Token Management

For users who need to manage tokens programmatically, ChainFree provides a Token Management API.

### Creating a Token via API

```bash
curl -X POST https://api.chainfree.io/api/v1/tokens \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "name": "Automated Token",
    "description": "Created via API",
    "expires_in_days": 30
  }'
```

### Listing Tokens via API

```bash
curl -X GET https://api.chainfree.io/api/v1/tokens \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

### Revoking a Token via API

```bash
curl -X DELETE https://api.chainfree.io/api/v1/tokens/{token_id} \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

## Troubleshooting

### Common Issues

**401 Unauthorized**
- The token is invalid or has been revoked
- The token has expired
- The token is being used with incorrect authentication method

**429 Too Many Requests**
- The token has exceeded its rate limit
- The account has reached its monthly request limit

**403 Forbidden**
- The token is trying to access a blockchain network not included in your plan
- The token doesn't have permission for the requested operation

### Resolution Steps

1. Verify the token is valid and active in your dashboard
2. Check if the token or account has reached its usage limits
3. Ensure the token is being passed correctly (URL path or Authorization header)
4. Create a new token if necessary

## FAQ

**Q: How many tokens can I create?**
A: The number of tokens depends on your subscription plan. See the "Token Limits by Plan" section.

**Q: Can I change a token's name or description after creation?**
A: Yes, you can edit a token's name and description at any time, but not the token string itself.

**Q: What happens if a token expires?**
A: Expired tokens are automatically invalidated, and any requests using them will receive a 401 Unauthorized response.

**Q: Can I temporarily disable a token instead of revoking it?**
A: Yes, you can disable a token without revoking it by toggling its status in the dashboard.

**Q: Do tokens count against my account's API limits?**
A: Yes, all requests made with your tokens count toward your account's monthly limit, unless a token has its own custom limit.

**Q: Can I see what IP addresses are using my token?**
A: Yes, the token analytics show the IP addresses making requests with each token.