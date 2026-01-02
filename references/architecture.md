# Bittensor Architecture

## System Overview

Bittensor is built on a layered architecture separating blockchain consensus from off-chain computation and incentive mechanisms.

```
┌─────────────────────────────────────────────────────────┐
│                   Applications Layer                    │
│  (Miners, Validators, Subnet-specific Services)         │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────┴──────────────────────────────────────┐
│                   Bittensor SDK                         │
│  (Python API, Utilities, Networking)                    │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────┴──────────────────────────────────────┐
│                  Subtensor Blockchain                    │
│  (Consensus, State, Registry, Rewards)                  │
└─────────────────────────────────────────────────────────┘
```

## Layer 1: Subtensor Blockchain

### What is Subtensor?

Subtensor is the Substrate-based blockchain that forms the foundation of Bittensor. It provides:
- **Consensus Mechanism**: Coordinates network agreement
- **Neuron Registry**: Advertises participant information (IP addresses, capabilities)
- **Value Transfer**: Facilitates TAO token transactions
- **Smart Contracts**: Supports WebAssembly contracts via `pallet-contracts`

### Technical Stack

**Framework**: Substrate
- Parity Technologies' blockchain framework
- Modular "pallets" for domain-specific logic
- FRAME runtime framework

**Consensus**:
- **Aura**: Authority-based block authoring
- **GRANDPA**: Byzantine fault-tolerant finality

**Networking**: libp2p for peer-to-peer communication

**Language**: Primarily Rust (92.6% of codebase)

### Key Responsibilities

1. **State Management**
   - Current network state (stakes, weights, bonds)
   - Participant registry (neurons, UIDs)
   - Subnet configurations
   - Historical records

2. **Consensus & Security**
   - Block production and finalization
   - Byzantine fault tolerance
   - Economic security through stake

3. **Reward Distribution**
   - TAO emission calculations
   - Incentive mechanism execution
   - Stake and bond accounting

4. **Smart Contracts**
   - WebAssembly contract execution
   - Custom logic deployment
   - Programmable extensions

### System Requirements

- **Memory**: ~286 MiB minimum
- **Storage**: Growing blockchain database
- **Platform Support**:
  - Linux x86_64
  - macOS x86_64
  - macOS ARM64 (Apple Silicon)

### Running a Subtensor Node

**Why Run a Node?**
- Direct blockchain access
- Reduced RPC dependency
- Network decentralization
- Local development

**Installation**: See [Bittensor Documentation](https://docs.learnbittensor.org)

**Node Types**:
- **Full Node**: Complete blockchain validation
- **Archive Node**: Full history retention
- **Light Client**: Minimal resource usage (planned)

## Layer 2: Bittensor SDK

### Overview

The Bittensor Python SDK provides abstractions for interacting with the network:
- Blockchain queries and transactions
- Subnet development tools
- Miner and validator frameworks
- Networking utilities

### Core Components

#### 1. Subtensor Interface
```python
import bittensor as bt

# Connect to blockchain
subtensor = bt.subtensor(network='finney')  # or 'test', 'local'

# Query network state
metagraph = subtensor.metagraph(netuid=1)
```

**Functions**:
- Query blockchain state
- Submit transactions
- Monitor events
- Access metagraph data

#### 2. Metagraph
```python
# Get metagraph for subnet
metagraph = subtensor.metagraph(netuid=1)

# Access participant data
stakes = metagraph.S  # Stake tensor
weights = metagraph.W  # Weight matrix
bonds = metagraph.B  # Bond matrix
uids = metagraph.uids  # Unique identifiers
axons = metagraph.axons  # Connection information
```

**Provides**:
- Real-time network state
- Participant information
- Stake, weight, and bond data
- Axon connection details

#### 3. Wallet
```python
# Load wallet
wallet = bt.wallet(name='default', hotkey='miner_1')

# Access keys
coldkey = wallet.coldkey
hotkey = wallet.hotkey
coldkeypub = wallet.coldkeypub
```

**Manages**:
- Coldkey (secure storage)
- Hotkey (operational activities)
- Key generation and recovery
- Transaction signing

#### 4. Axon (Server)
```python
# Create axon server
axon = bt.axon(wallet=wallet, port=8091)

# Attach forward function
def forward(synapse: bt.Synapse) -> bt.Synapse:
    # Process request
    synapse.response = process(synapse.input)
    return synapse

axon.attach(forward=forward)
axon.serve(netuid=1, subtensor=subtensor)
```

**Purpose**:
- Receive requests from validators
- Serve miner outputs
- Handle network communication

#### 5. Dendrite (Client)
```python
# Create dendrite client
dendrite = bt.dendrite(wallet=wallet)

# Query miners
responses = dendrite.query(
    axons=metagraph.axons,
    synapse=MyCustomSynapse(input_data)
)
```

**Purpose**:
- Query miners from validators
- Handle request/response cycles
- Manage connections

#### 6. Synapse (Protocol)
```python
import pydantic

class MyCustomSynapse(bt.Synapse):
    input: str
    output: Optional[str] = None

    class Config:
        validate_assignment = True
```

**Purpose**:
- Define request/response schemas
- Type-safe communication
- Subnet-specific protocols

### Installation

**Quick Install**:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/opentensor/bittensor/master/scripts/install.sh)"
```

**Via pip**:
```bash
pip install bittensor
```

**From Source**:
```bash
git clone https://github.com/opentensor/bittensor.git
cd bittensor
pip install -e .
```

**Optional Dependencies**:
- `torch`: PyTorch for ML operations
- `cubit`: CUDA support

### Verification
```python
import bittensor as bt
print(bt.__version__)
```

## Layer 3: Subnets (Application Layer)

### Subnet Architecture

Each subnet is an independent application with:
- Custom incentive mechanism
- Specific digital commodity
- Unique validation criteria
- Independent operation

### Typical Subnet Structure

```
my-subnet/
├── neurons/
│   ├── miner.py          # Miner implementation
│   ├── validator.py      # Validator implementation
│   └── __init__.py
├── protocol.py           # Custom Synapse definitions
├── validator/
│   ├── forward.py        # Request generation
│   ├── reward.py         # Scoring logic
│   └── __init__.py
├── miner/
│   ├── forward.py        # Response generation
│   └── __init__.py
├── requirements.txt
└── README.md
```

### Miner Components

**Core Responsibilities**:
1. Receive requests from validators
2. Process inputs
3. Generate outputs
4. Return responses
5. Optimize for quality and speed

**Typical Flow**:
```python
# Initialize
wallet = bt.wallet()
subtensor = bt.subtensor()
metagraph = subtensor.metagraph(netuid=1)
axon = bt.axon(wallet=wallet)

