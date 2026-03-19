# x402 Payment Standard Implementation

## Project Overview

The x402 payment standard implements the HTTP 402 Payment Required status code to enable internet-native, programmatic payments between clients and servers without accounts or credential management. This repository builds core x402 infrastructure supporting both Agent2Agent (A2A) commerce through CLI interfaces and Agent2Human (A2H) commerce with visual web interfaces.

| Aspect | Value |
|--------|-------|
| Standard | x402 Payment Required |
| Current Version | Specification v2 |
| Primary Use Case | Machine-to-machine and agent-based commerce |
| Settlement Network | Solana (USDC) with EVM compatibility |
| Key Innovation | Zero-fee protocol leveraging HTTP-native status code |
| Maintained By | Coinbase Developer Platform & Open Source Community |

## Architecture

The implementation follows a layered architecture separating core protocol handling from interaction-specific abstractions:

```
┌─────────────────────────────────────────────────────┐
│              Application Layer                      │
├───────────────┬───────────────┬─────────────────────┤
│   CLI Layer   │   Web Layer   │   Other Interfaces  │
│ (A2A Focus)   │ (A2H Focus)   │                     │
├───────────────┴───────────────┴─────────────────────┤
│           Core Infrastructure Layer                 │
│  • HTTP 402 Implementation                          │
│  • Payment Protocol Handlers                        │
│  • Verification & Settlement Mechanisms             │
│  • Facilitator Integration                          │
└─────────────────────────────────────────────────────┘
│                    Network Layer                    │
│  • Solana Blockchain (USDC)                         │
│  • HTTP Transport                                   │
└─────────────────────────────────────────────────────┘
```

### Core Infrastructure
Implements the foundational x402 protocol elements:

| Component | Responsibility | Technology | Interface |
|-----------|----------------|------------|-----------|
| HTTP 402 Middleware | Intercepts requests, enforces payment requirements | TypeScript/Node.js | Express/Koa/Hono middleware |
| Payment Scheme Handler | Processes "exact" scheme payments (EIP-3009 equivalent) | Cryptography libraries | REST API (/verify, /settle) |
| Verification Engine | Validates payment payloads, signatures, and replay protection | Ed25519/Secp256k1 | Internal API |
| Settlement Facilitator | Optional service for blockchain settlement and confirmation | Solana web3.js | gRPC/REST |
| Payment Registry | Manages payment requirements and pricing policies | In-memory/Redis | Configuration API |

### Abstraction Layers

#### CLI Layer (Agent2Agent)
Enables programmatic payment capabilities for autonomous agents through command-line interfaces:

| Feature | Description | Implementation |
|---------|-------------|----------------|
| Payment Discovery | Automatically detects HTTP 402 responses and payment requirements | Interceptor middleware |
| Payload Construction | Builds signed payment payloads per x402 spec v2 | Ed25519 signing |
| Transaction Submission | Handles payment submission and confirmation polling | Solana RPC client |
| Workflow Automation | Chains multiple paid API calls in agent workflows | CLI command sequencing |
| Balance Management | Tracks agent funds and automatic replenishment | Wallet integration |

#### Web Layer (Agent2Human)
Provides human-interfaces for overseeing agent transactions and manual payments:

| Component | Purpose | Technology |
|-----------|---------|------------|
| Payment Dashboard | Real-time transaction monitoring and history | React/Vue + WebSockets |
| Manual Payment UI | Enables humans to pay for agent services | Form-based with wallet connect |
| Transaction Explorer | Detailed view of payment flows and settlement | Blockchain explorer integration |
| Admin Controls | Configure payment requirements and pricing | REST API dashboard |
| Notification System | Alerts for failed/payment-required events | Webhook/WebSocket |

## Installation & Setup

### Prerequisites
- Node.js >= 18.0.0
- Solana CLI tools (for local validator)
- Wallet with USDC on Solana devnet/mainnet-beta
- Git

### Core Installation
```bash
# Clone repository
git clone https://github.com/your-org/x402-implementation.git
cd x402-implementation

# Install dependencies
npm ci

# Setup environment
cp .env.example .env
# Edit .env with your configuration:
# SOLANA_RPC_URL=https://api.devnet.solana.com
# WALLET_PRIVATE_KEY=your_encrypted_private_key
# FACILITATOR_URL=https://facilitator.x402.org
# NETWORK=devnet

# Initialize database (if using persistent storage)
npm run db:migrate

# Start development server
npm run dev
```

