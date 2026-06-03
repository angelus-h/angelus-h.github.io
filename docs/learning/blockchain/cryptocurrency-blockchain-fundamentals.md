# Cryptocurrency and Blockchain Fundamentals - Learning Plan

**Version:** 1.0  
**Last Updated:** 2026-06-03  
**Target Audience:** Tech professionals entering crypto/blockchain domain (engineers, SREs, infrastructure)  
**Prerequisites:** General tech background, no prior finance or crypto knowledge required  
**Estimated Time:** 6-8 weeks for domain literacy, 12-16 weeks for deeper technical understanding

---

## Overview

This learning plan teaches cryptocurrency and blockchain technology fundamentals from a tech-oriented perspective. The goal is to enable you to:
- Understand blockchain fundamentals (Bitcoin, Ethereum)
- Grasp crypto exchange architecture (how platforms like Kraken operate)
- Comprehend smart contract and DeFi concepts
- Navigate Web3 infrastructure and tooling
- Address security and custody topics

**Why Relevant:**
- Crypto/Web3 infrastructure roles (e.g., Kraken Senior AI Compute Infrastructure Engineer)
- Blockchain protocol development
- DeFi platform engineering
- Domain knowledge for tech interviews

**Important Disclaimer:** This is NOT investment advice. The goal is technical understanding, not trading strategy.

---

## 10 Modules: From Crypto Fundamentals to Web3 Infrastructure

### Module 1: Bitcoin Basics - The First Blockchain

**Goal:** Understand how Bitcoin works, what blockchain is, and why it's a significant innovation.

**Content:**

1. **What is Bitcoin?**
   - Digital currency concept (peer-to-peer electronic cash)
   - Satoshi Nakamoto whitepaper (2008) - **MANDATORY reading**
   - Problem: Double-spending prevention in decentralized systems
   - **Source:** Bitcoin whitepaper (9 pages, technical but readable)
   - **Link:** https://bitcoin.org/bitcoin.pdf

2. **Blockchain data structure**
   - Blocks (transaction groups)
   - Chain (cryptographic hash linking)
   - Immutability (modification = entire chain recalculation)
   - **Source:** Mastering Bitcoin (Andreas Antonopoulos, Chapter 7)
   - **Link:** https://github.com/bitcoinbook/bitcoinbook

3. **Proof of Work (PoW) consensus**
   - Mining concept (nonce search, SHA-256 hash puzzle)
   - Difficulty adjustment (every 2016 blocks, ~2 weeks)
   - Economic security model (51% attack cost)
   - Energy consumption debate
   - **Source:** Bitcoin whitepaper Section 4 (Proof-of-Work)

4. **UTXO model (Unspent Transaction Output)**
   - Not "account balance" but "unspent outputs"
   - Transactions = inputs (previous UTXOs) + outputs (new UTXOs)
   - Change addresses (change mechanism)
   - **Source:** Mastering Bitcoin, Chapter 6 (Transactions)

**Practical Exercise (5-7 days):**
- Read the Bitcoin whitepaper (9 pages, 1-2 hours)
- Explore Bitcoin block explorer (blockchain.com, blockchair.com)
- Trace a transaction: inputs → outputs → confirmations
- Optional: Run Bitcoin Core node (testnet, not mainnet!)
- Blog post: "Bitcoin UTXO model explained with example"

**Checkpoint:**
- Can you explain what blockchain is and why it's immutable?
- Do you understand Proof of Work consensus (why mining is necessary)?
- Are you familiar with the UTXO model (vs. account-based systems)?

---

### Module 2: Ethereum and Smart Contracts

**Goal:** Understand Ethereum's difference from Bitcoin (programmable blockchain).

**Content:**

1. **Ethereum core concept**
   - "World Computer" vision (decentralized computation platform)
   - Vitalik Buterin whitepaper (2013)
   - Ethereum vs. Bitcoin: Turing-complete scripting
   - **Source:** Ethereum whitepaper
   - **Link:** https://ethereum.org/en/whitepaper/

