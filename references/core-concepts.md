# Bittensor Core Concepts

## What is Bittensor?

Bittensor is an open-source platform where participants produce digital commodities, including compute power, storage space, AI inference, and training. It creates a decentralized marketplace where intelligence is priced by other intelligence systems through peer-to-peer evaluation.

## Fundamental Architecture

Bittensor operates on three core pillars:

### 1. Subnets
Incentive-based marketplaces where miners produce digital commodities and validators assess quality. Each subnet operates autonomously with its own standards and incentive mechanisms. Subnets are specialized environments focused on specific tasks:
- AI inference
- Model training
- Data storage
- Compute provision
- And other digital commodities

### 2. Blockchain & TAO Token
The Bittensor blockchain (Subtensor) functions as a system of record, maintaining:
- Network state
- Participant registry
- Transaction ledger
- Smart contract execution

TAO (τ) is the native token that serves as the economic incentive mechanism. It is distributed based on performance and used for:
- Rewarding miners and validators
- Staking to support validators
- Subnet registration
- Governance participation

### 3. SDK
The Bittensor Python SDK enables:
- Interactions between network participants
- Blockchain integration
- Subnet development
- Mining and validation operations

## Participation Roles

### Miners
**Role**: Produce digital commodities according to subnet specifications

**Responsibilities**:
- Run computational tasks
- Generate outputs (AI inference, training, storage, etc.)
- Respond to validator requests
- Compete for quality and performance

**Economics**: Earn TAO based on validator assessments

### Validators
**Role**: Evaluate the quality of miner work

**Responsibilities**:
- Query miners for outputs
- Assess work quality using subnet-specific criteria
- Set weights to rank miner performance
- Reach consensus with other validators

**Economics**: Earn TAO based on staking weight and accurate assessments

**Requirements**: Must stake TAO to participate

### Subnet Creators
**Role**: Design and manage incentive mechanisms

**Responsibilities**:
- Define the digital commodity
- Create validation criteria
- Set subnet hyperparameters
- Manage subnet operations
- Maintain subnet infrastructure

**Economics**: Register subnets by burning TAO

### Stakers (Delegators)
**Role**: Support validators with capital

**Responsibilities**:
- Delegate TAO to validators
- Select high-performing validators
- Participate in network security

**Economics**: Earn proportional share of validator rewards (minus validator take)

## Key Mechanisms

### Emission-Based Incentives
TAO tokens are distributed continuously through emissions:
- New TAO is created and distributed to subnets
- Subnets distribute TAO to validators based on stake
- Validators distribute TAO to miners based on weights
- Emissions create alignment between participant behavior and network goals

### Yuma Consensus
The consensus mechanism that determines reward distribution:
- Rewards validators who reach agreement with the majority
- Penalizes outliers and collusion attempts
- Exponentially rewards consensus (super-linear scaling)
- Resistant to collusion of up to 50% of network weight
- Makes honest majority participation economically rational

### Stake Delegation
TAO holders can:
- Delegate stake to validators without running infrastructure
- Earn rewards proportional to validator performance
- Support network security through distributed stake
- Switch delegation to improve returns

### Dynamic TAO
A mechanism for subnet token emission and trading:
- Subnets can have their own tokens
- Tokens can be traded against TAO
- Creates subnet-specific economies
- Enables price discovery for specialized services

## Network Structure

### Subtensor (Blockchain Layer)
The substrate-based blockchain that:
- Runs consensus mechanisms
- Maintains neuron registry (participant information)
- Facilitates TAO transfers
- Executes smart contracts
- Stores network state

**Technical Details**:
- Built on Substrate framework
- Uses Aura for block authoring
- Uses GRANDPA for finality
- Supports WebAssembly smart contracts
- Written primarily in Rust

### Subnets (Off-Chain Layer)
Multiple specialized platforms for specific digital commodities:
- Each subnet has unique incentive mechanisms
- Miners and validators operate off-chain
- Performance is periodically committed to blockchain
- Subnets compete for TAO emissions
- Success determined by utility and stake

### Metagraph
A data structure representing network state:
- All active neurons (miners and validators)
- Their stakes, weights, and bonds
- IP addresses and connection information
- Performance metrics
- Updated each tempo (blockchain epoch)

## How It Works: The Competitive Cycle

