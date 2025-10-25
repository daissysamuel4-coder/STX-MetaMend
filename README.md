Absolutely — here’s a **comprehensive, professional README** for your **Dynamic NFT-Backed Loan Smart Contract**, written for clarity, developer onboarding, and project documentation.

---

# 🧠 Dynamic NFT-Backed Loan Smart Contract

## Overview

This Clarity smart contract enables **NFT-collateralized lending** with a unique twist:
the **NFT’s attributes dynamically evolve** based on the **loan’s repayment status**.

Borrowers can list their NFTs as collateral for loans, while lenders can offer funds based on agreed terms. The NFT is held by the contract during the loan term and can change properties such as **rarity**, **power-level**, or **condition** depending on borrower behavior.

This creates a DeFi + NFT hybrid system where reputation and financial responsibility are reflected in the asset itself.

---

## ✨ Features

* **NFT Minting** — Create new dynamic NFTs with initial attributes.
* **NFT Loan Listing** — List NFTs as collateral for potential loans.
* **Loan Offering & Matching** — Lenders can make loan offers that must meet borrower’s listing parameters.
* **Dynamic Attribute Updates** — NFT attributes are designed to evolve (future extension) based on repayment behavior.
* **Secure Custody** — NFTs are transferred to the contract during active loans.
* **Loan Lifecycle Management** — Tracks loan terms, repayments, and defaults.

---

## 📚 Contract Structure

### ### Constants

| Constant         | Description                                                                 |
| ---------------- | --------------------------------------------------------------------------- |
| `CONTRACT_OWNER` | Address that deployed the contract.                                         |
| `ERR_*`          | Error codes for various failure states (authorization, missing data, etc.). |

---

### ### Tokens

```clarity
(define-non-fungible-token dynamic-nft uint)
```

A **non-fungible token** (NFT) representing a dynamic asset whose attributes evolve over time or based on loan status.

---

### ### Data Maps

| Map                | Key            | Value                                                                                                                 | Purpose                                         |
| ------------------ | -------------- | --------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| `token-attributes` | `{ token-id }` | `{ rarity, power-level, condition, last-updated }`                                                                    | Tracks NFT metadata and mutable traits.         |
| `loan-details`     | `{ loan-id }`  | `{ borrower, lender, token-id, amount, interest-rate, duration, start-block, status, missed-payments, total-repaid }` | Stores all loan-related information.            |
| `token-loans`      | `{ token-id }` | `{ loan-id }`                                                                                                         | Links NFTs to active loans.                     |
| `loan-listings`    | `{ token-id }` | `{ owner, requested-amount, min-duration, max-interest }`                                                             | Represents NFTs currently listed for borrowing. |

---

### ### Variables

| Variable        | Description                                              |
| --------------- | -------------------------------------------------------- |
| `next-token-id` | Auto-incrementing counter for minting new NFTs.          |
| `next-loan-id`  | Auto-incrementing counter for creating new loan records. |

---

## 🧩 Functions

### 🔍 Read-Only Functions

| Function                         | Description                                       |
| -------------------------------- | ------------------------------------------------- |
| `get-token-attributes(token-id)` | Returns the current attributes of a specific NFT. |
| `get-loan-details(loan-id)`      | Returns all data about a loan.                    |
| `get-token-loan(token-id)`       | Retrieves the loan associated with a given NFT.   |
| `get-loan-listing(token-id)`     | Gets current listing details for a given NFT.     |

---

### 🪙 Public Functions

#### 1. **Mint a New NFT**

```clarity
(define-public (mint-nft (recipient principal)))
```

**Description:**
Mints a new dynamic NFT with initial default attributes (`rarity`, `power-level`, and `condition` all set to 100).

**Returns:** `ok(token-id)` on success.
**Errors:** None (except internal minting failures).

---

#### 2. **List NFT for Loan**

```clarity
(define-public (list-nft-for-loan (token-id uint) (requested-amount uint) (min-duration uint) (max-interest uint)))
```

**Description:**
Allows the NFT owner to list their token as collateral for a loan, specifying the desired loan parameters.

