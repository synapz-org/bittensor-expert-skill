# Bittensor CLI (btcli) Comprehensive Guide

## Overview

The btcli is the command-line interface for the Bittensor platform, enabling users to manage wallet operations, subnet registration, TAO delegation, and governance voting.

**Supported Platforms**:
- macOS (full support)
- Linux (full support)
- WSL 2 (full support)
- Windows (limited support - wallet transactions only)

## Installation

### PyPI (Recommended)
```bash
pip install -U bittensor-cli
```

### Homebrew (macOS)
```bash
brew install btcli
```

### From Source
```bash
python3 -m venv btcli_venv
source btcli_venv/bin/activate
git clone https://github.com/opentensor/btcli.git
cd btcli
pip3 install .
```

### With Bittensor SDK
```bash
pip install -U bittensor[cli]
```

### Verification
```bash
btcli --version
```

## Configuration

### Configuration File
**Location**: `~/.bittensor/config.yml`

### View Current Configuration
```bash
btcli config get
```

### Set Configuration Values
```bash
# Basic settings
btcli config set --wallet-name default --network finney

# Safety settings
btcli config set --safe-staking --rate-tolerance 0.1

# Display settings
btcli config set --metagraph-columns uid,stake,emission
```

### Clear Configuration
```bash
# Clear all configuration
btcli config clear --all

# Clear specific fields
btcli config clear --chain --network
```

### Environment Variables

- `USE_TORCH`: Enable Torch instead of NumPy
- `DISK_CACHE`: Enable substrate call caching
- `BTCLI_CONFIG_PATH`: Custom config file location
- `BTCLI_DEBUG_FILE`: Debug log storage location (default: `~/.bittensor/debug.txt`)

## Global Options

| Flag | Purpose |
|------|---------|
| `--version` | Show version |
| `--debug` | Enable debug mode |
| `--help` | Show help |
| `--commands` | List all commands |
| `--wallet-name` | Specify wallet |
| `--hotkey` / `-H` | Select hotkey |
| `--network` | Connect to network (finney, test, local) |
| `--netuid` / `-n` | Subnet identifier |
| `--safe` | Enable rate tolerance protection |
| `--prompt` / `-y` | Interactive vs. automatic mode |
| `--json-output` | Machine-readable output |
| `--verbose` | Detailed output |
| `--quiet` | Minimal output |

## Wallet Management

### List Wallets
```bash
# Show all wallets and hotkeys
btcli wallet list
```

### Create New Wallet
```bash
# Create new coldkey and hotkey (24-word mnemonic)
btcli wallet create --n-words 24

# Create 12-word mnemonic
btcli wallet create --n-words 12

# Create with specific wallet name
btcli wallet create --wallet-name my_wallet
```

### Create Additional Hotkeys
```bash
# Add new hotkey to existing wallet
btcli wallet new-hotkey --wallet-name default

# Create hotkey with specific name
btcli wallet new-hotkey --wallet-name default --hotkey validator_1
```

### Create Additional Coldkeys
```bash
# Create new coldkey only
btcli wallet new-coldkey --n-words 12
```

### Regenerate Keys from Mnemonic/Seed

```bash
# Regenerate coldkey from mnemonic
btcli wallet regen-coldkey --mnemonic "word1 word2 word3 ..."

# Regenerate hotkey from seed
btcli wallet regen-hotkey --seed 0x1234567890abcdef...

# Regenerate coldkey public key from SS58 address
btcli wallet regen-coldkeypub --ss58 5DkQ4...

# Regenerate hotkey public key from SS58 address
btcli wallet regen-hotkeypub --ss58_address 5DkQ4...
```

### Check Balances
```bash
# Check specific wallet balance
btcli wallet balance --wallet-name default

# Check all wallet balances
btcli wallet balance --all

# Get overview of all registered accounts
btcli wallet overview

# Overview for specific wallet
btcli wallet overview --wallet-name default
```

### Transfer TAO
```bash
# Transfer to address
btcli wallet transfer --dest 5Dp8... --amount 100

# Transfer entire balance
btcli wallet transfer --dest 5Dp8... --all

# Transfer from specific wallet
btcli wallet transfer --wallet-name default --dest 5Dp8... --amount 50
```

