# X402 Payment Protocol v2 Specification: Authoritative Sources Bibliography

## Overview

This bibliography compiles authoritative sources on the X402 Payment Protocol v2 specification, including official documentation, GitHub repositories, npm packages, and technical guides for Node.js and Express.js implementation. The sources cover protocol fundamentals, Express.js integration, payment schemes, facilitator integration, security considerations, and code examples.

The X402 Payment Protocol is an open standard for internet-native payments developed by Coinbase, enabling clients to pay for external resources using blockchain-based payments. Version 2 of the protocol introduces CAIP-2 network identifiers, restructured message formats, and enhanced extensibility.

## Official Documentation and Specifications

### Core Protocol Specification

**Coinbase. (2025). X402 Protocol Specification v2.**  
https://github.com/coinbase/x402/blob/main/specs/x402-specification-v2.md  

The official specification document for X402 Payment Protocol v2. This comprehensive document defines:
- Protocol fundamentals including PaymentRequired, PaymentPayload, and SettlementResponse schemas
- Facilitator interface with /verify and /settle endpoints
- Payment schemes (currently supporting the "exact" scheme)
- Security considerations including replay attack prevention
- Network identifiers using CAIP-2 format
- Error handling and extension mechanisms

**Key Insights:** Provides the complete technical foundation for implementing X402 v2, detailing the request-response flow, data structures, and security mechanisms. Essential for understanding the protocol before implementation.

### Transport-Specific Documentation

**Coinbase. (2025). X402 HTTP Transport Specification.**  
https://github.com/coinbase/x402/blob/main/specs/transports-v2/http_transport_v2.md  

Defines how X402 v2 is implemented over HTTP transport, including:
- Mapping of protocol concepts to HTTP headers and status codes
- Specific header names (PAYMENT-SIGNATURE, PAYMENT-REQUIRED, PAYMENT-RESPONSE)
- HTTP status code usage (402 Payment Required)
- Content-Type requirements for JSON payloads

**Key Insights:** Essential for understanding how the transport-agnostic protocol is implemented specifically for HTTP/Express.js applications.

### Scheme Specifications

**Coinbase. (2025). Exact Scheme for EVM Chains.**  
https://github.com/coinbase/x402/blob/main/specs/schemes/exact/scheme_exact_evm.md  

Detailed specification of the "exact" payment scheme for EVM-compatible blockchains, covering:
- EIP-3009 (Transfer with Authorization) implementation
- EIP-712 signature requirements
- Authorization parameters (from, to, value, validAfter, validBefore, nonce)
- Verification and settlement procedures

**Coinbase. (2025). Exact Scheme for Solana Chains.**  
https://github.com/coinbase/x402/blob/main/specs/schemes/exact/scheme_exact_svm.md  

Specification of the "exact" payment scheme for Solana blockchain, detailing:
- TransferChecked instruction usage for SPL tokens
- Specific account layout requirements
- Fee payer constraints and compute unit limitations
- Associated Token Account (ATA) verification rules

**Key Insights:** These scheme-specific documents are critical for understanding how payments are actually processed on different blockchain networks.

## Official GitHub Repository

**Coinbase Inc. (2025). x402: Open Standard for Internet Native Payments.**  
GitHub repository: https://github.com/coinbase/x402  

The official source code repository containing:
- Complete implementation of all X402 packages
- Specification documents in the `specs/` directory
- Examples in multiple languages (TypeScript, Python, Go, Java)
- Documentation and contributing guidelines

**Key Insights:** The primary source for all official implementations, providing reference implementations that demonstrate correct usage patterns.

## NPM Packages Documentation

### Core Package

**Coinbase Inc. (2026). @x402/core v2.7.0.**  
npm package: https://www.npmjs.com/package/@x402/core  

The core implementation of the X402 payment protocol providing:
- Transport-agnostic client, server, and facilitator components
- Core data structures and type definitions
- HTTP adapter interfaces
- Verification and settlement logic

