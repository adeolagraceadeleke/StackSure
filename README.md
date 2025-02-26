# StackSure: Decentralized Escrow and Reputation Management

## Overview
StackSure is a Clarity smart contract designed to facilitate decentralized escrow transactions while incorporating a reputation management system. It ensures secure and trustless transactions between parties by allowing users to create, manage, and finalize transactions. Additionally, it maintains a reputation system where users can earn points based on successful transactions.

## Features
- **Decentralized Escrow**: Secure funds in a smart contract until transaction conditions are met.
- **Reputation Management**: Assign reputation points to users based on completed transactions.
- **Immutable Transaction Records**: Every transaction is stored on the blockchain for transparency and auditability.
- **Access Control**: Ensures only authorized users can modify transaction states.

## Constants
- `CONTRACT-OWNER`: The contract deployer.
- `ERR-UNAUTHORIZED (u1)`: Unauthorized action.
- `ERR-INSUFFICIENT-FUNDS (u2)`: Invalid or insufficient funds.
- `ERR-INVALID-RECIPIENT (u3)`: The recipient cannot be the sender or contract owner.
- `ERR-TRANSACTION-NOT-FOUND (u4)`: Transaction ID does not exist.
- `ERR-INVALID-POINTS (u5)`: Invalid reputation points.
- `ERR-INVALID-TRANSACTION-ID (u6)`: Invalid transaction ID.

## Data Structures
### **Transaction Map (`transactions`)**
| Field | Type | Description |
|--------|------|-------------|
| `id` | `uint` | Unique transaction ID |
| `sender` | `principal` | Address of the sender |
| `recipient` | `principal` | Address of the recipient |
| `amount` | `uint` | Amount in STX |
| `status` | `string-ascii 20` | Transaction status (`PENDING`, `COMPLETED`) |
| `created-at` | `uint` | Block height when transaction was created |
| `reputation-points` | `uint` | Reputation points assigned to sender |

### **User Reputation Map (`user-reputation`)**
| Field | Type | Description |
|--------|------|-------------|
| `user` | `principal` | Address of the user |
| `total-points` | `uint` | Total reputation points earned |
| `total-transactions` | `uint` | Number of transactions completed |

### **Global Variables**
- `next-transaction-id (uint)`: Auto-incremented ID for transactions.

## Functions
### **Private Functions**
#### `is-valid-recipient (recipient principal) -> bool`
- Ensures the recipient is neither the sender nor the contract owner.

#### `is-valid-transaction-id (id uint) -> bool`
- Validates that the transaction ID exists.

### **Public Functions**
#### `create-transaction (recipient principal, amount uint) -> (response uint (err u3 | u2))`
- Creates a new escrow transaction.
- **Validations**:
  - Recipient must be different from sender.
  - Amount must be greater than zero.
- **Returns**: Transaction ID if successful.

#### `release-funds (transaction-id uint) -> (response bool (err u1 | u4 | u6))`
- Transfers funds to the recipient upon sender’s approval.
- **Validations**:
  - Only the sender can release funds.
  - Transaction ID must exist.
- **Actions**:
  - Transfers STX to the recipient.
  - Updates transaction status to `COMPLETED`.

#### `add-reputation-points (transaction-id uint, points uint) -> (response bool (err u1 | u4 | u5 | u6))`
- Allows the recipient to reward reputation points to the sender.
- **Validations**:
  - Only the recipient can assign points.
  - Points must be greater than zero.
- **Actions**:
  - Updates sender’s reputation.
  - Updates transaction with reputation points.

### **Read-Only Functions**
#### `get-user-reputation (user principal) -> { total-points: uint, total-transactions: uint }`
- Fetches the reputation details of a user.
- Returns a default `{ total-points: u0, total-transactions: u0 }` if no data is found.

#### `get-transaction-details (transaction-id uint) -> (optional { transaction })`
- Retrieves transaction details by ID.
- Returns `none` if transaction does not exist.

## Usage
### Deploying the Contract
- Deploy the contract on the Stacks blockchain using Clarity smart contract tools.

### Interacting with the Contract
- Use `create-transaction` to initiate an escrow transaction.
- Sender calls `release-funds` to finalize and release payment.
- Recipient calls `add-reputation-points` to reward the sender.
- Fetch user reputation and transaction details using read-only functions.

## Security Considerations
- **Access Controls**: Transactions can only be modified by authorized parties.
- **Immutable Ledger**: All transactions and reputation scores are permanently stored.
- **Reputation Abuse Prevention**: Only recipients can assign reputation points to prevent manipulation.

## License
This project is open-source and distributed under the MIT License.