### Identity Management

```bash
# Set on-chain identity (costs 1 TAO)
btcli wallet set-identity --wallet-name default

# Get identity for address
btcli wallet get-identity --ss58 5Dk...

# Set identity with specific fields
btcli wallet set-identity \
  --display "My Name" \
  --legal "Legal Name" \
  --web "https://example.com" \
  --riot "@username:matrix.org" \
  --email "email@example.com" \
  --twitter "@handle"
```

### Message Signing & Verification

```bash
# Sign a message
btcli wallet sign --wallet-name default --message '{"data": "here"}'

# Verify a signature
btcli wallet verify \
  --message "Hello" \
  --signature 0xabc123... \
  --address 5Gr...
```

### Hotkey Swapping

```bash
# Swap to new hotkey
btcli wallet swap-hotkey destination_hotkey --wallet-name default

# Swap to hotkey with specific name
btcli wallet swap-hotkey new_hotkey --wallet-name default --hotkey old_hotkey
```

### Coldkey Swapping

```bash
# Swap to new coldkey
btcli wallet swap-coldkey --new-coldkey-ss58 5Dk...X3q

# Check pending swaps for wallet
btcli wallet swap-check --wallet-name my_wallet

# Check all pending swaps
btcli wallet swap-check --all
```

## Staking & Delegation

### Add Stake

```bash
# Interactive staking
btcli stake add

# Stake specific amount on subnet
btcli stake add --amount 100 --netuid 1

# Stake with safety checks
btcli stake add --amount 100 --netuid 1 --safe --tolerance 0.1

# Stake on multiple subnets
btcli stake add -n 4,14,64 --amount 100

# Stake all balance
btcli stake add --all --netuid 3
```

### Remove Stake

```bash
# Interactive unstaking
btcli stake remove

# Unstake specific amount
btcli stake remove --amount 100 --netuid 1

# Unstake with safety checks
btcli stake remove --amount 100 --netuid 1 --safe

# Unstake everything
btcli stake remove --all

# Unstake all Alpha tokens
btcli stake remove --all-alpha
```

### Move Stake

```bash
# Interactive stake movement
btcli stake move

# Move stake between subnets
btcli stake move --origin-netuid 1 --dest-netuid 2 --amount 50

# Move with specific wallet and hotkey
btcli stake move \
  --wallet-name default \
  --hotkey validator_1 \
  --origin-netuid 1 \
  --dest-netuid 2 \
  --amount 100
```

### Transfer Stake Between Coldkeys

```bash
# Transfer stake to another wallet
btcli stake transfer \
  --origin-netuid 1 \
  --dest-netuid 2 \
  --dest wallet2 \
  --amount 100

# Transfer to specific address
btcli stake transfer \
  --dest 5FrLx...nGyHLcK \
  --amount 100
```

### Swap Stake Between Subnets

```bash
# Swap stake between subnets
btcli stake swap --origin-netuid 1 --dest-netuid 2 --amount 100

# Swap all stake
btcli stake swap --all --origin-netuid 1 --dest-netuid 2
```

### Auto-Stake Configuration

```bash
# View auto-stake destination
btcli stake auto --wallet-name my_wallet

# Set auto-stake to specific subnet
btcli stake set-auto --netuid 1 --wallet-name my_wallet
```

### Claim Emissions

```bash
# Choose to keep or swap root emissions
btcli stake claim swap

# Process claims for specific subnets (max 5 at once)
btcli stake process-claim --netuids 1,2,3

# Process claims for all subnets
btcli stake process-claim --all
```

### Child Hotkey Management

```bash
# View child hotkeys on subnet
btcli stake child get --netuid 1

# Set child hotkeys with proportions
btcli stake child set \
  -c 5FCL... \
  -c 5Hp5... \
  -p 0.3 \
  -p 0.7 \
  --netuid 1

# Revoke child hotkey
btcli stake child revoke --hotkey default --netuid 1

# Set take percentage for child
btcli stake child take \
  --child-hotkey-ss58 <address> \
  --take 0.12 \
  --netuid 1
```

