# BARK Token TypeScript Implementation

## Overview

This repository contains the modular TypeScript implementation for the BARK Token on the Solana blockchain. The codebase is organized to provide a modular and maintainable structure for different functionalities related to the BARK token.

## File Structure

The project is organized into the `src/` folder and several files to handle specific functionalities:

1. **`main.ts`**: Main functions and logic.
   - This file likely contains the entry point and orchestrates the overall execution of your BARK token functionalities.

2. **`utils/config.ts`**: Contains configuration settings.
   - Configuration settings such as fees, program IDs, and other constants are defined in this file.

3. **`utils/helpers.ts`**: [To be updated]
   - Helpers typically contain utility functions that assist in various tasks throughout your codebase.

4. **`utils/error.ts`**: [To be updated]
   - This file might include custom error classes or functions to handle errors gracefully.

5. **`wallet.ts`**: [To be updated]
   - Wallet-related operations, such as keypair generation or interaction with user wallets, might be implemented in this file.

6. **`keypairs/secretKey.ts`**: [To be updated]
   - This file might handle the generation and management of secret keys.

7. **`utils/solana.ts`**: Handles Solana-related operations.
   - This file contains functions for establishing connections, creating Solana accounts, and handling transactions.

8. **`mint.ts`**: Manages BARK Mint Account creation and initialization.
   - This file likely handles the creation and initialization of the BARK Mint Account.

9. **`token.ts`**: Manages BARK token-related operations.
   - BARK token-related functionalities, such as transfers and balance checks, are implemented in this file.

10. **`fees/transactionFees.ts`**: Handles fee-related operations, including fee account creation and withdrawal.
    - This file manages operations related to fees, including creating fee accounts and withdrawing fees.

11. **`metadata/metadata.ts`**: Manages Token Metadata operations.
    - Token metadata, such as name, symbol, and URI, is handled in this file.

12. **`transactions/transactions.ts`**: Handles various transactions, including BARK transfers and burning.
    - This file likely includes functions for executing different types of transactions involving BARK tokens.

13. **`burning/burn.ts`**: Manages burning functionalities.
    - Burning-related operations, including calculating burn amounts and executing burn transactions, are implemented here.

## Getting Started

Follow these steps to set up and run the BARK Token TypeScript implementation:

### Prerequisites

- Node.js
- Solana CLI
- TypeScript
- Anchor
- Rust

### Installation

1. **Clone the repository:**

   ```bash
   git clone https://github.com/bark-community/bark-token/src.git
   cd bark-token && cd src
   ```

2. **Install dependencies:**

   ```bash
   npm install
   ```

3. **Build the project:**

   ```bash
   npm run build
   ```

### Usage

1. **Initialize Connection**: Establish a connection to the Solana blockchain.

2. **Check Balance**: Verify the SOL balance of the wallet.

3. **Initialize Mint Account**: Create and initialize the BARK Mint Account.

   ```typescript
   // Example usage of initializeMintAccount function
   await initializeMintAccount();
   ```

4. **Initialize Solana Accounts**: Create source and destination token accounts for BARK tokens.

   ```typescript
   // Example usage of initializeSolanaAccounts function
   const [sourceTokenAccount, destinationTokenAccount] = await initializeSolanaAccounts();
   ```

5. **Transfer BARK with Fee**: Transfer BARK tokens from the source account to the destination account with an associated fee.

   To initiate a BARK transfer with an associated fee, use the `transferBarkWithFee` function. This function not only transfers BARK tokens but also charges a fee based on the configured fee structure.

   ```typescript
   // Example usage of transferBarkWithFee function
   await transferBarkWithFee(sourceTokenAccount, destinationTokenAccount, config.MINT_AMOUNT);
   ```

6. **Withdraw Fees**: Withdraw accumulated fees from the destination account.

   To manage accumulated fees associated with token transfers, use the `withdrawFees` function. This function identifies fee accounts linked to the destination account, withdraws accumulated fees, and transfers them back to the BARK Mint Account.

   ```typescript
   // Example usage of withdrawFees function
   await withdrawFees(destinationTokenAccount, [sourceTokenAccount]);
   ```

7. **Transfer BARK Again**: Perform another BARK transfer.

   ```typescript
   // Example usage of transferBarkWithFee function for a second transfer
   await transferBarkWithFee(sourceTokenAccount, destinationTokenAccount, config.MINT_AMOUNT);
   ```

8. **Harvest Fees to Mint**: Harvest accumulated fees and transfer them back to the BARK Mint Account.

   ```typescript
   // Example usage of harvestWithheldTokensToMint function
   await harvestWithheldTokensToMint(mint, existingFeeAccount);
   ```

9. **Withdraw Fees Again**: Withdraw fees from the destination account.

   ```typescript
   // Example usage of withdrawFees function for a second withdrawal
   await withdrawFees(destinationTokenAccount, [], true);
   ```

10. **Burning Mechanism**: Check the current quarter, and if the burning quarter is reached, calculate and burn a percentage of BARK tokens.

- Token Burn Rate: 2% Quarterly
- Burning will start from Quarter 3. Current Quarter: 1

   ```typescript
   // Example usage of burnTokens function
   await burnTokens(burnAccounts[0].pubkey, burnAmount);
   ```

11. **Keypair Generation**: Generate Solana Keypairs for various accounts.

   ```typescript
   // Example usage of keypair generation
   const keypair = generateKeypair();
   ```

12. **Anchor Program Integration ToDo**

   - [ ] Create a new Anchor program file (e.g., `bark-token.ts`).
   - [ ] Define the necessary instructions, state, and accounts for the BARK Token program.
   - [ ] Implement the integration logic with the existing BARK Token program.

13. **Metadata Pointer**: [To be updated]

14. **Features to Update**: [To be updated]

15. **Controlling Tokens**: [To be updated]

### TypeScript Integration ToDo

- [ ] Integrate TypeScript with Anchor program.
- [ ] Update TypeScript functions based on Anchor program integration.
- [ ] Implement TypeScript logic for interacting with the Anchor program.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Solana Developers
- Anchor Protocol Developers