# Define forward function
def forward(synapse: CustomSynapse) -> CustomSynapse:
    synapse.output = generate_output(synapse.input)
    return synapse

# Serve
axon.attach(forward=forward)
axon.serve(netuid=1, subtensor=subtensor)
```

### Validator Components

**Core Responsibilities**:
1. Query miners with requests
2. Evaluate responses
3. Calculate rewards
4. Set weights
5. Commit weights to blockchain

**Typical Flow**:
```python
# Initialize
wallet = bt.wallet()
subtensor = bt.subtensor()
metagraph = subtensor.metagraph(netuid=1)
dendrite = bt.dendrite(wallet=wallet)

# Query miners
responses = dendrite.query(
    axons=metagraph.axons,
    synapse=CustomSynapse(input=generate_input())
)

# Score responses
rewards = score_responses(responses)

# Set weights
weights = calculate_weights(rewards)
subtensor.set_weights(
    wallet=wallet,
    netuid=1,
    uids=metagraph.uids,
    weights=weights
)
```

### Subnet-Specific Customization

Each subnet defines:
- **Protocol**: Custom Synapse classes for communication
- **Validation Logic**: How to score miner outputs
- **Mining Strategy**: How to produce quality outputs
- **Hyperparameters**: Tempo, immunity period, weights version, etc.

## Network Communication

### Axon-Dendrite Protocol

```
Validator                                  Miner
  │                                          │
  │  1. Generate request                     │
  │                                          │
  │  2. dendrite.query(synapse) ─────────>   │
  │                                          │
  │                     3. axon receives     │
  │                     4. forward(synapse)  │
  │                     5. process & respond │
  │                                          │
  │  <───────── 6. return synapse ──────────  │
  │                                          │
  │  7. score response                       │
  │  8. update weights                       │
  │                                          │
