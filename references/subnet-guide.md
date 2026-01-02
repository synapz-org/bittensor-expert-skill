# Bittensor Subnet Creation & Management Guide

## What is a Subnet?

A subnet is an independent incentive-based marketplace within the Bittensor network where:
- **Miners** produce specific digital commodities
- **Validators** assess the quality of miner outputs
- **Subnet owners** design and manage the incentive mechanism
- **The network** distributes TAO rewards based on performance

Each subnet operates autonomously with its own:
- Incentive mechanism
- Validation criteria
- Computational task
- Economic parameters

## Subnet Use Cases

### Current Subnet Types
- **AI Inference**: Text generation, image creation, code completion
- **Model Training**: Distributed training, fine-tuning
- **Data Storage**: Decentralized storage solutions
- **Compute Provision**: General-purpose computation
- **Specialized AI**: Audio processing, video generation, etc.

### Emerging Opportunities
- Domain-specific AI models
- Blockchain oracles
- Data processing pipelines
- Scientific computation
- And any digital commodity that can be evaluated programmatically

## Subnet Creation Process

### Prerequisites

**Technical Requirements**:
- Understanding of Python and Bittensor SDK
- Ability to design incentive mechanisms
- Understanding of the problem domain
- Infrastructure for hosting subnet code

**Economic Requirements**:
- TAO tokens for subnet registration (check current cost)
- Ongoing infrastructure costs
- Potential marketing/community building

**Knowledge Requirements**:
- Familiarity with Bittensor architecture
- Understanding of Yuma Consensus
- Experience with distributed systems
- Ability to define quality metrics

### Step 1: Design Your Incentive Mechanism

This is the most critical step. Ask yourself:

**What commodity will miners produce?**
- Be specific about inputs and outputs
- Define data formats clearly
- Consider computational feasibility

**How will validators evaluate quality?**
- Objective metrics preferred
- Reproducible evaluation
- Resistant to gaming
- Computationally efficient

**What prevents gaming the system?**
- Hidden test sets
- Randomization
- Cross-validation
- Cost of cheating > potential reward

**Example: Text Generation Subnet**
- **Commodity**: Generated text completions
- **Evaluation**: Perplexity, coherence, factuality, instruction following
- **Anti-gaming**: Random prompts, human feedback integration

### Step 2: Develop and Test Locally

**Local Testing Workflow**:
1. Set up local subtensor node
2. Implement miner and validator code
3. Test with multiple miners
4. Validate scoring mechanism
5. Ensure economic incentives work as intended

**Key Code Components**:

#### Protocol Definition
```python
# protocol.py
import bittensor as bt
from pydantic import Field

class MySubnetSynapse(bt.Synapse):
    """Custom protocol for your subnet"""
    input: str = Field(..., description="Input data for miners")
    output: str = Field(None, description="Miner generated output")

    class Config:
        validate_assignment = True
```

#### Miner Implementation
```python
# neurons/miner.py
import bittensor as bt
from protocol import MySubnetSynapse

def forward(synapse: MySubnetSynapse) -> MySubnetSynapse:
    """Process validator requests"""
    # Implement your mining logic
    synapse.output = generate_output(synapse.input)
    return synapse

# Setup and serve
wallet = bt.wallet()
subtensor = bt.subtensor(network='local')
axon = bt.axon(wallet=wallet)
axon.attach(forward=forward)
axon.serve(netuid=1, subtensor=subtensor)
```

#### Validator Implementation
```python
# neurons/validator.py
import bittensor as bt
from protocol import MySubnetSynapse

def score_responses(responses):
    """Evaluate miner outputs"""
    # Implement your scoring logic
    return [calculate_score(r) for r in responses]

# Main validation loop
wallet = bt.wallet()
subtensor = bt.subtensor(network='local')
dendrite = bt.dendrite(wallet=wallet)
metagraph = subtensor.metagraph(netuid=1)

while True:
    # Query miners
    responses = dendrite.query(
        axons=metagraph.axons,
        synapse=MySubnetSynapse(input=generate_input())
    )

    # Score and set weights
    scores = score_responses(responses)
    weights = normalize_weights(scores)
    subtensor.set_weights(
        wallet=wallet,
        netuid=1,
        uids=metagraph.uids,
        weights=weights
    )

    time.sleep(tempo_duration)
```