**Documentation Highlights:**
- Quick start guides for client and server usage
- Detailed API reference for all core components
- Lifecycle hooks for customization
- Network pattern matching capabilities

**Key Insights:** The foundational package that all other X402 integrations build upon. Understanding this package is essential for any X402 implementation.

### Express Middleware

**Coinbase Inc. (2026). @x402/express v2.7.0.**  
npm package: https://www.npmjs.com/package/@x402/express  

Express.js middleware integration for X402 Payment Protocol, featuring:
- `paymentMiddleware` function for protecting routes
- `x402ResourceServer` class for resource server implementation
- ExpressAdapter implementing HTTPAdapter interface
- Automatic paywall UI integration

**Documentation Highlights:**
- Comprehensive quick start examples
- Detailed configuration options (routes, paywall, facilitator)
- Advanced usage patterns (multiple routes, custom facilitators)
- Migration guide from legacy x402-express package

**Key Insights:** The primary package for integrating X402 payments into Express.js applications, providing a simple middleware approach to payment protection.

### Paywall UI

**Coinbase Inc. (2026). @x402/paywall v2.7.0.**  
npm package: https://www.npmjs.com/package/@x402/paywall  

Modular paywall UI for X402 with EVM and Solana support, offering:
- Pre-built wallet connection UI (MetaMask, Coinbase Wallet, Phantom, etc.)
- USDC balance checking and chain switching
- Tree-shakable imports for EVM-only or Solana-only usage
- Builder pattern for customization
- First-match network selection logic

**Documentation Highlights:**
- Usage examples for EVM-only, Solana-only, and multi-network configurations
- Detailed configuration options (appName, appLogo, testnet)
- Explanation of first-match selection mechanism
- Custom network handler implementation guide

**Key Insights:** Provides the user-facing payment interface that significantly reduces implementation complexity for browser-based applications.

### EVM Implementation

**Coinbase Inc. (2026). @x402/evm v2.7.0.**  
npm package: https://www.npmjs.com/package/@x402/evm  

Ethereum Virtual Machine (EVM) blockchain implementation for X402, including:
- ExactEvmScheme for client, server, and facilitator
- EIP-3009 transfer with authorization implementation
- Wallet signing integration
- Network-specific utilities

**Key Insights:** Essential for implementing X402 payments on EVM-compatible chains like Ethereum, Base, Polygon, etc.

### Solana Implementation

**Coinbase Inc. (2026). @x402/svm v2.7.0.**  
npm package: https://www.npmjs.com/package/@x402/svm  

Solana Virtual Machine (SVM) blockchain implementation for X402, featuring:
- ExactSvmScheme for client, server, and facilitator
- TransferChecked instruction implementation
- Associated Token Account (ATA) handling
- Solana-specific utility functions

**Key Insights:** Required for implementing X402 payments on Solana blockchain.

## Technical Guides and Tutorials

### Express.js Integration Guide

**Coinbase Inc. (2026). Express.js Integration with X402.**  
https://github.com/coinbase/x402/tree/main/typescript/packages/http/express  

Official Express.js integration guide covering:
- Installation and setup instructions
- Basic usage examples with protected routes
- Configuration options for routes, paywall, and facilitator
- Advanced patterns including multiple protected routes
- Custom facilitator client configuration
- Migration instructions from legacy packages

**Key Insights:** Step-by-step guidance for implementing X402 in Express.js applications, from basic setup to advanced configurations.

### Client-Side Implementation Patterns

**Coinbase Inc. (2026). X402 Client Implementation Patterns.**  
https://github.com/coinbase/x402/tree/main/typescript/packages/http  

Client-side implementation patterns for various HTTP libraries:
- Axios interceptor (@x402/axios)
- Fetch wrapper (@x402/fetch)
- Hono middleware (@x402/hono)
- Next.js integration (@x402/next)

**Key Insights:** Demonstrates how X402 can be implemented across different HTTP client and server frameworks beyond Express.js.

### Facilitator Implementation

