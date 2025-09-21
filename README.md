# BitForge Collateral Protocol

## Overview

BitForge is a Bitcoin-collateralized stablecoin protocol built on the **Stacks blockchain**. It enables users to lock BTC in trustless, autonomous vaults and mint **BFUSD**, a USD-pegged stablecoin, while maintaining self-custody of their collateral. The system is designed to be permissionless, decentralized, and secure — bridging Bitcoin's store-of-value strength with the programmability of DeFi.

BitForge is inspired by proven CDP (Collateralized Debt Position) systems but optimized for Bitcoin and Stacks smart contracts, featuring robust oracle infrastructure, dynamic risk parameters, and automated liquidation mechanisms to protect solvency.

---

## Key Features

* **Bitcoin-Collateralized CDPs:** Users can open vaults, deposit BTC, and mint BFUSD against their collateral.
* **Multi-Oracle Price Feeds:** Robust and decentralized BTC price data aggregation.
* **Dynamic Risk Parameters:** Governance-driven collateralization ratios and liquidation thresholds.
* **Automated Liquidation Engine:** Ensures undercollateralized vaults are closed to maintain system health.
* **Minting & Redemption:** Permissionless minting and burning of BFUSD with minimal fees.
* **Stacks-Native:** Fully on-chain Clarity contracts, inheriting Bitcoin security guarantees.

---

## System Overview

The BitForge protocol consists of the following core components:

1. **Vault Management**

   * Users create vaults, deposit collateral, and mint BFUSD.
   * Vaults are individually tracked by `(owner, id)` pairs.
   * Over-collateralization is enforced at minting time.

2. **Oracle System**

   * Authorized price oracles push BTC/USD price updates.
   * Protocol uses latest price data for collateralization checks.

3. **Risk Management**

   * Automated liquidation for vaults falling below `liquidation-threshold`.
   * Configurable parameters via governance functions.

4. **Governance & Parameters**

   * Collateralization ratio, minting limits, and fees can be updated by the contract owner or DAO.

---

## Contract Architecture

The contract is organized into distinct sections for clarity and maintainability:

| Section                        | Purpose                                                                                     |
| ------------------------------ | ------------------------------------------------------------------------------------------- |
| **Trait Definitions**          | Implements SIP-010 token trait compliance for BFUSD minting and tracking.                   |
| **Error Codes & Constants**    | Standardized error handling and security constraints (price caps, max timestamps, etc.).    |
| **Protocol Configuration**     | Global parameters: stablecoin name, symbol, collateralization ratio, liquidation threshold. |
| **Oracle System**              | Registry of approved price oracles and last reported BTC price.                             |
| **Vault System**               | Mapping of vaults with collateral, minted amount, and creation timestamp.                   |
| **Vault Management Functions** | Create vault, mint stablecoin, redeem collateral.                                           |
| **Risk Management Functions**  | Liquidation logic for undercollateralized vaults.                                           |
| **Governance Functions**       | Owner-only parameter updates.                                                               |
| **Read-Only Functions**        | Get latest price, vault details, and total supply.                                          |

---

## Data Flow (High-Level)

```text
User → Create Vault → Deposit BTC (off-chain tx) → Call `create-vault()` → Vault Stored On-Chain

Oracle → Call `update-btc-price()` → Price Feed Updated

User → Call `mint-stablecoin()` → Collateralization Check → Mint BFUSD → Update Total Supply

System Monitor / Liquidator → Call `liquidate-vault()` → Check Collateralization → Burn BFUSD → Delete Vault

User → Call `redeem-stablecoin()` → Burn BFUSD → Unlock Collateral
```

---

## Security Considerations

* **Oracle Whitelisting:** Only authorized oracles can submit BTC price updates.
* **Price & Timestamp Validation:** Hard limits prevent malicious oracle data.
* **Over-Collateralization Enforcement:** Minting is blocked if collateral ratio is too low.
* **Liquidation Protection:** Automatic closure of risky vaults protects protocol solvency.

---

## Governance

The initial version is controlled by the contract deployer (`CONTRACT-OWNER`). Over time, governance can be migrated to a DAO or multi-sig to decentralize parameter management and risk oversight.

---

## Future Improvements

* Integration with **sBTC** or other Bitcoin-pegged assets for smoother collateral flow.
* Support for multiple collateral types.
* Decentralized, aggregated price feed mechanism (medianizer/oracle committee).
* Enhanced liquidation auctions for fairer collateral distribution.

---

## Getting Started (Developers)

### Prerequisites

* [Clarinet](https://github.com/hirosystems/clarinet) for local development and testing.
* Stacks CLI for contract deployment.

### Local Setup

```bash
# Clone repository
git clone https://github.com/your-org/bitforge-protocol.git
cd bitforge-protocol

# Run tests
clarinet test

# Deploy locally
deploy_contract.sh bitforge
```

### Testing

Unit tests should cover:

* Vault creation & minting
* Collateralization ratio enforcement
* Oracle price updates
* Liquidation flow
* Parameter governance