## Subnet Management

### List Subnets

```bash
# List all subnets
btcli subnets list

# Live updating view
btcli subnets list --live

# List with specific columns
btcli subnets list --columns netuid,name,owner,tempo
```

### Subnet Information

```bash
# Show detailed subnet info
btcli subnets show --netuid 1

# View subnet hyperparameters
btcli subnets hyperparameters --netuid 1

# Check subnet registration cost
btcli subnets burn-cost

# View subnet metagraph
btcli subnets metagraph --netuid 1
```

### Create Subnet

```bash
# Interactive subnet creation
btcli subnets create

# Create with parameters
btcli subnets create \
  --subnet-name "My Subnet" \
  --github-repo "https://github.com/org/subnet"
```

### Register on Subnet

```bash
# Register via TAO burning (recycling)
btcli subnets register --netuid 1

# Register with specific wallet and hotkey
btcli subnets register \
  --netuid 1 \
  --wallet-name default \
  --hotkey miner_1

# Register via Proof of Work
btcli subnets pow-register --netuid 1 --num-processes 4

# PoW registration with CUDA
btcli subnets pow-register --netuid 1 --num-processes 4 --cuda
```

### Subnet Pricing

```bash
# View historical pricing (4 hours)
btcli subnets price --netuid 1

# View all subnet prices
btcli subnets price --all

# View prices in HTML browser
btcli subnets price --all --html

# View specific subnets with logging
btcli subnets price --netuids 1,2,3,4 --html --log
```

### Subnet Mechanisms (Multi-Pool)

```bash
# Count mechanisms for subnet
btcli subnet mechanisms count --netuid 12

# Set mechanism count
btcli subnet mechanisms set --netuid 12 --count 2

# View mechanism emissions
btcli subnet mechanisms emissions --netuid 12

# Split emissions between mechanisms
btcli subnet mechanisms split-emissions --netuid 12 --split 70,30
```

### Subnet Identity & Configuration

```bash
# Set subnet identity
btcli subnets set-identity --netuid 1

# Get subnet identity
btcli subnets get-identity --netuid 1

# Set subnet symbol
btcli subnets set-symbol --netuid 1 シ

# Check emission start schedule
btcli subnets check-start --netuid 1
```

## Governance & Administration

### View Senate & Proposals

```bash
# List Senate members
btcli sudo senate

# View active proposals
btcli sudo proposals
```

### Vote on Proposals

```bash
# Cast vote on proposal
btcli sudo senate-vote --proposal <hash>

# Vote with specific wallet
btcli sudo senate-vote \
  --proposal <hash> \
  --wallet-name default
```

### Hyperparameter Management

```bash
# View all hyperparameters for subnet
btcli sudo get --netuid 1

# Set hyperparameter
btcli sudo set --netuid 1 --param tempo --value 400

# Common hyperparameters:
# - tempo: Blocks per epoch
# - immunity_period: Protection period for new neurons
# - min_difficulty: Minimum PoW difficulty
# - max_difficulty: Maximum PoW difficulty
# - weights_version: Weight setting version
# - weights_rate_limit: Min blocks between weight updates
# - max_weight_limit: Maximum single weight value
```

### Delegate Take Management

```bash
# Set validator take percentage
btcli sudo set-take --wallet-name my_wallet --take 0.12

# Get current take percentage
btcli sudo get-take --wallet-name my_wallet
```

### Subnet Trimming

```bash
# Limit UIDs on subnet (remove lowest performers)
btcli sudo trim --netuid 95 --max 64
```

## Weights & Validation

### Commit Weights (Encrypted Voting)

```bash
# Commit weights
btcli weights commit \
  --netuid 1 \
  --uids 1,2,3,4 \
  --weights 0.1,0.2,0.3,0.4

# Commit with specific wallet
btcli weights commit \
  --netuid 1 \
  --wallet-name default \
  --hotkey validator_1 \
  --uids 1,2,3,4 \
  --weights 0.1,0.2,0.3,0.4
```

### Reveal Weights