2. **Ethereum Virtual Machine (EVM)**
   - Bytecode execution environment
   - Gas concept (computation metering, spam prevention)
   - Gas price vs. Gas limit (transaction fee calculation)
   - **Source:** Ethereum Yellow Paper (technical spec, difficult reading)
   - **Easier alternative:** "Mastering Ethereum" (Antonopoulos + Wood)

3. **Smart Contracts basics**
   - Solidity language (JavaScript-like syntax)
   - Contract deployment (bytecode on-chain)
   - Function calls (state-changing vs. view functions)
   - Events and logs (off-chain monitoring)
   - **Source:** Solidity documentation
   - **Link:** https://docs.soliditylang.org/

4. **Account model (vs. Bitcoin UTXO)**
   - Externally Owned Accounts (EOA) - user wallets
   - Contract Accounts - smart contract code
   - State storage (account balance, nonce, contract storage)
   - **Source:** Mastering Ethereum, Chapter 7 (Smart Contracts)

**Practical Exercise (7-10 days):**
- Deploy simple smart contract (testnet: Sepolia or Goerli)
- Write basic Solidity contract (e.g., simple storage, counter)
- Interact with contract via Remix IDE (browser-based Solidity IDE)
- Explore Etherscan (Ethereum block explorer, contract verification)
- Blog post: "First smart contract deployment - learning notes"

**Checkpoint:**
- Do you understand Ethereum's "world computer" concept?
- Are you familiar with the Gas mechanism (why it's needed, how it's calculated)?
- Have you deployed a simple smart contract (testnet)?

---

### Module 3: Cryptocurrency Exchanges - Centralized (CEX) Architecture

**Goal:** Understand how centralized exchanges (Kraken, Coinbase, Binance) operate.

**Content:**

1. **Exchange core functions**
   - Order book (bid/ask matching)
   - Trading engine (high-performance order matching)
   - Wallet custody (hot wallet vs. cold wallet)
   - Fiat on/off ramps (KYC/AML compliance)
   - **Source type:** Industry blog posts (Kraken, Coinbase engineering blogs)
   - **Uncertainty:** Internal architecture is proprietary, public info limited

2. **Hot vs. Cold Wallets**
   - Hot wallet: Online, fast withdrawals (operational liquidity)
   - Cold wallet: Offline, multisig, air-gapped (long-term storage, security)
   - Risk trade-off: Convenience vs. Security
   - **Source:** Kraken Security Practices blog
   - **Link:** https://blog.kraken.com/product/security

3. **Order matching engine**
   - Latency requirements (microseconds matter)
   - Matching algorithms (price-time priority)
   - Market orders vs. Limit orders
   - Order book depth (liquidity indicator)
   - **Source:** Coinbase engineering blog (trading infrastructure)

4. **Regulatory compliance**
   - KYC (Know Your Customer) - identity verification
   - AML (Anti-Money Laundering) - transaction monitoring
   - Jurisdiction-specific regulations (US, EU, Asia differences)
   - **Source:** Regulatory frameworks overview (not deep legal analysis)

**Practical Exercise (5-7 days):**
- Create Kraken account (use your Nexo experience as baseline)
- Explore Kraken API documentation (REST, WebSocket)
- Paper trading (simulate trades, observe order book)
- Compare Kraken vs. Coinbase vs. Binance feature sets
- Blog post: "Centralized crypto exchange architecture overview"

**Checkpoint:**
- Do you understand the hot/cold wallet trade-off?
- Are you familiar with the order matching engine (price-time priority)?
- Can you distinguish CEX vs. DEX (latter covered in Module 5)?

---

### Module 4: Consensus Mechanisms - PoW vs. PoS

**Goal:** Understand different consensus algorithms and their trade-offs.

**Content:**

1. **Proof of Work (PoW) - deeper dive**
   - Hash rate and network security
   - Mining pools (centralization concern)
   - ASIC resistance (Ethereum pre-merge, Monero)
   - Energy consumption critique
   - **Source:** Bitcoin mining overview (Bitcoin Wiki)