**Coinbase Inc. (2026). Implementing an X402 Facilitator.**  
https://github.com/coinbase/x402/tree/main/typescript/packages/core/server  

Guide to implementing X402 facilitator services:
- HTTPFacilitatorClient for connecting to external facilitators
- x402Facilitator class for building custom facilitators
- Verification and settlement endpoint implementations
- Supported kinds and signers management
- Lifecycle hooks for customization

**Key Insights:** Essential for understanding how to either use existing facilitator services or implement custom ones.

## Code Examples and Reference Implementations

### TypeScript Examples

**Coinbase Inc. (2026). X402 TypeScript Examples.**  
https://github.com/coinbase/x402/tree/main/typescript/examples  

Collection of runnable examples demonstrating:
- Basic Express.js server with payment protection
- Client-side payment handling with fetch and axios
- Multi-network support configurations
- Custom paywall implementations
- Facilitator service examples

**Key Insights:** Practical, executable code samples that show real-world implementation patterns.

### Express.js Specific Example

**Coinbase Inc. (2026). Express.js Payment Protection Example.**  
https://github.com/coinbase/x402/blob/main/typescript/packages/http/express/examples/basic-server.ts  

A complete, minimal Express.js server demonstrating:
- Setting up an Express application
- Creating an HTTPFacilitatorClient
- Building an x402ResourceServer with ExactEvmScheme
- Applying paymentMiddleware to protect routes
- Implementing a protected endpoint that returns JSON data
- Starting the server on port 3000

**Key Insights:** The definitive example for implementing X402 payments in Express.js, showing all components working together.

### Paywall Integration Example

**Coinbase Inc. (2026). Express.js with Paywall UI Example.**  
https://github.com/coinbase/x402/blob/main/typescript/packages/http/paywall/examples/express-integration.ts  

Example showing Express.js integration with the paywall UI:
- Creating a Paywall instance with builder pattern
- Configuring app name and testnet settings
- Integrating with paymentMiddleware
- Handling both API requests and browser-based paywall display

**Key Insights:** Demonstrates how to provide a complete user experience with wallet connections and payment UI.

## Security Documentation

**Coinbase Inc. (2025). X402 Security Considerations.**  
https://github.com/coinbase/x402/blob/main/specs/x402-specification-v2.md#10-security-considerations  

Detailed security analysis of the X402 protocol covering:
- Replay attack prevention mechanisms (EIP-3009 nonce, time constraints, signature verification)
- Trust minimization principles
- Authentication integration possibilities (SIWE)
- Error handling and failure scenarios
- Specific error codes and their meanings

**Key Insights:** Critical for understanding the security properties of the protocol and implementing it securely.

## Ecosystem and Community Resources

**X402.org. (2026). X402 Ecosystem.**  
https://x402.org/ecosystem  

Listing of projects and tools building with X402, including:
- Client-side integrations
- Services and endpoints
- Ecosystem infrastructure and tooling
- Learning and community resources

**Key Insights:** Shows real-world adoption and provides examples of how others are implementing X402.

**Coinbase Inc. (2025). X402 Contributing Guide.**  
https://github.com/coinbase/x402/blob/main/CONTRIBUTING.md  

Guidelines for contributing to the X402 project, including:
- Development setup instructions
- Coding standards and conventions
- Testing procedures
- Documentation requirements
- Release process

**Key Insights:** Useful for understanding best practices and quality standards for X402 implementations.

## Related Standards and References

**Chain Agnostic Improvement Proposals (CAIP). (2023). CAIP-2: Chain ID.**  
https://chainagnostic.org/CAIPs/caip-2  

The standard for blockchain network identification used in X402 v2:
- Format: `namespace:reference` (e.g., `eip155:8453`)
- EVM networks: `eip155:*`
- Solana networks: `solana:*`
- Guidance for non-blockchain networks

**Key Insights:** Essential for understanding the network identifier format used throughout X402 v2.