### Step 3: Deploy to Testnet

**Testnet Benefits**:
- Test with real network conditions
- No cost for mistakes
- Community testing
- Gather feedback

**Deployment Steps**:
```bash
# 1. Configure for testnet
btcli config set --network test

# 2. Create testnet wallet
btcli wallet create --wallet-name testnet_owner

# 3. Get test TAO from faucet
# (Follow testnet documentation)

# 4. Register subnet
btcli subnets create --subnet-name "My Subnet" --github-repo "https://github.com/me/subnet"

# 5. Note your netuid
# Output will show: "Subnet registered with netuid: X"

# 6. Set hyperparameters
btcli sudo set --netuid X --param tempo --value 360
```

**Testnet Validation**:
- Run multiple miners yourself
- Invite community to test
- Monitor for unexpected behaviors
- Iterate on design

### Step 4: Register on Mainnet

**Pre-Launch Checklist**:
- [ ] Thoroughly tested on testnet
- [ ] Documentation complete
- [ ] Clear value proposition
- [ ] Community interest validated
- [ ] Infrastructure ready
- [ ] Monitoring tools in place
- [ ] Support channels established

**Registration**:
```bash
# 1. Check registration cost
btcli subnets burn-cost
# Example output: "Cost to register: 100.0 TAO"

# 2. Configure for mainnet
btcli config set --network finney

# 3. Register subnet (burns TAO)
btcli subnets create \
  --subnet-name "My Production Subnet" \
  --github-repo "https://github.com/me/subnet"

# 4. Record your netuid
# CRITICAL: Save this netuid - you'll need it for all operations

# 5. Set initial hyperparameters
btcli sudo set --netuid <your_netuid> --param tempo --value 360
```

### Step 5: Configure Subnet

**Critical Hyperparameters**:

```bash
# Tempo: Blocks per epoch (affects reward frequency)
btcli sudo set --netuid <netuid> --param tempo --value 360

# Immunity period: Protection for new neurons (in blocks)
btcli sudo set --netuid <netuid> --param immunity_period --value 7200

# Min/Max difficulty: PoW registration difficulty
btcli sudo set --netuid <netuid> --param min_difficulty --value 1000000
btcli sudo set --netuid <netuid> --param max_difficulty --value 1000000000

# Weights rate limit: Min blocks between weight updates
btcli sudo set --netuid <netuid> --param weights_rate_limit --value 100

# Max weight limit: Maximum single weight value (0-65535)
btcli sudo set --netuid <netuid> --param max_weight_limit --value 65535
```

**Parameter Considerations**:
- **Tempo**: Shorter = more frequent rewards, higher blockchain load
- **Immunity Period**: Protects new miners from deregistration
- **Difficulty**: Controls registration cost (higher = fewer registrations)
- **Weights Rate Limit**: Prevents validator weight spam
- **Max Weight Limit**: Constrains single miner dominance

### Step 6: Set Subnet Identity

```bash
btcli subnets set-identity --netuid <netuid>
```

**Information to Provide**:
- Subnet name
- Description
- Website/documentation URL
- GitHub repository
- Discord/community links
- Logo/branding

**Why Identity Matters**:
- Helps validators find your subnet
- Builds trust and credibility
- Improves discoverability
- Professional presentation

### Step 7: Launch and Promote

**Launch Activities**:
1. **Documentation**: Comprehensive guides for miners and validators
2. **GitHub**: Well-organized repository with examples
3. **Community**: Discord/Telegram for support
4. **Marketing**: Announce on Bittensor Discord, Twitter, etc.
5. **Incentives**: Consider early participant rewards

**Initial Participants**:
- Run your own miners/validators to bootstrap
- Recruit early testers
- Provide clear onboarding
- Offer support

## Subnet Management

### Monitoring Subnet Health