### CLI Tools Installation
```bash
# Install CLI globally
npm install -g @x402/cli

# Verify installation
x402 --version
# Expected: x402-cli/2.0.0 (linux-x64) node-v18.0.0

# Configure CLI profile
x402 configure
# Prompts for RPC URL, wallet path, default facilitator
```

## Usage Examples

### Agent2Agent CLI Interaction
```bash
# Discover payment requirement for an API endpoint
x402 discover https://api.example.com/premium-data
# Output: Payment required: 0.001 USDC for access to premium-data endpoint

# Execute paid request with automatic payment handling
x402 fetch https://api.example.com/premium-data --max-price 0.005
# Output: 
# [PAYMENT] Constructing payment payload for 0.001 USDC
# [PAYMENT] Signature: 3a7f...f92b
# [PAYMENT] Submitting to facilitator...
# [SETTLEMENT] Transaction confirmed: 5E...fGh
# [RESPONSE] {"data": "...", "timestamp": "..."}

# Batch process multiple paid endpoints
x402 batch \
  --input endpoints.txt \
  --output results.json \
  --price-limit 0.01 \
  --retry-attempts 3
```

### Agent2Human Web Interface
```javascript
// Initialize x402 web client
import { X402WebClient } from '@x402/web-client';

const client = new X402WebClient({
  facilitator: 'https://facilitator.x402.org',
  network: 'devnet',
  wallet: window.solana // Assuming Phantom or similar
});

// Handle payment-required responses automatically
client.interceptFetch(async (request) => {
  const response = await fetch(request);
  
  if (response.status === 402) {
    const paymentRequired = await response.json();
    try {
      // This triggers wallet UI for payment approval
      const paymentPayload = await client.constructPayment(paymentRequired);
      const settledResponse = await client.submitPayment(paymentPayload);
      return settledResponse;
    } catch (error) {
      // Handle payment rejection or failure
      throw new Error(`Payment failed: ${error.message}`);
    }
  }
  
  return response;
});

// Manual payment initiation for tips or subscriptions
async function tipAgent(agentId, amount) {
  const paymentReq = {
    scheme: 'exact',
    network: 'solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp', // Mainnet-beta
    resource: `https://agents.example.com/${agentid}/tip`,
    max_amount: amount * 1000000, // USDC has 6 decimals
    asset: 'USDC',
    description: `Tip for agent ${agentId}`
  };
  
  try {
    const payload = await client.constructPayment(paymentReq);
    const result = await client.submitPayment(payload);
    console.log(`Tip sent! Transaction: ${result.transactionId}`);
  } catch (error) {
    console.error('Tip failed:', error);
  }
}
```

## API Reference

### Core Infrastructure Interfaces

#### HTTP 402 Middleware
```typescript
interface X402MiddlewareOptions {
  facilitatorUrl: string;
  network: string; // CAIP-2 format: e.g., "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp"
  asset: string; // Token contract address or symbol
  getPrice: (request: Request) => Promise<number>; // Returns amount in base units
  verifyPayment: (payload: PaymentPayload) => Promise<boolean>;
  onPaymentSuccess?: (transactionId: string) => void;
  onPaymentFailure?: (error: PaymentError) => void;
}

declare function x402Middleware(options: X402MiddlewareOptions): 
  (req: Request, res: Response, next: NextFunction) => Promise<void>;
```

#### Payment Payload Structure (x402 v2)
```typescript
interface PaymentPayload {
  scheme: string; // "exact"
  network: string; // CAIP-2 network identifier
  asset: string; // Token identifier
  resource: string; // The URL being paid for
  max_amount: string; // Amount in base units (string to avoid precision loss)
  signature: string; // Ed25519 signature of the payload (excluding signature field)
  // Optional fields for extensions
  payload_id?: string; // For idempotency extension
  valid_after?: number; // Unix timestamp
  valid_before?: number; // Unix timestamp
  metadata?: Record<string, string>;
}
```

#### Facilitator API
```typescript
interface FacilitatorClient {
  /**
   * Verifies a payment payload without submitting to blockchain
   * @returns Verification result with any errors
   */
  verify(payload: PaymentPayload): Promise<VerificationResult>;
  
  /**
   * Submits payment for settlement on blockchain
   * @returns Transaction signature and status
   */
  settle(payload: PaymentPayload): Promise<SettlementResult>;
  
  /**
   * Checks which payment schemes and networks are supported
   */
  getSupported(): Promise<SupportedSchemes>;
}
```

### CLI Command Reference
```
x402 <command> [options]