**Conditions:**

* Caller must be NFT owner.
* NFT must not already be listed.

**Returns:** `ok(true)` on success.
**Errors:**

* `ERR_NOT_AUTHORIZED` — Not the NFT owner.
* `ERR_ALREADY_LISTED` — NFT already listed.
* `ERR_NFT_NOT_FOUND` — NFT does not exist.

---

#### 3. **Offer Loan**

```clarity
(define-public (offer-loan (token-id uint) (amount uint) (interest-rate uint) (duration uint)))
```

**Description:**
Allows a lender to make a loan offer that satisfies the borrower’s listing conditions. Upon success:

* STX are transferred to the borrower.
* NFT collateral is transferred to the contract.
* Loan details are recorded.

**Conditions:**

* Offer must meet or exceed borrower’s requested parameters.
* Loan listing must exist.

**Returns:** `ok(loan-id)` on success.
**Errors:**

* `ERR_NOT_LISTED` — No listing found for the NFT.
* `ERR_INSUFFICIENT_VALUE` — Offer doesn’t meet borrower’s requirements.

---

### 🧮 Private Helper Functions

| Function                  | Purpose                                                                              |
| ------------------------- | ------------------------------------------------------------------------------------ |
| `calculate-payment(loan)` | Computes per-block payment amount based on loan amount, interest rate, and duration. |
| `min-uint(a, b)`          | Returns the smaller of two uints.                                                    |
| `max-uint(a, b)`          | Returns the larger of two uints.                                                     |

---

## ⚙️ Workflow

### 1. **Mint NFT**

User calls `mint-nft(recipient)` to create a new NFT with baseline attributes.

### 2. **List for Loan**

NFT owner calls `list-nft-for-loan(...)` specifying:

* Requested loan amount
* Minimum duration
* Maximum acceptable interest rate

### 3. **Offer Loan**

Lender calls `offer-loan(...)` with terms matching or exceeding borrower’s criteria:

* STX are sent to borrower
* NFT is escrowed in the contract
* Loan record is created

### 4. **Loan Lifecycle**

The contract records:

* Repayments
* Missed payments
* Defaulted status
* NFT attribute updates (to be implemented in extended version)

---

## 🧠 Dynamic NFT Logic (Conceptual Extension)

In a full version of this protocol, the NFT’s **attributes** (`rarity`, `power-level`, `condition`) would dynamically adjust:

* **Timely repayment:** Increases `rarity` and `power-level`.
* **Missed payments:** Reduces `condition` or other attributes.
* **Default:** NFT could lose certain traits or become “damaged.”

These behaviors are tracked via `missed-payments`, `total-repaid`, and `status` fields in `loan-details`.

---

## 🚀 Future Enhancements

* [ ] Add **repayment function** to allow periodic STX payments.
* [ ] Implement **loan default logic** (automatic NFT forfeiture).
* [ ] Add **dynamic attribute adjustments** on repayment events.
* [ ] Enable **NFT recovery or burn** upon default.
* [ ] Integrate with NFT marketplace for trading post-loan.

---

## 🧱 Technical Details

* **Language:** [Clarity](https://docs.stacks.co/docs/write-smart-contracts/clarity)
* **Blockchain:** [Stacks](https://stacks.co)
* **Token Standard:** SIP-009 Non-Fungible Token standard
* **Data Storage:** On-chain maps and variables
* **Error Handling:** `err` tuples with human-readable numeric codes

---

## 🧪 Example Flow

1. **Alice** mints NFT #1.
2. **Alice** lists NFT #1 requesting **1,000 STX**, minimum **500 blocks**, max **10% interest**.
3. **Bob** offers a loan of **1,000 STX**, **5% interest**, **600 blocks**.
4. Contract validates and approves.
5. **Bob’s 1,000 STX** are sent to Alice.
6. **NFT #1** is held in escrow by the contract.
7. **Loan #1** is recorded as active.

---

## 📜 License

MIT License — feel free to use, modify, and extend this contract with attribution.

---