2. **Proof of Stake (PoS) - Ethereum Merge**
   - Validators (stake 32 ETH)
   - Slashing (penalty for malicious behavior)
   - Energy efficiency (99.95% reduction vs. PoW)
   - Finality (vs. probabilistic finality in PoW)
   - **Source:** Ethereum Proof-of-Stake documentation
   - **Link:** https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/

3. **Other consensus mechanisms**
   - Delegated Proof of Stake (DPoS) - EOS, Tron
   - Proof of Authority (PoA) - private/consortium chains
   - Byzantine Fault Tolerance (BFT) variants
   - **Source:** Consensus algorithm comparison papers (research-heavy)

4. **Consensus security assumptions**
   - 51% attack (PoW)
   - Nothing-at-stake problem (PoS)
   - Long-range attacks (PoS)
   - Economic security models
   - **Source:** Vitalik Buterin blog posts on PoS security

**Practical Exercise (5-7 days):**
- Compare Bitcoin (PoW) vs. Ethereum (PoS) block explorers
- Calculate mining profitability (Bitcoin mining calculator online)
- Understand Ethereum staking (not doing it, just research)
- Read Vitalik's "A Proof of Stake Design Philosophy"
- Blog post: "PoW vs. PoS trade-offs - technical comparison"

**Checkpoint:**
- Can you explain the PoW vs. PoS security model difference?
- Do you understand the significance of the Ethereum Merge (energy, finality)?
- Are you aware of consensus mechanism trade-offs?

---

### Module 5: Decentralized Finance (DeFi) Basics

**Goal:** Understand DeFi protocols (decentralized exchanges, lending, liquidity pools).

**Content:**

1. **What is DeFi?**
   - Decentralized Finance concept (no intermediaries)
   - Smart contract-based financial primitives
   - Composability ("money legos")
   - Risks: smart contract bugs, oracle failures
   - **Source:** DeFi Pulse (DeFi protocol aggregator)
   - **Link:** https://defipulse.com/

2. **Automated Market Makers (AMM) - Uniswap**
   - Liquidity pools (x * y = k constant product formula)
   - No order book (vs. CEX)
   - Impermanent loss (liquidity provider risk)
   - Slippage (large trades move price)
   - **Source:** Uniswap V2 whitepaper
   - **Link:** https://uniswap.org/whitepaper.pdf

3. **Lending protocols - Aave, Compound**
   - Collateralized lending (over-collateralization required)
   - Interest rates (algorithmically determined by supply/demand)
   - Liquidation (if collateral value drops below threshold)
   - Flash loans (uncollateralized loans within single transaction)
   - **Source:** Aave documentation
   - **Link:** https://docs.aave.com/

4. **Stablecoins**
   - USDC, USDT (fiat-backed, centralized)
   - DAI (crypto-collateralized, decentralized)
   - Algorithmic stablecoins (UST collapse case study)
   - **Source:** Stablecoin comparison analysis (Circle, MakerDAO docs)

**Practical Exercise (7-10 days):**
- Explore Uniswap interface (testnet or mainnet view-only)
- Calculate impermanent loss scenario (spreadsheet exercise)
- Read Uniswap V2 whitepaper (technical, ~20 pages)
- Understand Aave liquidation mechanics (docs + examples)
- Blog post: "DeFi AMM vs. CEX order book - architecture comparison"

**Checkpoint:**
- Do you understand how AMM works (constant product formula)?
- Are you familiar with the impermanent loss concept?
- Can you distinguish fiat-backed vs. crypto-collateralized stablecoins?

---

### Module 6: Web3 and Decentralized Applications (dApps)

**Goal:** Understand Web3 stack (frontend + blockchain backend).

**Content:**

1. **Web3 stack overview**
   - Frontend (React, Vue - same as Web2)
   - Wallet integration (MetaMask, WalletConnect)
   - Smart contract interaction (ethers.js, web3.js libraries)
   - Decentralized storage (IPFS, Arweave)
   - **Source:** Web3 developer roadmap
   - **Link:** https://roadmap.sh/blockchain

