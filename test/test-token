/**
 * Solana 2022 Token Script
 *
 * Description: This script provides functions for creating, initializing, and managing a Solana token.
 * It includes features such as minting, transferring with fees, withdrawing fees, burning BARK tokens, and more.
 *
 * Author: BARK Protocol
 * Date: May 19, 2024
 *  BARK Test Token
 * Version: 1.0.4 Beta
 *
 * Libraries:
 * - @solana/web3.js: Solana Web3 library for interacting with the Solana blockchain.
 * - @solana/spl-token: Solana SPL Token library for BARK token-related operations.
 * - @solana/spl-token-metadata: Solana SPL Token Metadata library for managing BARK token metadata.
 */

import {
  Connection,
  Keypair,
  SystemProgram,
  Transaction,
  clusterApiUrl,
  sendAndConfirmTransaction,
  PublicKey,
} from "@solana/web3.js";

import {
  ExtensionType,
  TOKEN_2022_PROGRAM_ID,
  createAccount,
  createInitializeMintInstruction,
  createInitializeTransferFeeConfigInstruction,
  getMintLen,
  createInitializeMetadataPointerInstruction,
  getTransferFeeAmount,
  mintTo,
  transferCheckedWithFee,
  unpackAccount,
  withdrawWithheldTokensFromAccounts,
  getMetadataPointerState,
  getTokenMetadata,
  TYPE_SIZE,
  LENGTH_SIZE,
  transferChecked,
} from "@solana/spl-token";

import {
  createInitializeInstruction,
  createUpdateFieldInstruction,
  createRemoveKeyInstruction,
  pack,
  TokenMetadata,
} from "@solana/spl-token-metadata";

const config = {
  COMMITMENT_LEVEL: "confirmed",
  clusterUrl: clusterApiUrl("devnet"),
  FEE_BASIS_POINTS: 500, // 5%
  MAX_FEE: BigInt(1000), // 10%
  MINT_AMOUNT: 25_000_000_000_000n, // 25 Billion tokens
  DECIMALS: 3,
  MAX_SUPPLY: BigInt("25000000000000"),
  TRANSFER_AMOUNT: BigInt(10_000),
  LAMPORTS_PER_SOL: 1000000000,
  BURN_START_QUARTER: 3,
  BURN_START_YEAR: 2024,
  BURN_RATE: 0.025,
  TOKEN_2022_PROGRAM_ID: new PublicKey("TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb"),
  BARK_ACCOUNT: new PublicKey('BARKanRruQPJbJoBEJATQg5hrmaQ1NNNFdTwdgGgRaBd'),
  FREEZE_AUTHORITY: new PublicKey('BARKanRruQPJbJoBEJATQg5hrmaQ1NNNFdTwdgGgRaBd'),
  BURN_WALLET_ADDRESS: "BURNF5qPfU1A9wSYCB4x4VUwQd398VHqwMHCCsDhp134",
};

const connection = new Connection(config.clusterUrl, config.COMMITMENT_LEVEL);

let payerWallet;
if (pg && pg.wallet && pg.wallet.keypair) {
  payerWallet = pg.wallet.keypair;
} else {
  console.error("Error: Unable to access payer wallet keypair.");
  throw new Error("Unable to access payer wallet keypair.");
}

let mintKeypair;
try {
  mintKeypair = Keypair.generate();
} catch (error) {
  console.error("Error generating keypair for the Mint BARK Account:", error.message);
  throw new Error("Failed to generate keypair for the Mint BARK Account");
}
const mint = mintKeypair.publicKey;

const mintAuthority = pg?.wallet?.publicKey;
const transferFeeConfigAuthority = pg?.wallet?.keypair;
const withdrawWithheldAuthority = pg?.wallet?.keypair;
const burnAuthority = pg?.wallet?.keypair;
const updateAuthority = pg?.wallet?.publicKey;
if (!updateAuthority) {
  throw new Error("Update authority is undefined.");
}

