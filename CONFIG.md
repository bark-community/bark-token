## Confiquration

**TOKEN_2022_PROGRAM_ID**: TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb

### Check
solana config get

### Devnet
solana config set --url https://api.devnet.solana.com

### Mainnet
solana config set --url https://api.mainnet-beta.solana.com

## Testnet
solana config set --url https://api.testnet.solana.com

## Account

solana-keygen new --no-passphrase -o 

## Token Test

spl-token --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb \
  create-token --interest-rate 5 --enable-metadata --transfer-fee 280 500

BARKmaEJkBJ9S2Yiu5YpSuawm21v6HbNWgVCSC3LeYKb

solana config set BARKmaEJkBJ9S2Yiu5YpSuawm21v6HbNWgVCSC3LeYKb.json

solana config set [FLAGS] [OPTIONS] <--url <URL_OR_MONIKER>|--ws <URL>|--keypair <KEYPAIR>|--commitment <COMMITMENT_LEVEL>>

solana-keygen verify <PUBKEY> 