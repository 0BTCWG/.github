## 0BTC

**Project Vision:** 0BTC represents a significant advancement in bridging the robustness and liquidity of Bitcoin with the privacy, scalability, and programmability offered by modern zero-knowledge proof systems. It is a Layer 2 (or potentially sovereign) UTXO-based system built using Rust and the high-performance Plonky2 ZK proving system. The core goal is to enable users to securely wrap native Bitcoin (BTC) into a privacy-preserving environment (wBTC) and transact with it, alongside the ability to create and manage custom native assets, all while benefiting from the mathematical certainty of zero-knowledge proofs.

**The Challenge with Bitcoin:** While Bitcoin provides unparalleled security and decentralization as a base layer, its scripting capabilities are limited, making complex applications difficult. Furthermore, its transparent ledger offers pseudonymity rather than true privacy, and scaling transaction throughput directly on L1 faces constraints.

**The 0BTC Solution:** 0BTC tackles these challenges by implementing a shielded UTXO model inspired by systems like Zcash but built with modern ZK-SNARK technology (Plonky2). Key aspects include:

1.  **Zero-Knowledge Proofs:** Every state transition (minting, transferring, burning) is accompanied by a ZK proof generated by the user (or a service they trust). This proof mathematically guarantees that the transaction adheres to the system's rules (e.g., valid signatures, conservation of value, no double-spending) *without revealing sensitive details* like sender, receiver, or exact amounts to the public network.
2.  **Shielded UTXO Model:** Like Bitcoin, funds are held in Unspent Transaction Outputs (UTXOs). However, within 0BTC Wire, the details of these UTXOs (owner, amount, asset type) are cryptographically obscured, providing strong privacy guarantees for users.
3.  **MPC-Secured Bitcoin Bridge:** To enable Wrapped Bitcoin (wBTC), 0BTC Wire utilizes a Multi-Party Computation (MPC) system based on Threshold Ed25519 signatures. A distributed group of trusted operators collectively manages the keys controlling the Bitcoin locked on L1. This avoids a single point of failure for custody, requiring a threshold of operators to cooperate for minting attestations (based on verified BTC deposits) and processing withdrawals (signing BTC transactions for burned wBTC).
4.  **Native Asset Support:** Beyond wBTC, the system allows users (with appropriate authorization, potentially via ZK proofs) to create, mint, and burn their own custom tokens directly within the privacy-preserving L2 environment.

**Core Features & Capabilities:**

*   **Wrapped Bitcoin (wBTC):**
    *   `WrappedAssetMintCircuit`: Securely mints wBTC on L2 upon receiving a valid, threshold-signed attestation from the MPC custodians confirming an L1 BTC deposit.
    *   `WrappedAssetBurnCircuit`: Securely burns wBTC on L2 and generates an authenticated withdrawal request payload, enabling the MPC custodians to release the corresponding BTC on L1.
*   **Private Transfers:**
    *   `TransferCircuit`: Enables users to transfer wBTC or native assets between shielded addresses. The ZK proof ensures validity without revealing transaction details publicly. It utilizes nullifiers to prevent double-spending of input UTXOs.
*   **Native Assets:**
    *   `NativeAssetCreateCircuit`: Allows authorized users to define and register new custom assets on the L2.
    *   `NativeAssetMintCircuit`: Allows the asset creator (or designated minter) to mint new tokens of a previously created native asset.
    *   `NativeAssetBurnCircuit`: Allows owners to provably burn native asset tokens.
*   **Security Foundations:**
    *   **Plonky2:** Leverages the speed and recursion capabilities of the Plonky2 proving system.
    *   **Poseidon Hash:** Uses ZK-friendly hashing with strict domain separation for all cryptographic commitments (UTXOs, nullifiers, messages, Merkle trees).
    *   **EdDSA Signatures:** Employs standard Ed25519 signature verification within circuits to authorize spending.
    *   **Nullifiers:** Robust mechanism preventing the same UTXO from being spent multiple times.
    *   **MPC Security:** Distributed trust model for the critical Bitcoin bridge component.
*   **Key Management:** Includes utilities for generating BIP-39 mnemonics and deriving keys using SLIP-0010 standards for user wallets.

**Architecture & Implementation:**

The project is developed in Rust for performance and memory safety. It features a modular design:

*   **`wire_lib`:** The core library containing circuits, gadgets, core types, and utilities. Designed to be reusable.
*   **`wire` CLI:** A command-line tool for users and developers to interact with the system (keygen, prove, verify, aggregate).
*   **`mpc_operator`:** A dedicated binary for MPC operators to run the necessary services for DKG, threshold signing, and bridge monitoring. Includes secure storage and key rotation logic.
*   **WASM Bindings:** Enables the use of core proving and utility functions directly in web browsers or Node.js environments.
*   **Comprehensive Testing:** Includes unit, integration, fuzzing, performance benchmark, and audit-focused tests.
*   **Extensive Documentation:** Covers installation, usage, APIs, security models, MPC operations, and audit readiness.

**Decentralization Model:**

0BTC employs a hybrid model. User assets and transaction *validation* are highly decentralized, relying on user key control and mathematical proofs. The *Bitcoin bridge custody* uses a distributed trust model via MPC operators. For finality and double-spend prevention in a rollup/sovereign context, it requires a mechanism (potentially a centralized sequencer initially, or integration with L1/decentralized sequencers) to order transactions and maintain the canonical nullifier set.

**Project Status & Readiness:**

The 0BTC Wire core library (`wire_lib`) and associated tooling (`wire` CLI, `mpc_operator`) are functionally complete, having implemented the designed circuits, MPC interactions, and supporting utilities. The codebase is well-structured, extensively documented, and includes a comprehensive test suite. It has undergone significant internal security reviews and hardening, focusing on cryptographic soundness and constraint verification. The project is considered **audit-ready**.

However, **deployment to production handling real value is strongly discouraged until a thorough external security audit** by experts in ZK cryptography and MPC systems has been completed and any critical findings addressed.

**Future Directions:**

Potential future work includes further performance optimization, exploring advanced privacy features, implementing decentralized sequencer designs, integrating hardware security modules (HSMs) for MPC operators, and refining the developer and user experience.

0BTC Wire aims to be a foundational piece for building private, scalable applications that leverage Bitcoin's liquidity and security within a state-of-the-art zero-knowledge framework. We welcome contributions, reviews, and collaboration from the community.