const totalSupply = BigInt("25000000000") * BigInt(10 ** 9);
const decimals = 3;
const scaleFactor = BigInt(10 ** decimals);
const mintAmount = totalSupply * scaleFactor;

async function initializeFeeAccountSpace(connection) {
  try {
    const FEE_ACCOUNT_SPACE = getMintLen([ExtensionType.TransferFeeConfig]);
    const FEE_ACCOUNT_LAMPORTS = await connection.getMinimumBalanceForRentExemption(FEE_ACCOUNT_SPACE);
    return { FEE_ACCOUNT_SPACE, FEE_ACCOUNT_LAMPORTS };
  } catch (error) {
    console.error("Error initializing fee account space:", error.message);
    throw new Error("Failed to initialize fee account space");
  }
}

const { FEE_ACCOUNT_SPACE, FEE_ACCOUNT_LAMPORTS } = await initializeFeeAccountSpace(connection);
console.log("FEE_ACCOUNT_SPACE:", FEE_ACCOUNT_SPACE);
console.log("FEE_ACCOUNT_LAMPORTS:", FEE_ACCOUNT_LAMPORTS);

const mintLen = getMintLen([ExtensionType.TransferFeeConfig]);
const lamports = await connection.getMinimumBalanceForRentExemption(mintLen);

console.log("pg:", pg);
console.log("wallet:", pg?.wallet);
console.log("mintAuthority:", mintAuthority);
console.log("transferFeeConfigAuthority:", transferFeeConfigAuthority);
console.log("withdrawWithheldAuthority:", withdrawWithheldAuthority);
console.log("burnAuthority:", burnAuthority);
console.log("metaData:", metaData);
console.log("updateAuthority:", updateAuthority);

const metaData = {
  updateAuthority: mintAuthority,
  mint: mint,
  name: "BARK",
  symbol: "BARK",
  uri: "https://raw.githubusercontent.com/bark-community/bark-token/main/src/assets/bark.svg",
  extensions: {
    website: "https://barkprotocol.net",
    token_sale_platform: "https://invest.barkprotocol.net",
    socialMedia: {
      bark_dao: "https://app.realms.today/realm/bark/hub",
      twitter: "https://twitter.com/bark_protocol",
      discord: "https://discord.gg/CjUeKEB7b6",
      telegram: "https://t.me/+EnczyzzKS_k2NmQ0",
      medium: "https://medium.com/@barkprotocol",
    },
    additionalMetadata: [
      ["description", "BARK, a real-world asset token (BRWA) on the Solana blockchain, driven by community contributions. It aims to offer transparent, secure, and community-driven financial operations, integrating blockchain's trust and efficiency with real-world asset values."],
      ["traits", [
        { trait_type: "Utility", value: "Financial Operations, Governance" },
        { trait_type: "Blockchain", value: "Solana" },
        { trait_type: "Asset Type", value: "Real-World Asset (RWA)" },
        { trait_type: "Community Driven", value: "Yes" },
      ]],
    ],
  },
};

const initializeMetadataInstruction = await createInitializeInstruction({
  programId: TOKEN_2022_PROGRAM_ID,
  mint: mint,
  updateAuthority: updateAuthority,
  metadata: mint,
  mintAuthority: mintAuthority,
  data: {
    name: metaData.name,
    symbol: metaData.symbol,
    uri: metaData.uri,
    ...metaData.extensions,
    ...metaData.extensions.additionalMetadata.reduce((acc, [key, value]) => {
      acc[key] = value;
      return acc;
    }, {}),
  },
});

function handleError(error, errorMessage) {
  console.error(errorMessage, error.stack || error);
  throw new Error(errorMessage);
}

function logTransactionDetails(message, signature) {
  console.log(`\n${message}: https://solana.fm/tx/${signature}?cluster=devnet`);
}

