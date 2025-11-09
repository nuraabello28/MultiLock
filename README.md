
# MultiLock - Multi-Signature Wallet (Clarity Smart Contract)

### Overview

This project implements a **Multi-Signature (Multi-Sig) Wallet** in **Clarity**, the smart contract language for the Stacks blockchain.
The wallet requires **M-of-N signatures** from authorized owners to approve and execute transactions securely.
It supports key wallet functions like adding/removing owners, submitting and signing transactions, executing approved transfers, and updating signature thresholds — all governed by multi-signature rules.

---

## 🚀 Features

* **Multi-Signature Enforcement:**
  Requires a configurable number of owner signatures (`M-of-N`) for transaction approval.

* **Transaction Lifecycle Management:**

  * Submit new transactions
  * Sign pending transactions
  * Execute approved transactions
  * Cancel pending ones

* **Dynamic Owner Management:**
  Owners can propose and approve the addition or removal of other wallet owners.

* **Threshold Management:**
  Allows updating the required number of signatures (`M`) — only if no pending transactions exist.

* **Security & Validation:**

  * Prevents duplicate signatures
  * Restricts operations to verified owners
  * Ensures proper owner/threshold ratio
  * Uses structured error codes for debugging and event tracking

---

## 📜 Contract Details

### Key Data Variables

| Variable              | Description                                                   |
| --------------------- | ------------------------------------------------------------- |
| `required-signatures` | Number of signatures (`M`) required to execute a transaction. |
| `total-owners`        | Total number of wallet owners (`N`).                          |
| `tx-nonce`            | Unique transaction counter used to assign transaction IDs.    |
| `tx-expiration`       | Optional expiration block count for pending transactions.     |

---

### Maps

| Map                   | Key                 | Value                                                | Description                                       |
| --------------------- | ------------------- | ---------------------------------------------------- | ------------------------------------------------- |
| `owners`              | `principal`         | `bool`                                               | Tracks if a principal is an owner.                |
| `transactions`        | `uint`              | `{ recipient, amount, status, signatures, signers }` | Stores transaction metadata.                      |
| `transaction-signers` | `{ tx-id, signer }` | `bool`                                               | Tracks whether a signer has signed a transaction. |

---

### Public Functions

| Function                                    | Description                                                    |
| ------------------------------------------- | -------------------------------------------------------------- |
| `initialize(owners-list, threshold)`        | Initializes contract with owners and signature threshold.      |
| `submit-transaction(recipient, amount)`     | Submits a new pending transaction for approval.                |
| `sign-transaction(tx-id)`                   | Signs a pending transaction by an owner.                       |
| `execute-transaction(tx-id)`                | Executes a transaction if required signatures are met.         |
| `cancel-transaction(tx-id)`                 | Cancels a pending transaction.                                 |
| `add-owner(new-owner)`                      | Adds a new wallet owner (requires multi-sig approval).         |
| `remove-owner(owner-to-remove)`             | Removes an existing owner (requires multi-sig approval).       |
| `update-required-signatures(new-threshold)` | Updates signature threshold (only if no pending transactions). |

---

### Read-Only Functions

| Function                    | Description                              |
| --------------------------- | ---------------------------------------- |
| `get-transaction(tx-id)`    | Retrieves transaction details.           |
| `get-required-signatures()` | Returns the current signature threshold. |
| `get-total-owners()`        | Returns total number of owners.          |
| `is-valid-owner(user)`      | Checks if a principal is an owner.       |

---

### Error Codes

| Code  | Description               |
| ----- | ------------------------- |
| `u1`  | Not authorized            |
| `u2`  | Invalid signature         |
| `u3`  | Already signed            |
| `u4`  | Insufficient signatures   |
| `u5`  | Invalid threshold         |
| `u6`  | Transaction not found     |
| `u7`  | Max signers exceeded      |
| `u8`  | List overflow             |
| `u9`  | Transaction not pending   |
| `u10` | Already an owner          |
| `u11` | Not an owner              |
| `u12` | Owner threshold too low   |
| `u13` | Transaction expired       |
| `u14` | Invalid signature count   |
| `u15` | Active transaction exists |

---

## 🧩 Example Workflow

1. **Initialize Wallet**

   ```clarity
   (contract-call? .multi-sig initialize (list tx-sender .owner2 .owner3) u2)
   ```

   → Requires 2 out of 3 owners to sign transactions.

2. **Submit Transaction**

   ```clarity
   (contract-call? .multi-sig submit-transaction .recipient u1000000)
   ```

   → Creates a pending transaction ID (e.g., `tx-id = 0`).

3. **Owners Sign Transaction**

   ```clarity
   (contract-call? .multi-sig sign-transaction u0)
   ```

4. **Execute Transaction**

   ```clarity
   (contract-call? .multi-sig execute-transaction u0)
   ```

   → Transfers tokens after reaching required signatures.

---

## 🔐 Security Model

* Only verified owners can propose, sign, or execute actions.
* Prevents duplicate signing and conflicting threshold updates.
* Enforces on-chain validation — no external signature logic required.
* Uses Clarity’s safety guarantees (no reentrancy, no runtime exceptions).

---

## 🧠 Future Improvements

* Add transaction expiration based on block height.
* Implement event emissions for better front-end tracking.
* Integrate off-chain signature collection.
* Support different asset types (fungible/non-fungible tokens).
* Add optional time-lock functionality for extra security.

---