2. **Ethereum JSON-RPC API**
   - Node providers (Infura, Alchemy, QuickNode)
   - RPC methods (eth_call, eth_sendTransaction, eth_getBalance)
   - WebSocket subscriptions (real-time events)
   - **Source:** Ethereum JSON-RPC specification
   - **Link:** https://ethereum.org/en/developers/docs/apis/json-rpc/

3. **Wallet integration (MetaMask)**
   - Browser extension wallet
   - Transaction signing (user approval required)
   - Network switching (Mainnet, testnet, L2s)
   - **Source:** MetaMask developer documentation
   - **Link:** https://docs.metamask.io/

4. **IPFS (InterPlanetary File System)**
   - Content-addressed storage (hash-based addressing)
   - Decentralized vs. centralized storage trade-offs
   - NFT metadata storage (why IPFS for images)
   - **Source:** IPFS documentation
   - **Link:** https://docs.ipfs.tech/

**Practical Exercise (7-10 days):**
- Build simple dApp (React + ethers.js + smart contract)
- Connect MetaMask to dApp (testnet)
- Read smart contract state (view function call)
- Send transaction (state-changing function, testnet ETH)
- Upload file to IPFS (via Pinata or web interface)
- Blog post: "First Web3 dApp - wallet integration notes"

**Checkpoint:**
- Have you deployed a simple dApp (frontend + smart contract interaction)?
- Do you understand wallet integration (signing, approval flow)?
- Are you familiar with the IPFS use case (NFT metadata, decentralized storage)?

---

### Module 7: Layer 2 Scaling Solutions

**Goal:** Understand Ethereum scalability problems and L2 solutions.

**Content:**

1. **Ethereum scalability trilemma**
   - Decentralization vs. Security vs. Scalability
   - Current Ethereum limitations (~15 TPS, high gas fees)
   - Vitalik's roadmap (rollup-centric future)
   - **Source:** Vitalik Buterin blog - "The Limits to Blockchain Scalability"
   - **Link:** https://vitalik.eth.limo/

2. **Optimistic Rollups (Optimism, Arbitrum)**
   - Execution off-chain, data on-chain (data availability)
   - Fraud proofs (7-day challenge period)
   - EVM compatibility (easy dApp migration)
   - **Source:** Optimism documentation
   - **Link:** https://community.optimism.io/docs/

3. **Zero-Knowledge Rollups (zkSync, StarkNet)**
   - ZK proofs (validity proofs, instant finality)
   - Higher computational overhead (proof generation)
   - Future scalability (zk-SNARKs, zk-STARKs)
   - **Source:** zkSync documentation
   - **Link:** https://docs.zksync.io/

4. **State channels, Plasma, Sidechains**
   - State channels (Lightning Network for Bitcoin)
   - Plasma (deprecated Ethereum scaling approach)
   - Sidechains (Polygon PoS - not a "true" L2)
   - **Source:** Ethereum L2 comparison
   - **Link:** https://l2beat.com/

**Practical Exercise (5-7 days):**
- Bridge funds to Optimism or Arbitrum (testnet)
- Deploy smart contract on L2 (compare gas costs vs. mainnet)
- Explore L2Beat (L2 comparison, TVL, risk analysis)
- Read Vitalik's "An Incomplete Guide to Rollups"
- Blog post: "Optimistic vs. ZK rollups - trade-offs"

**Checkpoint:**
- Do you understand the scalability trilemma?
- Can you distinguish Optimistic vs. ZK rollups?
- Have you deployed a smart contract to L2 (testnet)?

---

### Module 8: Cryptocurrency Security and Custody

**Goal:** Understand private key management, wallet security, custody solutions.

**Content:**

1. **Private key cryptography basics**
   - ECDSA (Elliptic Curve Digital Signature Algorithm)
   - Public/private key pair (Bitcoin, Ethereum same curve: secp256k1)
   - Seed phrases (BIP-39 mnemonic, 12-24 words)
   - Derivation paths (BIP-44, HD wallets)
   - **Source:** Mastering Bitcoin, Chapter 4 (Keys, Addresses)