async function initializeConnection() {
  try {
    const connection = new Connection(config.clusterUrl, config.COMMITMENT_LEVEL);
    return connection;
  } catch (error) {
    handleError(error, "Error initializing Solana connection");
  }
}

async function createFeeAccount(payer) {
  try {
    const newFeeAccountKeypair = Keypair.generate();
    const newFeeAccount = newFeeAccountKeypair.publicKey;
    const createFeeAccountInstruction = SystemProgram.createAccount({
      fromPubkey: payer.publicKey,
      newAccountPubkey: newFeeAccount,
      space: FEE_ACCOUNT_SPACE,
      lamports: FEE_ACCOUNT_LAMPORTS,
      programId: TOKEN_2022_PROGRAM_ID,
    });
    const createFeeAccountTransaction = new Transaction().add(createFeeAccountInstruction);
    await sendAndConfirmTransaction(connection, createFeeAccountTransaction, [payer, newFeeAccountKeypair]);
    console.log(`New fee account created: ${newFeeAccount.toBase58()}`);
    return newFeeAccount;
  } catch (error) {
    handleError(error, "Error creating fee account");
  }
}

async function createSolanaAccountWithSignature(instruction, signers = []) {
  try {
    if (!(instruction instanceof Transaction)) {
      throw new Error("Invalid instruction. Expected a Transaction object.");
    }
    if (!Array.isArray(signers) || signers.length === 0) {
      throw new Error("Invalid signers array. At least one signer is required.");
    }
    console.log("Sending transaction to create Solana account...");
    const signature = await sendAndConfirmTransaction(connection, instruction, signers);
    console.log("Transaction successful. Signature:", signature);
    return signature;
  } catch (error) {
    console.error("Error creating Solana account with signature:", error.message);
    throw new Error("Failed to create Solana account with signature");
  }
}

async function initializeMintAccount() {
  try {
    const transaction = new Transaction()
      .add(
        SystemProgram.createAccount({
          fromPubkey: payerWallet.publicKey,
          newAccountPubkey: mint,
          space: mintLen,
          lamports,
          programId: config.TOKEN_2022_PROGRAM_ID,
        }),
        createInitializeTransferFeeConfigInstruction(
          mint,
          transferFeeConfigAuthority.publicKey,
          withdrawWithheldAuthority.publicKey,
          config.FEE_BASIS_POINTS,
          config.MAX_FEE,
          config.TOKEN_2022_PROGRAM_ID,
        ),
        createInitializeMintInstruction(
          mint,
          config.DECIMALS,
          mintAuthority,
          null,
          config.TOKEN_2022_PROGRAM_ID,
        ),
      );
    const transactionSignature = await createSolanaAccountWithSignature(transaction, [payerWallet, mintKeypair]);
    logTransactionDetails("Create Solana Account", transactionSignature);
  } catch (error) {
    console.error("Error initializing Mint BARK Account:", error.message);
    throw new Error("Failed to initialize Mint BARK Account");
  }
}

async function initializeMintAccountAndTokenMetadata() {
  try {
    await initializeMintAccount();
  } catch (error) {
    console.error("Error initializing Mint Account and BARK Token Metadata:", error.message);
    throw new Error("Failed to initialize Mint Account and BARK Token Metadata");
  }
}

async function initializeSolanaAccounts() {
  try {
    const sourceOwnerKeypair = payerWallet;
    const sourceTokenAccount = await createAccount(
      connection,
      sourceOwnerKeypair,
      mint,
      sourceOwnerKeypair.publicKey,
      undefined,
      undefined,
      TOKEN_2022_PROGRAM_ID,
    );
    const destinationOwnerKeypair = Keypair.generate();
    const destinationTokenAccount = await createAccount(
      connection,
      payerWallet,
      mint,
      destinationOwnerKeypair.publicKey,
      undefined,
      undefined,
      TOKEN_2022_PROGRAM_ID,
    );
    return [sourceTokenAccount, destinationTokenAccount];
  } catch (error) {
    console.error("Error initializing Solana accounts:", error.message);
    throw new Error("Failed to initialize Solana accounts");
  }
}

