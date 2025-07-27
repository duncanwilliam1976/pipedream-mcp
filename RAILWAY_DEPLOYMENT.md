# Pipedream MCP Server - Railway Deployment Guide

This guide explains how to deploy the Pipedream MCP server to Railway and integrate it with the hire-chat-bots Lovable application.

## Prerequisites

1. Railway account ([railway.app](https://railway.app))
2. Pipedream account and API credentials
3. Supabase service role key for hire-chat-bots integration
4. Environment variables from your Pipedream setup

## Deployment Steps

### 1. Deploy to Railway

#### Option A: Deploy from GitHub (Recommended)

1. Fork or clone the pipedream-mcp repository
2. In Railway, create a new project
3. Select "Deploy from GitHub repo"
4. Choose your forked repository
5. Select the `/modelcontextprotocol` directory as the root directory

#### Option B: Deploy using Railway CLI

```bash
# Install Railway CLI
npm install -g @railway/cli

# Login to Railway
railway login

# Navigate to the modelcontextprotocol directory
cd pipedream-mcp/modelcontextprotocol

# Initialize Railway project
railway init

# Deploy to Railway
railway up
```

### 2. Configure Environment Variables

In your Railway project settings, add the following environment variables:

```env
# Required Pipedream Configuration
PIPEDREAM_API_KEY=your-pipedream-api-key
PIPEDREAM_ACCOUNT_ID=your-pipedream-account-id

# Required Supabase Configuration
SUPABASE_URL=https://yqqvecymsbiofyiripui.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your-supabase-service-key

# Server Configuration
PORT=3010
NODE_ENV=production

# MCP Configuration
MCP_MODE=server
TENANT_ISOLATION=true

# Optional: Datadog tracing (if needed)
DD_TRACE_ENABLED=false
```

### 3. Configure Railway Service

1. In Railway project settings:
   - Set **Start Command**: Leave empty (uses Dockerfile CMD)
   - Set **Root Directory**: `/modelcontextprotocol`
   - Enable **Private Networking**: Optional but recommended

2. Generate a public domain:
   - Go to Settings → Networking
   - Click "Generate Domain"
   - Your URL will be: `https://pipedream-mcp-production.up.railway.app`

### 4. Verify Deployment

Test the deployment by accessing the health endpoint:

```bash
curl https://pipedream-mcp-production.up.railway.app/health
```

Expected response:
```json
{
  "status": "ok",
  "mode": "server",
  "version": "1.0.0"
}
```

### 5. Register with hire-chat-bots

After successful deployment, register the MCP server with hire-chat-bots:

```bash
# Navigate to hire-chat-bots directory
cd ../hire-chat-bots

# Set environment variable
export SUPABASE_SERVICE_ROLE_KEY=your-supabase-service-key

# Run registration script
node register-pipedream-mcp.js
```

### 6. Verify Integration

1. Log into the hire-chat-bots Lovable app
2. Navigate to MCP Servers or Data Sources
3. Verify that "Pipedream MCP Server" appears in the list
4. Test connection by clicking "Connect"

## Available MCP Tools

Once deployed and registered, the following tools are available:

- **select-apps**: Browse and select from 2000+ available apps
- **begin-configuration**: Start configuring a workflow component
- **configure-component**: Select specific actions or triggers
- **configure-props**: Set up component properties
- **run-action**: Execute configured actions
- **query**: Search for apps, components, or workflows
- **abort-configuration**: Cancel current configuration
- **async-options**: Load dynamic property options

## Troubleshooting

### Common Issues

1. **Deployment fails with build errors**
   - Ensure you're deploying from the `/modelcontextprotocol` directory
   - Check that all dependencies are properly specified in package.json

2. **Server returns 401 Unauthorized**
   - Verify PIPEDREAM_API_KEY is correctly set
   - Check that the API key has appropriate permissions

3. **Cannot connect from hire-chat-bots**
   - Ensure the server URL is publicly accessible
   - Verify TENANT_ISOLATION is set to true
   - Check Railway logs for connection attempts

### Viewing Logs

```bash
# Using Railway CLI
railway logs

# Or view in Railway dashboard
# Project → Deployments → View Logs
```

### Testing MCP Connection

Test the MCP server directly:

```bash
# List available tools
curl -X POST https://pipedream-mcp-production.up.railway.app/mcp \
  -H "Content-Type: application/json" \
  -H "X-Tenant-ID: test-tenant" \
  -d '{"method": "tools/list"}'
```

## Security Considerations

1. **API Keys**: Never commit API keys to version control
2. **Service Role Key**: Keep Supabase service role key secure
3. **Network Security**: Consider using Railway's private networking for internal services
4. **Rate Limiting**: Pipedream API has rate limits - monitor usage

## Monitoring

1. **Railway Metrics**: Monitor CPU, memory, and network usage in Railway dashboard
2. **Pipedream Dashboard**: Track API usage and workflow executions
3. **Application Logs**: Check hire-chat-bots logs for MCP integration issues

## Support

- **Pipedream Documentation**: [pipedream.com/docs](https://pipedream.com/docs)
- **Railway Documentation**: [docs.railway.app](https://docs.railway.app)
- **MCP Protocol**: [modelcontextprotocol.io](https://modelcontextprotocol.io)

## Next Steps

After successful deployment:

1. Create test agents in hire-chat-bots that use Pipedream tools
2. Build example workflows combining Pipedream with other MCP servers
3. Monitor usage and optimize performance
4. Consider implementing caching for frequently used app metadata