```

### Connection Management

**Axon Registration**:
- Miners register axon info to blockchain
- IP address and port stored in metagraph
- Validators query metagraph for connection info

**Dendrite Querying**:
- Validators use dendrite to query axons
- Supports parallel requests
- Handles timeouts and failures

**Network Stack**:
- HTTP/HTTPS for requests
- Pydantic for serialization
- FastAPI for axon servers

## Data Structures

### Metagraph Tensors

**Stake (S)**: `[n_neurons]`
- TAO staked by each neuron
- Determines validator influence
- Updated each tempo

**Weights (W)**: `[n_validators, n_neurons]`
- Validator assessments of miners
- Row per validator, column per neuron
- Must sum to 1 per validator

**Bonds (B)**: `[n_neurons, n_neurons]`
- Speculative stakes between neurons
- Accumulated over time
- Represents trust networks

**UIDs**: `[n_neurons]`
- Unique identifiers within subnet
- 0 to max_neurons-1
- Reused when neurons deregister

**Axons**: `[n_neurons]`
- Connection information (IP, port)
- Hotkey addresses
- Protocol versions

### Blockchain State

**Neurons**: Dictionary of registered participants
```python
{
    uid: {
        'hotkey': ss58_address,
        'coldkey': ss58_address,
        'stake': amount,
        'ip': ip_address,
        'port': port_number,
        'ip_type': 4 or 6,
        'uid': unique_id,
        'active': bool,
        ...
    }
}
```

**Subnet State**:
```python
{
    'netuid': subnet_id,
    'tempo': blocks_per_epoch,
    'immunity_period': protection_blocks,
    'min_difficulty': pow_min,
    'max_difficulty': pow_max,
    'weights_version': version,
    'max_weight_limit': max_single_weight,
    ...
}
```

## Consensus & Incentive Mechanism

### Yuma Consensus Algorithm

**Inputs**:
- Stake tensor (S): `[n_validators]`
- Weight matrix (W): `[n_validators, n_neurons]`
- Bond matrix (B): `[n_neurons, n_neurons]`

**Process**:
1. Normalize weights per validator
2. Calculate consensus weights (stake-weighted average)
3. Compute validator trust (agreement with consensus)
4. Calculate miner ranks (consensus weights)
5. Apply bonds for final ranks
6. Distribute emissions based on ranks and stakes

**Outputs**:
- Validator rewards (based on stake and consensus agreement)
- Miner rewards (based on consensus ranks)
- Updated bonds (moving average of weights)

### Emission Distribution

**Per Tempo**:
```
Total_Emission = Block_Emission × Tempo_Blocks

Subnet_Emission = Total_Emission × (Subnet_Stake / Total_Stake)

Validator_Share = Subnet_Emission × (Validator_Stake / Subnet_Stake)

Miner_Rewards = Validator distributes via weights
```

**Factors**:
- TAO emission schedule (decreasing over time)
- Subnet stake proportion
- Validator stake proportion within subnet
- Validator-set weights for miners

## Security Architecture

### Key Management

**Coldkey**:
- High security
- Offline storage recommended
- Controls TAO holdings
- Infrequent use

**Hotkey**:
- Operational security
- Can be on networked servers
- Signs weights and axon info
- Frequent use
- Swappable without losing stake

### Attack Resistance

**Sybil Resistance**:
- Registration costs (TAO burn or PoW)
- UID limits per subnet
- Stake requirements for validators

**Collusion Resistance**:
- Super-linear consensus rewards
- Outlier penalties
- Economic irrationality of minority coordination

**Byzantine Tolerance**:
- Blockchain consensus (GRANDPA)
- Subnet consensus (Yuma)
- Redundancy through multiple validators

## Performance Considerations

### Blockchain Performance

**Block Time**: ~12 seconds on Finney
**Finality**: GRANDPA provides fast finality
**TPS**: Limited by block size and computation

### Subnet Performance

**Latency**:
- Validator-miner: Network dependent
- Query timeout configurable
- Parallel queries reduce total time

**Throughput**:
- Miners can handle multiple validators
- Validators query multiple miners in parallel
- Limited by computational capacity

**Scaling**:
- Horizontal: More miners/validators
- Vertical: Better hardware
- Conditional computation: Sparse querying

## Deployment Environments

### Mainnet (Finney)
- **Network**: `finney`
- **Token**: Real TAO
- **Use**: Production
- **Chain Endpoint**: `wss://entrypoint-finney.opentensor.ai:443`

### Testnet
- **Network**: `test`
- **Token**: Test TAO (no value)
- **Use**: Testing before mainnet
- **Faucet**: Available for test TAO

### Local Development
- **Network**: `local`
- **Token**: Local TAO
- **Use**: Development and testing
- **Setup**: Run local subtensor node

### Devnet
- **Network**: `devnet`
- **Token**: Dev TAO
- **Use**: Experimental features
- **Access**: Varies

## Development Tools

### btcli (Command-Line Interface)
- Wallet management
- Subnet operations
- Staking and delegation
- Governance participation

### Python SDK
- Programmatic blockchain access
- Subnet development framework
- Miner/validator templates

### Local Subtensor
- Full local development environment
- Fast iteration
- No testnet dependency

### Monitoring Tools
- Metagraph inspection
- Performance metrics
- Emission tracking
- Network health

## Best Practices

### Architecture Design
- Separate concerns (protocol, mining, validation)
- Modular code for maintainability
- Type hints and Pydantic models
- Comprehensive logging

### Security
- Coldkey offline storage
- Hotkey rotation capability
- Input validation
- Rate limiting

### Performance
- Efficient forward functions
- Parallel processing where possible
- Caching when appropriate
- Resource monitoring

### Testing
- Local testing first
- Testnet validation
- Gradual mainnet rollout
- Continuous monitoring

## Future Architecture Evolution

### Planned Enhancements
- Light client support
- Enhanced smart contract capabilities
- Cross-subnet communication
- Improved conditional computation

### Scalability Roadmap
- Subnet sharding
- State pruning
- Optimized consensus
- Layer 2 solutions

### Developer Experience
- Better tooling and SDKs
- More examples and templates
- Enhanced documentation
- Improved debugging tools