async function transferBarkWithFee(sourceTokenAccount, destinationTokenAccount, mintAmount) {
  try {
    if (!sourceTokenAccount || !destinationTokenAccount) {
      console.error("Source or destination token account is undefined.");
      return;
    }
    const sourceAccountInfo = await connection.getAccountInfo(sourceTokenAccount);
    const sourceAccountBalance = sourceAccountInfo ? sourceAccountInfo.lamports : 0;
    console.log(`Source account balance before transfer: ${sourceAccountBalance}`);
    if (sourceAccountBalance < config.TRANSFER_AMOUNT) {
      console.error("Insufficient balance in source account for transfer.");
      return;
    }
    console.log("Source account:", sourceTokenAccount.toBase58());
    console.log("Destination account:", destinationTokenAccount.toBase58());
    const mintToSignature = await mintTo(
      connection,
      payerWallet,
      mint,
      sourceTokenAccount,
      mintAuthority,
      mintAmount,
      undefined,
      undefined,
      config.TOKEN_2022_PROGRAM_ID,
    );
    logTransactionDetails("Mint BARK", mintToSignature);
    const fee = (config.TRANSFER_AMOUNT * BigInt(config.FEE_BASIS_POINTS)) / BigInt(10_000);
    const feeCharged = fee > config.MAX_FEE ? Number(config.MAX_FEE) : Number(fee);
    const transferSignature = await transferCheckedWithFee(
      connection,
      payerWallet,
      sourceTokenAccount,
      mint,
      destinationTokenAccount,
      payerWallet.publicKey,
      config.TRANSFER_AMOUNT,
      3,
      feeCharged,
      undefined,
      undefined,
      config.TOKEN_2022_PROGRAM_ID,
    );
    logTransactionDetails("Transfer BARK", transferSignature);
  } catch (error) {
    handleError(error, "Error transferring BARK with fee");
  }
}

async function filterAccountsToWithdraw(destinationTokenAccount, accountsToWithdrawFrom, programId) {
  try {
    const accountInfoPromises = accountsToWithdrawFrom.map(account => connection.getAccountInfo(account, config.COMMITMENT_LEVEL));
    const accountInfos = await Promise.all(accountInfoPromises);
    const filteredAccounts = accountsToWithdrawFrom.filter((account, index) => {
      const accountInfo = accountInfos[index];
      return accountInfo && accountInfo.owner.equals(programId);
    });
    return filteredAccounts;
  } catch (error) {
    console.error("Error filtering accounts to withdraw:", error.message);
    throw new Error("Failed to filter accounts to withdraw");
  }
}

async function withdrawFees(destinationTokenAccount, accountsToWithdrawFrom, isMint = false) {
  try {
    const filteredAccounts = await filterAccountsToWithdraw(
      destinationTokenAccount,
      accountsToWithdrawFrom,
      TOKEN_2022_PROGRAM_ID
    );
    if (filteredAccounts.length > 0) {
      console.log("Accounts selected for withdrawal:", filteredAccounts.map(account => account.toString()));
      const withdrawSignature = await withdrawWithheldTokensFromAccounts(
        connection,
        payerWallet,
        mint,
        destinationTokenAccount,
        withdrawWithheldAuthority,
        undefined,
        filteredAccounts,
        undefined,
        TOKEN_2022_PROGRAM_ID,
      );
      logTransactionDetails(`Withdraw Fee${isMint ? ' from Mint' : ''}`, withdrawSignature);
    } else {
      console.log(`No fees to withdraw${isMint ? ' from Mint' : ''}.`);
    }
  } catch (error) {
    console.error("Error withdrawing fees:", error.message);
    throw new Error("Failed to withdraw fees");
  }
}