Commands:
  discover <url>         Discover payment requirements for endpoint
  fetch <url> [options]  Make automated paid request
  batch                  Process multiple endpoints from file
  configure              Set up CLI profile and defaults
  wallet                 Manage wallet addresses and balances
  transaction <txid>     Look up transaction details
  facilitator            Interact with facilitator services
  version                Show version information

Options:
  --max-price <amount>   Maximum price willing to pay (in USDC)
  --network <name>       Solana network (devnet/mainnet-beta)
  --facilitator <url>    Custom facilitator endpoint
  --timeout <ms>         Request timeout (default: 30000)
  --verbose              Enable debug logging
```

## Development Guidelines

### Contributing
1. Fork the repository and create your feature branch
2. Ensure code passes linting: `npm run lint`
3. Write tests for new functionality: `npm run test -- --coverage`
4. Follow commit message convention: `type(scope): description`
5. Submit pull request with clear description of changes

### Testing Strategy
| Test Type | Framework | Coverage Target | Location |
|-----------|-----------|-----------------|----------|
| Unit | Jest | 90% | `__tests__/unit/` |
| Integration | Vitest | 80% | `__tests__/integration/` |
| End-to-End | Playwright | 70% | `__tests__/e2e/` |
| Contract | Pact | 100% | `contracts/` |

Run full test suite:
```bash
npm test
```

### Code Standards
- TypeScript with strict mode enabled
- Prettier formatting with 80-character line limit
- ESLint with Airbnb base + plugin:@typescript-eslint
- Documentation via JSDoc for all public APIs
- No console.log in production code - use logger utility

### Release Process
1. Update version in package.json following semver
2. Generate changelog: `npm run changelog`
3. Create GitHub release with notes
4. CI automatically publishes to npm and Docker registry

## Security Considerations

### Payment Security Best Practices
| Practice | Implementation | Risk Mitigated |
|----------|----------------|----------------|
| Payload Nonce | Use timestamp windows (valid_after/valid_before) | Replay attacks |
| Signature Verification | Ed25519 verification of signed payload | Payment forgery |
| Amount Validation | Check against published price requirements | Overcharging |
| Facilitator Trust | Verify facilitator signatures and certificates | Man-in-the-middle |
| Wallet Security | Hardware wallet integration, encrypted private keys | Key theft |
| Network Validation | Confirm CAIP-2 network identifier matches expected | Chain mismatch attacks |
| Idempotency | Implement payment-identifier extension for safe retries | Duplicate charges |

### Security Configuration
```env
# .env.security - Example security-focused settings
REQUIRE_PAYLOAD_TIMESTAMP=true
MAX_PAYLOAD_AGE_SECONDS=30
VERIFY_FACILITATOR_TLS=true
ALLOWED_NETWORKS=solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp,solana:4uhcVJyU9pJkvQyS88uRDiswHXSCkY3z
MAX_TRANSACTION_AMOUNT=100000000  # 100 USDC in base units
ENABLE_PAYLOAD_IDEMPOTENCY=true
LOG_PAYLOADS=false  # Disable in production to avoid leaking sensitive data
```

### Vulnerability Reporting
Please report security vulnerabilities privately to security@x402-example.org.
Do NOT create public issues for security concerns.

## References

| Source Type | Title/Reference | URL | Relevance | Access Date |
|-------------|-----------------|-----|-----------|-------------|
| Official Documentation | x402 Protocol Specification v2 | https://raw.githubusercontent.com/coinbase/x402/main/specs/x402-specification-v2.md | Definitive technical specification | 2026-03-19 |
| Official Documentation | HTTP 402 Core Concepts | https://docs.x402.org/core-concepts/http-402.md | Transport layer implementation details | 2026-03-19 |
| Reference Implementation | Coinbase x402 GitHub Repository | https://github.com/coinbase/x402 | Canonical implementation and SDKs | 2026-03-19 |
| Technical Article | x402 Whitepaper | https://www.x402.org/x402-whitepaper.pdf | Theoretical foundations and motivation | 2026-03-19 |
| Extension Spec | Payment-Identifier (Idempotency) | https://docs.x402.org/extensions/payment-identifier.md | Critical for reliable agent commerce | 2026-03-19 |
| Ecosystem Directory | x402 Ecosystem | https://www.x402.org/ecosystem | Real-world adoption examples | 2026-03-19 |