**Ethereum Improvement Proposals. (2019). EIP-3009: Transfer with Authorization.**  
https://eips.ethereum.org/EIPS/eip-3009  

The foundational standard for the "exact" scheme on EVM chains:
- EIP-712 typed signed data structure
- TransferWithAuthorization transaction type
- Security considerations and implementation guidelines

**Key Insights:** Critical for understanding the cryptographic foundations of X402 payments on EVM chains.

## Summary of Key Resources

### For Protocol Understanding:
1. [X402 Protocol Specification v2](https://github.com/coinbase/x402/blob/main/specs/x402-specification-v2.md) - Primary specification document
2. [X402 HTTP Transport Specification](https://github.com/coinbase/x402/blob/main/specs/transports-v2/http_transport_v2.md) - HTTP-specific implementation details

### For Express.js Implementation:
1. [@x402/express npm package](https://www.npmjs.com/package/@x402/express) - Core Express.js middleware
2. [Express.js README](https://github.com/coinbase/x402/blob/main/typescript/packages/http/express/README.md) - Usage guide and examples
3. [Basic Express.js Server Example](https://github.com/coinbase/x402/blob/main/typescript/packages/http/express/examples/basic-server.ts) - Complete working example

### For User Interface:
1. [@x402/paywall npm package](https://www.npmjs.com/package/@x402/paywall) - Modular paywall UI
2. [Paywall README](https://github.com/coinbase/x402/blob/main/typescript/packages/http/paywall/README.md) - Configuration and usage guide

### For Blockchain Integration:
1. [@x402/core npm package](https://www.npmjs.com/package/@x402/core) - Protocol foundation
2. [@x402/evm npm package](https://www.npmjs.com/package/@x402/evm) - EVM blockchain implementation
3. [@x402/svm npm package](https://www.npmjs.com/package/@x402/svm) - Solana blockchain implementation
4. [Exact Scheme for EVM](https://github.com/coinbase/x402/blob/main/specs/schemes/exact/scheme_exact_evm.md) - EVM payment details
5. [Exact Scheme for Solana](https://github.com/coinbase/x402/blob/main/specs/schemes/exact/scheme_exact_svm.md) - Solana payment details

### For Facilitator Services:
1. [Facilitator Interface Specification](https://github.com/coinbase/x402/blob/main/specs/x402-specification-v2.md#7-facilitator-interface) - API endpoints
2. [Core Server Implementation](https://github.com/coinbase/x402/tree/main/typescript/packages/core/server) - Facilitator building blocks

### For Security:
1. [Security Considerations Section](https://github.com/coinbase/x402/blob/main/specs/x402-specification-v2.md#10-security-considerations) - Protocol security analysis
2. [Error Handling Documentation](https://github.com/coinbase/x402/blob/main/specs/x402-specification-v2.md#9-error-handling) - Error codes and meanings

## Research Gaps and Areas for Further Investigation

While the X402 ecosystem is well-documented, several areas could benefit from additional resources:

1. **Performance Benchmarks**: Limited public data on transaction throughput and latency characteristics
2. **Production Case Studies**: Few detailed reports from production implementations at scale
3. **Alternative Schemes**: Documentation is focused on the "exact" scheme; other schemes like "upto" are mentioned but not fully specified
4. **Fiat Integration**: While the protocol aims to support fiat networks, specific implementations are not well-documented
5. **Advanced Patterns**: Implementation guidance for sophisticated patterns like subscriptions, batch payments, and dynamic pricing is limited to conceptual descriptions

Developers implementing X402 in production environments may need to supplement these authoritative sources with practical experimentation and community engagement to address these gaps.

## Keywords

X402, Payment Protocol v2, Express.js middleware, @x402/express, @x402/paywall, blockchain payments, HTTP 402 Payment Required, EIP-3009, CAIP-2, Exact Scheme, Facilitator, Resource Server, Client, Paywall UI, EVM, Solana, USDC, Replay Attack Prevention, Payment Schemes, Network Identifiers, Transport Specification, Security Considerations