```bash
# Reveal committed weights
btcli weights reveal \
  --netuid 1 \
  --uids 1,2,3,4 \
  --weights 0.1,0.2,0.3,0.4 \
  --salt 163,241,217,11

# Must use same UIDs, weights, and salt as commit
```

## Crowdloans

### Create Crowdloan Campaign

```bash
# Create basic campaign
btcli crowd create --deposit 10 --cap 1000 --target-address 5D...

# Create subnet lease campaign
btcli crowd create \
  --subnet-lease \
  --emissions-share 30 \
  --cap 5000
```

### Participate in Campaigns

```bash
# Contribute to campaign
btcli crowd contribute --id 0 --amount 100

# Withdraw contribution
btcli crowd withdraw --id 0
```

### View Campaigns

```bash
# List all campaigns
btcli crowd list

# View campaign details
btcli crowd info --id 0
```

### Manage Campaigns (Creator Only)

```bash
# Finalize funded campaign
btcli crowd finalize --id 0

# Update campaign terms
btcli crowd update --id 0 --cap 2000

# Refund all contributors
btcli crowd refund --id 0

# Dissolve campaign
btcli crowd dissolve --id 0
```

## Liquidity Management

### Add Liquidity Position

```bash
# Add liquidity with price range
btcli liquidity add \
  --netuid 1 \
  --liquidity 100 \
  --price-low 0.5 \
  --price-high 2.0
```

### View Liquidity Positions

```bash
# List positions for subnet
btcli liquidity list --netuid 1

# List positions for specific wallet
btcli liquidity list --netuid 1 --wallet-name default
```

### Modify Liquidity

```bash
# Modify existing position
btcli liquidity modify \
  --netuid 1 \
  --position-id 5 \
  --liquidity-delta 50
```

### Remove Liquidity

```bash
# Remove specific position
btcli liquidity remove --netuid 1 --position-id 5

# Remove all positions
btcli liquidity remove --netuid 1 --all
```

## Utilities

### TAO/Rao Conversion

```bash
# Convert TAO to Rao
btcli utils convert --tao 100

# Convert Rao to TAO
btcli utils convert --rao 1000000000
```

**Note**: 1 TAO = 1,000,000,000 Rao (1e9)

### Network Latency Testing

```bash
# Test network connection speed
btcli utils latency --network ws://127.0.0.1:9944

# Test public network
btcli utils latency --network finney
```

## Dashboard & Visualization

### HTML Dashboard

```bash
# Launch interactive dashboard
btcli view dashboard

# Dashboard for specific subnet
btcli view dashboard --netuid 1
```

## Common Workflows

### Setting Up a New Wallet

```bash
# 1. Create wallet
btcli wallet create --wallet-name my_wallet --n-words 24

# 2. Check balance (get TAO from exchange/faucet first)
btcli wallet balance --wallet-name my_wallet

# 3. Set as default
btcli config set --wallet-name my_wallet
```

### Becoming a Miner

```bash
# 1. Create wallet with mining hotkey
btcli wallet create --wallet-name miner_wallet
btcli wallet new-hotkey --wallet-name miner_wallet --hotkey mining_key

# 2. Check registration cost
btcli subnets burn-cost

# 3. Register on subnet
btcli subnets register --netuid 1 --wallet-name miner_wallet --hotkey mining_key

# 4. Run your miner code (subnet-specific)
# python miner.py --wallet-name miner_wallet --hotkey mining_key --netuid 1
```

### Becoming a Validator

```bash
# 1. Create wallet with validator hotkey
btcli wallet create --wallet-name validator_wallet
btcli wallet new-hotkey --wallet-name validator_wallet --hotkey validator_key

# 2. Register on subnet
btcli subnets register --netuid 1 --wallet-name validator_wallet --hotkey validator_key

# 3. Add stake (validators need significant stake)
btcli stake add --amount 1000 --netuid 1 --wallet-name validator_wallet --hotkey validator_key

# 4. Run your validator code (subnet-specific)
# python validator.py --wallet-name validator_wallet --hotkey validator_key --netuid 1
```

### Delegating to a Validator