```bash
# View subnet status
btcli subnets show --netuid <netuid>

# View metagraph
btcli subnets metagraph --netuid <netuid>

# Check hyperparameters
btcli subnets hyperparameters --netuid <netuid>

# Monitor emissions
btcli subnets price --netuid <netuid>
```

**Key Metrics**:
- Number of active miners/validators
- Registration rate
- Emission trends
- Validator stake levels
- Miner performance distribution

### Adjusting Hyperparameters

**When to Adjust**:
- Too many/few registrations → Adjust difficulty
- Reward frequency issues → Adjust tempo
- New miner churn → Adjust immunity period
- Weight manipulation → Adjust rate limit

**How to Adjust**:
```bash
# Always test impact before applying
btcli sudo set --netuid <netuid> --param <parameter> --value <new_value>
```

**Best Practices**:
- Announce changes to community first
- Make gradual adjustments
- Monitor impact closely
- Document rationale

### Updating Subnet Code

**Miner Updates**:
- Backwards compatible when possible
- Version your protocol
- Provide migration guides
- Give advance notice

**Validator Updates**:
- Test thoroughly before deploying
- Coordinate with other validators
- Monitor for unexpected behaviors
- Rollback plan ready

**Protocol Changes**:
```python
# Version your synapse
class MySubnetSynapse(bt.Synapse):
    protocol_version: int = 2  # Increment on breaking changes
    # ... rest of fields
```

### Governance Participation

As subnet owner, you can:
- Propose hyperparameter changes
- Participate in network governance
- Collaborate with other subnet owners
- Advocate for subnet interests

## Multi-Pool Subnets (Advanced)

**What are Mechanisms?**
Multiple independent reward pools within one subnet, allowing:
- Different mining tasks in same subnet
- Independent reward distribution
- Specialized miner types
- Complex incentive structures

**Setup**:
```bash
# Set number of mechanisms
btcli subnet mechanisms set --netuid <netuid> --count 2

# Split emissions between mechanisms
btcli subnet mechanisms split-emissions --netuid <netuid> --split 70,30

# View current setup
btcli subnet mechanisms count --netuid <netuid>
btcli subnet mechanisms emissions --netuid <netuid>
```

**Use Cases**:
- Text + image generation in one subnet
- Fast inference + quality inference pools
- Multiple model sizes with different rewards

## Economic Considerations

### Subnet Economics

**Emission Allocation**:
```
Subnet_Emissions = Network_Emissions × (Subnet_Stake / Total_Network_Stake)
```

**Attracting Stake**:
- Demonstrate utility and value
- Build strong community
- Show consistent emissions
- Prove sustainability
- Market effectively

**Sustainability**:
- Emissions must cover miner costs + profit
- Validator rewards sufficient to attract stake
- Long-term value proposition
- Competitive positioning

### Dynamic TAO (Optional)

**What is Dynamic TAO?**
- Subnet-specific tokens
- Tradeable against TAO
- Custom economics per subnet
- Price discovery mechanism

**Benefits**:
- Specialized tokenomics
- Market-driven valuation
- Additional funding mechanisms
- Ecosystem growth

**Setup**: See Bittensor documentation for Dynamic TAO configuration

## Best Practices

### Incentive Design

**Principles**:
1. **Aligned Incentives**: Mining rewards proportional to value provided
2. **Gaming Resistance**: Cost of cheating > potential reward
3. **Sustainability**: Economics work long-term
4. **Simplicity**: Easy to understand and implement

**Anti-Gaming Measures**:
- Randomization in validation
- Hidden test sets
- Cross-validator consensus
- Temporal variation
- Cost-based barriers

**Common Pitfalls**:
- Easily gameable metrics
- Centralized dependencies
- Insufficient validator diversity
- Poor economic incentives

### Community Building

**Essential Elements**:
- Clear documentation
- Responsive support
- Regular updates
- Transparency
- Inclusive participation

**Communication Channels**:
- GitHub (code and issues)
- Discord/Telegram (community)
- Twitter (announcements)
- Medium/Blog (deep dives)

**Engagement**:
- Regular AMAs
- Tutorial content
- Showcase successful participants
- Gather and act on feedback

### Technical Excellence