1. **Production**: Miners produce outputs according to subnet specifications
2. **Evaluation**: Validators query miners and assess quality
3. **Consensus**: Validators set weights and reach consensus on rankings
4. **Rewards**: Blockchain distributes TAO based on consensus and stake
5. **Iteration**: Better work generates greater compensation, creating self-reinforcing improvement

## Tensor Standardization

To enable diverse models to interact:
- Unified input/output formats
- Support for multiple modalities (text, image, speech)
- Standardized APIs across subnets
- Flexible enough for specialized tasks

## Conditional Computation

As the network scales:
- Sparse gating layers allow selective querying
- Peers query only relevant neighbors
- Reduces bandwidth requirements
- Maintains signal quality
- Enables efficient large-scale operation

## Knowledge Extraction & Distillation

- Peers can run compressed offline models
- Eliminates constant network dependency
- Enables edge deployment
- Preserves knowledge from network learning

## Core Principles

### Decentralization
- No central authority audits or ranks participants
- Peer-to-peer evaluation determines value
- Blockchain provides trustless coordination
- Open participation without gatekeeping

### Diverse Intelligence
- Legacy and niche systems can monetize capabilities
- Multiple modalities and approaches supported
- Specialization rewarded within subnets
- Innovation encouraged through competition

### Direct Incentives
- Researchers monetize work directly
- Consumers purchase intelligence without intermediaries
- Performance directly correlates with rewards
- Market mechanisms determine value

### Collusion Resistance
- Super-linear reward scaling favors honest majorities
- Economic irrationality of coordinated dishonesty
- Byzantine fault tolerance
- Long-term alignment through bonding mechanisms

## Network Parameters

### Tempo
The blockchain epoch duration, typically measured in blocks. Each tempo:
- Triggers weight updates
- Processes reward distribution
- Updates metagraph state
- Varies by subnet configuration

### Registration
To participate as a miner or validator:
- **Burn TAO**: Pay registration cost (recycled into network)
- **Proof of Work**: Complete computational puzzle
- **Recycle**: Deregister inactive neurons and claim their UID

### UIDs (Unique Identifiers)
Each neuron receives a UID within their subnet:
- Limited number per subnet
- Reused when neurons deregister
- Required for all network operations
- Subnet-specific (same entity can have different UIDs across subnets)

## Governance

### Senate
A governance body that:
- Proposes network upgrades
- Votes on parameter changes
- Manages treasury
- Requires significant stake to join

### Proposals
Network participants can:
- Submit improvement proposals
- Vote on changes (weighted by stake)
- Influence network evolution
- Participate in decentralized governance

## Testing & Development

Bittensor provides multiple environments:
- **Mainnet (Finney)**: Production network with real TAO
- **Testnet**: Testing environment with test TAO
- **Local**: Fully local development setup
- **Devnet**: Development network for experimentation

Recommended workflow:
1. Test mechanisms locally
2. Deploy to testnet for validation
3. Launch on mainnet for production

## Technical Requirements

### For Validators
- Significant TAO stake (varies by subnet)
- Computational resources for evaluation
- Network connectivity for querying miners
- Understanding of subnet-specific criteria

### For Miners
- Computational resources for production
- Network connectivity for receiving requests
- Capability to produce subnet-specific outputs
- Competitive performance for rewards

### For Subnet Creators
- TAO to burn for registration
- Technical capability to implement incentive mechanisms
- Infrastructure for subnet operation
- Understanding of Bittensor SDK

## Economic Model

### TAO Supply
- Fixed maximum supply
- Continuous emission through inflation
- Emission rate decreases over time (halving schedule)
- Distribution through subnet rewards

### Value Accrual
TAO value derives from:
- Demand for subnet services
- Network security requirements (staking)
- Governance participation
- Subnet registration costs

### Subnet Economics
Each subnet:
- Competes for TAO emissions
- Emission allocation based on subnet stake
- Creates internal economy for its commodity
- Can implement custom token economics (Dynamic TAO)

## Key Terminology

- **Neuron**: A participant (miner or validator) registered on a subnet
- **Hotkey**: Key for operational activities (signing weights, transactions)
- **Coldkey**: Key for storing TAO (higher security)
- **Weight**: Validator's assessment of miner performance
- **Bond**: Speculative stake in another neuron
- **Stake**: TAO locked to participate as validator
- **Emission**: Newly created TAO distributed as rewards
- **UID**: Unique identifier for a neuron within a subnet
- **Netuid**: Network unique identifier for a subnet
- **Tempo**: Blockchain epoch for updates and rewards
- **Metagraph**: Data structure representing network state