```bash
# 1. Find a validator to delegate to (research required)
btcli subnets metagraph --netuid 1

# 2. Delegate stake
btcli stake add --amount 100 --netuid 1 --wallet-name my_wallet

# 3. Monitor rewards
btcli wallet overview --wallet-name my_wallet
```

### Creating a Subnet

```bash
# 1. Check cost to create subnet
btcli subnets burn-cost

# 2. Create subnet (burns TAO)
btcli subnets create --subnet-name "My Subnet" --github-repo "https://github.com/me/subnet"

# 3. Set subnet parameters
btcli sudo set --netuid <your_netuid> --param tempo --value 360

# 4. Set subnet identity
btcli subnets set-identity --netuid <your_netuid>
```

## Safety Best Practices

### Use Safe Mode
```bash
# Enable safe staking with rate tolerance
btcli config set --safe-staking --rate-tolerance 0.1
```

### Backup Mnemonics
- **Always** backup your 24/12-word mnemonic phrases
- Store in secure, offline location
- Never share with anyone
- Test recovery before storing significant TAO

### Key Separation
- **Coldkey**: For storing TAO (keep offline/secure)
- **Hotkey**: For operational activities (can be on server)
- Never expose coldkey private key on networked systems

### Test on Testnet
```bash
# Connect to testnet
btcli config set --network test

# Verify network
btcli wallet balance --network test
```

### Double-Check Addresses
- Always verify destination addresses before transfers
- Use `--prompt` flag to confirm transactions
- Start with small test amounts

### Monitor Regularly
```bash
# Regular checks
btcli wallet overview
btcli subnets metagraph --netuid <your_netuid>
btcli stake auto
```

## Troubleshooting

### Common Issues

**"Insufficient funds"**
- Check balance: `btcli wallet balance`
- Ensure enough TAO for transaction + fees

**"Hotkey already registered"**
- Each hotkey can only register once per subnet
- Create new hotkey or use different subnet

**"PoW registration failed"**
- Try increasing `--num-processes`
- Use `--cuda` if GPU available
- Network difficulty may be high

**"Connection refused"**
- Check network setting: `btcli config get`
- Verify internet connection
- Try different RPC endpoint

### Debug Mode
```bash
# Enable debug output
btcli --debug [command]

# Check debug logs
cat ~/.bittensor/debug.txt
```

### Getting Help
```bash
# General help
btcli --help

# Command-specific help
btcli wallet --help
btcli stake --help
btcli subnets --help

# List all commands
btcli --commands
```

## Advanced Usage

### Custom RPC Endpoints
```bash
# Use custom subtensor endpoint
btcli --subtensor.chain_endpoint ws://custom:9944 wallet balance
```

### JSON Output for Scripting
```bash
# Get machine-readable output
btcli wallet balance --json-output

# Use in scripts
BALANCE=$(btcli wallet balance --json-output | jq -r '.balance')
```

### Batch Operations
```bash
# Stake on multiple subnets
btcli stake add -n 1,2,3,4 --amount 100

# Remove liquidity from all positions
btcli liquidity remove --netuid 1 --all
```

## Quick Reference

### Most Common Commands

```bash
# Wallet management
btcli wallet create
btcli wallet balance
btcli wallet transfer --dest <address> --amount <amount>

# Staking
btcli stake add --amount <amount> --netuid <netuid>
btcli stake remove --amount <amount> --netuid <netuid>

# Subnet operations
btcli subnets list
btcli subnets register --netuid <netuid>
btcli subnets metagraph --netuid <netuid>

# Information
btcli wallet overview
btcli subnets show --netuid <netuid>
btcli subnets hyperparameters --netuid <netuid>
```

### Important File Locations

- **Config**: `~/.bittensor/config.yml`
- **Wallets**: `~/.bittensor/wallets/`
- **Debug logs**: `~/.bittensor/debug.txt`

### Network Values

- **Mainnet**: `--network finney`
- **Testnet**: `--network test`
- **Local**: `--network local`

### Default Ports

- **Subtensor RPC**: 9944
- **Axon (miner/validator)**: 8091 (customizable)