async function harvestWithheldTokensToMint(mintAccount, feeAccount) {
  try {
    const feeAccountKey = new PublicKey(feeAccount);
    const feeAccountInfo = await connection.getAccountInfo(feeAccountKey, config.COMMITMENT_LEVEL);
    if (!feeAccountInfo) {
      console.log(`Fee account ${feeAccount} not found. Creating a new fee account...`);
      const newFeeAccount = await createFeeAccount(payerWallet);
      console.log(`Using the newly created fee account: ${newFeeAccount.toBase58()}`);
      return newFeeAccount;
    }
    const feeAccountData = unpackAccount(feeAccountKey, feeAccountInfo.data, config.TOKEN_2022_PROGRAM_ID);
    const withheldAmount = getTransferFeeAmount(feeAccountData)?.withheldAmount || 0;
    if (withheldAmount > 0) {
      const transferSignature = await transferCheckedWithFee(
        connection,
        payerWallet,
        feeAccountKey,
        mintAccount,
        mintAuthority,
        withheldAmount,
        9,
        0,
        undefined,
        undefined,
        config.TOKEN_2022_PROGRAM_ID,
      );
      logTransactionDetails(`Harvested ${withheldAmount} fees to Mint BARK`, transferSignature);
    } else {
      console.log("No withheld fees to harvest to Mint BARK.");
    }
  } catch (error) {
    console.error(`Error harvesting and transferring withheld fees to Mint BARK: ${error.message}`);
    throw new Error(`Failed to harvest and transfer withheld fees to Mint BARK: ${error.message}`);
  }
}

async function checkBalance() {
  try {
    const balance = await connection.getBalance(payerWallet.publicKey);
    console.log(`BARK account balance: ${balance / config.LAMPORTS_PER_SOL} SOL`);
  } catch (error) {
    console.error("Error checking wallet balance:", error.message);
    throw new Error("Failed to check wallet balance");
  }
}

function calculateBurnAmount(amount) {
  return BigInt(Math.floor(Number(amount) * config.BURN_RATE));
}

function getCurrentQuarter() {
  const currentDate = new Date();
  const currentMonth = currentDate.getUTCMonth() + 1;
  return Math.floor((currentMonth - 1) / 3) + 1;
}

async function burnTokens(tokenAccount, burnAmount) {
  try {
    if (burnAmount <= 0n) {
      console.error("Invalid burn amount:", burnAmount);
      return;
    }
    const burnSignature = await transferChecked(
      connection,
      payerWallet,
      tokenAccount,
      null,
      payerWallet.publicKey,
      burnAmount,
      config.TOKEN_2022_PROGRAM_ID,
    );
    logTransactionDetails("Burn BARK Tokens", burnSignature);
  } catch (error) {
    console.error("Error burning BARK tokens:", error.message);
    throw new Error("Failed to burn BARK tokens");
  }
}

async function main() {
  try {
    await checkBalance();
    await initializeMintAccountAndTokenMetadata();
    const [sourceTokenAccount, destinationTokenAccount] = await initializeSolana_accounts();
    await transferBarkWithFee(sourceTokenAccount, destinationTokenAccount, config.MINT_AMOUNT);
    await withdrawFees(destinationTokenAccount, [sourceTokenAccount]);
    const currentQuarter = getCurrentQuarter();
    if (currentQuarter >= config.BURN_START_QUARTER) {
      console.log("Quarter >= BURN_START_QUARTER. Burning tokens...");
      const burnAmount = calculateBurnAmount(config.MINT_AMOUNT);
      console.log("Burn amount:", burnAmount);
      if (burnAmount > 0n) {
        await burnTokens(destinationTokenAccount, burnAmount);
      } else {
        console.log("No tokens to burn.");
      }
    } else {
      console.log("Quarter < BURN_START_QUARTER. Skipping token burn.");
    }
  } catch (error) {
    console.error("Error in main process:", error.message);
    throw new Error("Main process failed");
  }
}

main();