**Code Quality**:
- Comprehensive tests
- Clear documentation
- Type hints and validation
- Error handling
- Logging and monitoring

**Performance**:
- Efficient algorithms
- Appropriate timeouts
- Resource management
- Scalability planning

**Security**:
- Input validation
- Rate limiting
- DDoS resistance
- Secure key management

## Troubleshooting

### Low Participation

**Symptoms**: Few miners or validators
**Causes**:
- Unclear value proposition
- Difficult onboarding
- Poor economics
- Lack of awareness

**Solutions**:
- Improve documentation
- Simplify setup
- Adjust hyperparameters
- Market effectively
- Provide support

### Gaming/Cheating

**Symptoms**: Suspicious patterns, collusion, fake work
**Causes**:
- Predictable validation
- Weak anti-gaming measures
- Insufficient validator diversity

**Solutions**:
- Add randomization
- Implement hidden tests
- Encourage validator diversity
- Adjust weights rate limits
- Community reporting

### Technical Issues

**Symptoms**: Crashes, errors, poor performance
**Causes**:
- Bugs in code
- Resource constraints
- Network issues
- Blockchain problems

**Solutions**:
- Comprehensive logging
- Monitoring and alerts
- Gradual rollouts
- Testing infrastructure
- Community feedback loops

### Economic Problems

**Symptoms**: Insufficient stake, declining participation
**Causes**:
- Emissions too low
- Costs too high
- Better alternatives
- Unclear value

**Solutions**:
- Demonstrate utility
- Optimize costs
- Market positioning
- Partnership building
- Token mechanics adjustment

## Subnet Lifecycle

### Early Stage (0-3 months)
- Focus: Getting initial participants
- Goals: Prove concept, gather feedback
- Metrics: Registration rate, basic functionality

### Growth Stage (3-12 months)
- Focus: Scaling and optimization
- Goals: Increase stake, improve quality
- Metrics: Emission growth, participant satisfaction

### Mature Stage (12+ months)
- Focus: Sustainability and evolution
- Goals: Stable operations, continuous improvement
- Metrics: Long-term retention, ecosystem impact

## Advanced Topics

### Cross-Subnet Collaboration
- Share infrastructure
- Coordinate on standards
- Joint marketing
- Ecosystem building

### Subnet Upgrades
- Plan migration paths
- Maintain backwards compatibility
- Coordinate with participants
- Test extensively

### Scaling Strategies
- Horizontal: More miners/validators
- Vertical: Better hardware/algorithms
- Architectural: Sharding, pooling
- Economic: Emission optimization

## Resources

### Official Documentation
- https://docs.learnbittensor.org
- GitHub: https://github.com/opentensor

### Community
- Discord: Bittensor Official
- Twitter: @bittensor_
- Reddit: r/bittensor

### Examples
Study existing successful subnets for patterns and best practices.

## Checklist: Subnet Launch

### Pre-Development
- [ ] Clear problem definition
- [ ] Incentive mechanism designed
- [ ] Anti-gaming measures planned
- [ ] Technical architecture outlined

### Development
- [ ] Protocol defined (Synapse)
- [ ] Miner implementation complete
- [ ] Validator implementation complete
- [ ] Scoring mechanism tested
- [ ] Local testing successful

### Testnet
- [ ] Deployed to testnet
- [ ] Multiple test miners running
- [ ] Community testing conducted
- [ ] Feedback incorporated
- [ ] Documentation complete

### Mainnet Launch
- [ ] TAO for registration secured
- [ ] Subnet registered on mainnet
- [ ] Hyperparameters configured
- [ ] Identity set
- [ ] Infrastructure ready
- [ ] Monitoring in place

### Post-Launch
- [ ] Initial miners/validators onboarded
- [ ] Community channels active
- [ ] Support system operational
- [ ] Regular updates scheduled
- [ ] Metrics being tracked

## Conclusion

Creating a successful Bittensor subnet requires:
- Thoughtful incentive design
- Strong technical implementation
- Active community engagement
- Continuous iteration

Start small, test thoroughly, and build a sustainable ecosystem that provides real value to the Bittensor network.