2. **Wallet types**
   - Software wallets (MetaMask, Trust Wallet)
   - Hardware wallets (Ledger, Trezor)
   - Paper wallets (cold storage, airgapped)
   - Multisig wallets (Gnosis Safe)
   - **Source:** Wallet security best practices (Bitcoin Wiki)

3. **Custody solutions (Exchange context)**
   - Self-custody (not your keys, not your coins)
   - Custodial services (Coinbase Custody, Fireblocks)
   - Institutional custody (regulatory requirements)
   - **Source:** Kraken custody blog posts

4. **Common attack vectors**
   - Phishing (fake websites, social engineering)
   - Clipboard malware (address replacement)
   - SIM swapping (2FA bypass)
   - Smart contract exploits (reentrancy, flash loan attacks)
   - **Source:** Rekt News (DeFi exploit database)
   - **Link:** https://rekt.news/

**Practical Exercise (5-7 days):**
- Generate Bitcoin/Ethereum address offline (Python script or CLI tool)
- Restore wallet from seed phrase (testnet, not mainnet!)
- Research hardware wallet setup (don't buy yet, just understand workflow)
- Read Rekt News top 5 exploits (case studies)
- Blog post: "Crypto security fundamentals - key takeaways"

**Checkpoint:**
- Do you understand private key → public key → address derivation?
- Are you aware of hot/cold/multisig wallet trade-offs?
- Can you generate an address from seed phrase (practical, but testnet!)?

---

### Module 9: Blockchain Infrastructure and Node Operation

**Goal:** Understand blockchain node operation, infrastructure requirements (SRE/infra perspective).

**Content:**

1. **Full node vs. Light client**
   - Full node (complete blockchain, validation)
   - Light client (SPV - Simplified Payment Verification)
   - Archive node (complete historical state)
   - **Source:** Bitcoin Core documentation
   - **Link:** https://bitcoin.org/en/full-node

2. **Ethereum node clients**
   - Execution clients (Geth, Nethermind, Besu)
   - Consensus clients (Prysm, Lighthouse, Teku)
   - Post-merge architecture (execution + consensus)
   - **Source:** Ethereum node setup guide
   - **Link:** https://ethereum.org/en/developers/docs/nodes-and-clients/

3. **Node infrastructure requirements**
   - Storage (Bitcoin ~500GB, Ethereum ~1TB+ for archive node)
   - Network (P2P gossip, peer discovery)
   - Sync time (initial sync days/weeks)
   - Hardware recommendations (SSD required, RAM)
   - **Source:** Geth hardware requirements documentation

4. **RPC node providers (Infura, Alchemy)**
   - Managed node infrastructure
   - API rate limits, pricing
   - Geographic distribution (latency considerations)
   - **Source:** Infura, Alchemy product documentation

**Practical Exercise (7-10 days):**
- Run Bitcoin Core (testnet) - sync, observe logs
- Run Geth (Ethereum testnet) - execution client
- Query local node via JSON-RPC (curl or Postman)
- Monitor node metrics (disk usage, peer count, sync status)
- Blog post: "Running Ethereum node - infrastructure notes"

**Checkpoint:**
- Have you run a Bitcoin or Ethereum node (testnet)?
- Do you understand the full node vs. light client trade-off?
- Are you aware of storage/network requirements?

---

### Module 10: Advanced Topics and Industry Trends

**Goal:** Emerging topics, industry direction, career-relevant trends.

**Content:**

1. **Maximal Extractable Value (MEV)**
   - Front-running, sandwich attacks
   - Flashbots (MEV mitigation)
   - Block builder separation (PBS - Proposer-Builder Separation)
   - **Source:** Flashbots documentation
   - **Link:** https://docs.flashbots.net/

2. **Cross-chain bridges**
   - Wrapped tokens (WBTC, wrapped ETH on other chains)
   - Bridge security risks (Ronin, Wormhole exploits)
   - Interoperability protocols (Cosmos IBC, Polkadot)
   - **Source:** Bridge exploit case studies (Rekt News)

3. **Ethereum roadmap (The Surge, The Scourge, etc.)**
   - EIP-4844 (Proto-Danksharding, blob transactions)
   - Account abstraction (ERC-4337)
   - Verkle trees (state tree optimization)
   - **Source:** Ethereum roadmap
   - **Link:** https://ethereum.org/en/roadmap/

4. **Industry career paths**
   - Smart contract developer (Solidity, Rust for Solana)
   - Protocol engineer (core blockchain development)
   - DeFi engineer (protocol design, security)
   - Infrastructure/DevOps (node operation, indexers, RPCs)
   - **Source:** Crypto job boards (Crypto Jobs List, Web3 Careers)

**Practical Exercise (5-7 days):**
- Read MEV explanation (Flashbots docs)
- Explore bridge transaction on block explorer (e.g., Ethereum → Polygon)
- Read Ethereum roadmap overview (Vitalik's updates)
- Browse crypto job postings (identify skill requirements)
- Blog post: "Crypto industry trends - 2026 landscape"

**Checkpoint:**
- Do you understand the MEV problem (and Flashbots solution)?
- Are you aware of cross-chain bridge risks?
- Can you identify main themes in Ethereum roadmap (scalability, UX, security)?

---

## 10 Practices: Practical Skill Development

### Practice 1: Daily Crypto News Reading
- **What:** Daily 15 min crypto news (CoinDesk, The Block, Decrypt)
- **Why:** Industry awareness, trend spotting
- **Time:** 15 min/day
- **Platform:** Twitter (crypto accounts), Reddit (r/CryptoCurrency, r/ethereum)

### Practice 2: Weekly On-Chain Analysis
- **What:** Weekly explore an interesting transaction (Etherscan, Blockchain.com)
- **Why:** On-chain data reading practice
- **Time:** 30 min/week
- **Tool:** Etherscan, Dune Analytics

### Practice 3: Smart Contract Reading
- **What:** Read 1-2 smart contract source codes weekly (verified contracts on Etherscan)
- **Why:** Solidity pattern recognition
- **Time:** 1-2 hours/week
- **Platform:** Etherscan verified contracts, OpenZeppelin library

### Practice 4: DeFi Protocol Monitoring
- **What:** Follow 2-3 DeFi protocols (TVL, yield changes, governance votes)
- **Why:** Understand protocol dynamics
- **Time:** 30 min/week
- **Platform:** DeFi Pulse, DefiLlama

### Practice 5: Crypto Twitter Engagement
- **What:** Follow key voices (Vitalik, developers, researchers)
- **Why:** Direct insights, early trend awareness
- **Time:** 15-30 min/day
- **Platform:** Twitter/X (crypto community very active here)

### Practice 6: Weekly Research Paper
- **What:** Read 1 crypto/blockchain research paper weekly
- **Why:** Deep technical understanding, academic perspective
- **Time:** 2-3 hours/week
- **Sources:** arXiv (cs.CR crypto category), Stanford Blockchain Research

### Practice 7: Testnet Experimentation
- **What:** Deploy contracts, test dApps, experiment with L2s (testnet only!)
- **Why:** Hands-on experience, no financial risk
- **Time:** 2-4 hours/week
- **Platform:** Sepolia testnet (Ethereum), Bitcoin testnet

### Practice 8: Security Incident Reviews
- **What:** Analyze 1 major exploit/hack per month (Rekt News, post-mortems)
- **Why:** Learn from failures, security awareness
- **Time:** 1-2 hours/month
- **Platform:** Rekt News, PeckShield alerts

### Practice 9: Crypto Podcast Listening
- **What:** Weekly crypto podcast (Bankless, Epicenter, Unchained)
- **Why:** Interviews with builders, diverse perspectives
- **Time:** 1 hour/week
- **Platform:** Spotify, Apple Podcasts

### Practice 10: Blockchain Explorer Deep Dives
- **What:** Explore a blockchain explorer feature in depth (contract interactions, internal txs, token transfers)
- **Why:** On-chain forensics skill
- **Time:** 1 hour/week
- **Tool:** Etherscan advanced features, Blockchair

---

## Source Hierarchy and Reliability

### Primary Sources (Most Reliable)
1. **Bitcoin whitepaper** (Satoshi Nakamoto, 2008)
2. **Ethereum whitepaper** (Vitalik Buterin, 2013)
3. **Protocol documentation** (Ethereum.org, Bitcoin.org)
4. **Academic research papers** (arXiv, peer-reviewed crypto conferences)

### Professional Analysis (High Reliability)
1. **Mastering Bitcoin / Mastering Ethereum** (Andreas Antonopoulos)
2. **Vitalik Buterin blog** (Ethereum Foundation research)
3. **Protocol engineering blogs** (Flashbots, Uniswap, Aave)
4. **Security firms** (Trail of Bits, OpenZeppelin audits)

### Community Sources (Medium Reliability)
1. **Reddit** (r/CryptoCurrency, r/ethereum - user discussions)
2. **Crypto Twitter** (developers, researchers - verify credentials)
3. **YouTube** (Finematics, Whiteboard Crypto - educational channels)
4. **Medium/Substack** (individual researcher blogs)

### Sources to Verify (Low Reliability)
1. **Marketing materials** (project whitepapers with no code - vaporware risk)
2. **Influencer opinions** (often paid promotions, conflicts of interest)
3. **Price prediction content** (speculation, not technical learning)
4. **Anonymous sources** (verify claims before trusting)

---

## Potential Biases and Uncertainties

### Technological Biases
- **Ethereum-centric:** Content is Ethereum-heavy (largest smart contract platform)
  - **Alternatives:** Solana, Avalanche, Cosmos (different design trade-offs)
  - **Bias:** Ethereum community narratives may influence objectivity

### Investment Biases
- **Disclaimer:** This is NOT investment advice
  - **Crypto volatility:** Prices can drop 50-90%
  - **Regulatory risk:** Government regulations can change (SEC, EU MiCA)
  - **Technology risk:** Smart contract exploits, protocol failures

### Learning Curve Uncertainties
- **6-8 week estimate:** Assumes daily 1-2 hours intensive study
  - **Variance:** Depends on individual background (coding experience, finance knowledge)
  - **Reality check:** Domain literacy 6-8 weeks, deep expertise 6-12 months

### Practical Limitations
- **Testnet practice cost:** Free, but mainnet interaction expensive (gas fees $10-100+)
  - **Alternative:** Paper exercises, simulations (no real transactions)
  - **Trade-off:** Cheaper, but less production-like experience

### Source Freshness
- **Rapid change:** 6-month-old crypto tutorial may be outdated
  - **Mitigation:** Always check documentation publish date
  - **Best practice:** Official docs > blog posts (docs maintained, blogs static)

---

## Next Steps (Using This Learning Plan)

1. **Assess commitment:**
   - Do you have 6-8 weeks for basic level? (domain literacy)
   - Do you have 12-16 weeks for deeper tech understanding? (protocol level)
   - **If NOT:** Consider part-time schedule (2-3 months, daily 30-60 min)

2. **Start with Module 1:**
   - Bitcoin whitepaper is mandatory (9 pages)
   - Don't skip ahead (fundamentals are critical)

3. **Track progress:**
   - Markdown checklist (checkbox after each module)
   - Blog posts = public accountability

4. **Adjust as needed:**
   - If a module is too fast/slow → customize
   - If a tech stack isn't relevant (e.g., Solana) → skip or replace

5. **Build portfolio in parallel:**
   - GitHub repo for every smart contract project
   - Blog posts at every milestone
   - **Goal:** After 3-6 months, "Crypto-literate engineer" profile

---

**Last Updated:** 2026-06-03  
**Version:** 1.0  
**Feedback/Questions:** Comment or update this document!
