### Solana Token-2022 CLI

The Solana Token-2022 command-line interface (CLI) tool provides various commands to interact with the Solana blockchain, specifically dealing with Token-2022 features.

1. **Installation and Setup:**

   Ensure that you have the Solana CLI tools installed. If not, you can install them by following the instructions on the [official Solana documentation](https://docs.solana.com/cli/install-solana-cli-tools).

2. **Install Solana Token-2022 CLI:**

   The `spl-token` CLI tool is part of the SPL Token Program. For Token-2022, you'll need to ensure you're using the right version that supports Token-2022 features.

   ```sh
   cargo install spl-token-cli --features token-2022
   ```

3. **Basic Commands:**

   Below are some common commands you can use with the `spl-token-2022` CLI.

   - **Create a new Token:**

     ```sh
     spl-token create-token --program-id <TOKEN_2022_PROGRAM_ID>
     ```

     Replace `<TOKEN_2022_PROGRAM_ID>` with the actual program ID for Token-2022.

   - **Create a new Token Account:**

     ```sh
     spl-token create-account <TOKEN_ADDRESS>
     ```

     Replace `<TOKEN_ADDRESS>` with the address of the token you created.

   - **Mint Tokens:**

     ```sh
     spl-token mint <TOKEN_ADDRESS> <AMOUNT> <RECIPIENT_TOKEN_ACCOUNT>
     ```

     Replace `<TOKEN_ADDRESS>`, `<AMOUNT>`, and `<RECIPIENT_TOKEN_ACCOUNT>` with appropriate values.

   - **Transfer Tokens:**

     ```sh
     spl-token transfer <TOKEN_ADDRESS> <AMOUNT> <RECIPIENT_TOKEN_ACCOUNT>
     ```

     Replace `<TOKEN_ADDRESS>`, `<AMOUNT>`, and `<RECIPIENT_TOKEN_ACCOUNT>` with appropriate values.

4. **Detailed Help:**

   For more detailed help and to see all available commands, you can use the `--help` flag:

   ```sh
   spl-token --help
   ```

   Or for a specific command:

   ```sh
   spl-token create-token --help
   ```

### Example Workflow

Here is an example workflow to create a token, create an account, mint tokens, and transfer them:

1. **Create a Token:**

   ```sh
   spl-token create-token --program-id <TOKEN_2022_PROGRAM_ID>
   ```

   Note down the token address returned by this command.

2. **Create a Token Account:**

   ```sh
   spl-token create-account <TOKEN_ADDRESS>
   ```

   Note down the token account address returned by this command.

3. **Mint Tokens to the Account:**

   ```sh
   spl-token mint <TOKEN_ADDRESS> 100 <TOKEN_ACCOUNT_ADDRESS>
   ```

4. **Transfer Tokens to Another Account:**

   ```sh
   spl-token transfer <TOKEN_ADDRESS> 10 <RECIPIENT_TOKEN_ACCOUNT_ADDRESS>
   ```

Replace the placeholders with actual values from your setup.

### Conclusion

The Solana Token-2022 CLI provides a powerful set of tools for managing SPL tokens on the Solana blockchain. By following the steps above, you should be able to effectively create and manage your tokens. For more advanced usage, refer to the official Solana documentation and the SPL Token Program documentation.



## Wallet Keypairs

## Test Account & Wallet Keypairs

- **Publickey**: test8ZRaDdtuAznnGLGv3JAPy2YVfWqbdkW1zU6PkNH

- **Json**: test8ZRaDdtuAznnGLGv3JAPy2YVfWqbdkW1zU6PkNH.json