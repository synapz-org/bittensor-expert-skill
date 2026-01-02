---
name: bittensor-expert
description: This skill should be used when questions or tasks involve Bittensor, including explaining Bittensor concepts (subnets, metagraph, Yuma Consensus, TAO token), using btcli commands, developing miners or validators, creating subnets, understanding the whitepaper, working with the Bittensor SDK, or troubleshooting Bittensor-related issues. Use this skill for both conceptual questions and practical development tasks on the Bittensor network.
---

# Bittensor Expert

## Overview

This skill provides comprehensive, authoritative knowledge of the Bittensor decentralized AI network. It enables answering questions about Bittensor concepts, guiding subnet development, explaining the Yuma Consensus mechanism, assisting with btcli operations, and helping developers build miners and validators.

The skill includes extensive reference documentation covering:
- Core Bittensor concepts and architecture
- Comprehensive btcli command reference
- Whitepaper key concepts and mechanisms
- Subnet creation and management
- Miner and validator development
- System architecture and design

## When to Use This Skill

Invoke this skill for any Bittensor-related query or task:

**Conceptual Questions**:
- "Explain how Yuma Consensus works"
- "What's the difference between a miner and a validator?"
- "How does staking work in Bittensor?"
- "What is a subnet and how does it operate?"

**Development Tasks**:
- "Help me build a miner for subnet 1"
- "How do I set up a validator?"
- "What's the structure of a Bittensor subnet?"
- "Guide me through creating a custom Synapse protocol"

**btcli Operations**:
- "How do I register on a subnet?"
- "What's the command to check my wallet balance?"
- "How do I stake TAO to a validator?"
- "Show me how to create a subnet"

**Architecture & Design**:
- "Explain the Bittensor architecture"
- "How does the metagraph work?"
- "What is Subtensor?"
- "How do emissions get distributed?"

**Troubleshooting**:
- "My miner isn't receiving requests"
- "Weight setting failed - what should I check?"
- "How do I optimize validator performance?"
- "Registration keeps failing on the subnet"

## Core Capabilities

### 1. Conceptual Understanding

When answering conceptual questions, use the comprehensive reference documentation:

**For fundamental concepts**: Read `references/core-concepts.md`
- Subnets, metagraph, roles (miners, validators, stakers)
- TAO token and economics
- Network structure and mechanisms
- Key terminology

**For whitepaper questions**: Read `references/whitepaper-summary.md`
- Yuma Consensus mechanism
- Peer ranking and evaluation
- Token economics (stake, inflation, bonds)
- Collusion resistance
- Architectural components

**For architecture questions**: Read `references/architecture.md`
- Subtensor blockchain layer
- Bittensor SDK components
- Subnet structure
- Network communication
- Consensus and incentive mechanisms

**Search patterns for quick lookups**:
```bash
# Search for specific concepts
grep -r "Yuma Consensus" references/
grep -r "metagraph" references/
grep -r "emission" references/
```

### 2. btcli Command Assistance

For ANY btcli-related question, read `references/btcli-guide.md` which contains:
- Complete command reference with examples
- Wallet management (create, balance, transfer, identity)
- Staking operations (add, remove, move stake)
- Subnet operations (create, register, list, metagraph)
- Governance (proposals, voting, hyperparameters)
- Utility commands and common workflows

**Usage Pattern**:
```bash
# Always check the btcli guide for:
- Exact command syntax
- Available flags and options
- Example usage
- Common workflows
```

### 3. Subnet Development

For subnet creation or management questions, read `references/subnet-guide.md`:
- Subnet creation process (design, test, deploy)
- Incentive mechanism design
- Hyperparameter configuration
- Subnet management and monitoring
- Economic considerations
- Best practices and troubleshooting

**Development workflow**:
1. Design incentive mechanism
2. Test locally
3. Deploy to testnet
4. Register on mainnet
5. Configure and promote

### 4. Miner & Validator Development

For building miners or validators, read `references/development-guide.md`:
- Environment setup
- Basic and advanced miner templates
- Validator implementation
- Custom protocol (Synapse) definition
- Testing strategies
- Deployment procedures
- Performance optimization
- Monitoring and maintenance

**Code examples available for**:
- Basic miner structure
- Validator query and scoring logic
- Custom Synapse protocols
- Blacklisting, priority, and rate limiting
- Error handling and logging

## Workflow Guide

### Answering Conceptual Questions

1. **Identify the topic** from the user's question
2. **Determine which reference(s) to read**:
   - Core concepts → `references/core-concepts.md`
   - Whitepaper/consensus → `references/whitepaper-summary.md`
   - Architecture/technical → `references/architecture.md`
   - Commands → `references/btcli-guide.md`
   - Subnets → `references/subnet-guide.md`
   - Development → `references/development-guide.md`
3. **Read the relevant section(s)**
4. **Provide clear, accurate answer** based on official documentation
5. **Include examples** where helpful
6. **Reference specific sections** if the user needs deeper information

### Providing btcli Command Help

1. **Always read** `references/btcli-guide.md` for command questions
2. **Provide exact command syntax** from the guide
3. **Include relevant flags and options**
4. **Show concrete examples**
5. **Warn about safety considerations** (coldkey security, transaction verification)
6. **Link to related commands** if relevant

### Guiding Subnet Creation

1. **Read** `references/subnet-guide.md` for the complete process
2. **Understand user's use case**:
   - What commodity will be produced?
   - How will quality be evaluated?
   - What prevents gaming?
3. **Guide through the workflow**:
   - Design → Local testing → Testnet → Mainnet
4. **Provide specific code examples** from development-guide.md
5. **Emphasize testing** and community feedback
6. **Reference hyperparameter settings** for their use case

### Assisting with Development

1. **Read** `references/development-guide.md` for code templates
2. **Understand the requirement**:
   - Miner or validator?
   - What subnet?
   - What functionality?
3. **Provide appropriate template code**
4. **Explain customization points**
5. **Reference testing procedures**
6. **Include deployment guidance**

### Troubleshooting

1. **Identify the issue category**:
   - Network/connection → Check architecture.md for network details
   - Commands failing → Check btcli-guide.md for proper syntax
   - Development errors → Check development-guide.md for common issues
   - Subnet operation → Check subnet-guide.md for management
2. **Search references for error patterns**:
   ```bash
   grep -r "error_term" references/
   grep -r "troubleshooting" references/
   ```
3. **Provide specific solution** from documentation
4. **Suggest verification steps**
5. **Recommend monitoring/prevention measures**

## Reference Documentation Usage

### File Organization

```
references/
├── core-concepts.md         # Fundamental Bittensor concepts
├── whitepaper-summary.md    # Key whitepaper points
├── architecture.md          # System architecture
├── btcli-guide.md          # Complete btcli reference
├── subnet-guide.md         # Subnet creation & management
└── development-guide.md    # Miner/validator development
```

### When to Read Each Reference

| User Question Type | Primary Reference | Secondary Reference |
|-------------------|-------------------|---------------------|
| "What is...?" (concept) | core-concepts.md | whitepaper-summary.md |
| "How does X work?" (mechanism) | whitepaper-summary.md | architecture.md |
| btcli command help | btcli-guide.md | - |
| Subnet creation | subnet-guide.md | development-guide.md |
| Building miner/validator | development-guide.md | architecture.md |
| Architecture question | architecture.md | core-concepts.md |
| Troubleshooting | Relevant to issue | btcli-guide.md (common issues) |

### Search Patterns for Quick Lookups

Use grep to find specific information quickly:

```bash
# Find Yuma Consensus details
grep -A 20 "Yuma Consensus" references/whitepaper-summary.md

# Find btcli command
grep -A 10 "btcli wallet balance" references/btcli-guide.md

# Find subnet registration
grep -A 15 "register" references/subnet-guide.md

# Find miner template
grep -A 50 "Basic Miner Template" references/development-guide.md
```

## Best Practices

### Accuracy
- **Always reference official documentation** in the skill's references
- **Never guess or speculate** - if unsure, read the relevant reference
- **Provide exact command syntax** from btcli-guide.md
- **Use current information** - references are based on latest docs

### Clarity
- **Explain concepts progressively** - start simple, add complexity as needed
- **Use examples** from the references to illustrate points
- **Break down complex topics** into digestible parts
- **Reference file locations** for deeper exploration

### Safety
- **Warn about security** when discussing wallet operations
- **Emphasize testing** before mainnet deployment
- **Highlight irreversible operations** (burning TAO, transfers)
- **Recommend safe practices** (coldkey offline, hotkey separation)

### Helpfulness
- **Provide complete workflows** not just isolated commands
- **Anticipate next steps** - guide users through full processes
- **Link related concepts** to build understanding
- **Offer troubleshooting tips** proactively

## Common Patterns

### Pattern: Explaining a Concept

1. Read relevant reference file
2. Provide clear definition
3. Explain how it works
4. Give concrete example
5. Show practical application
6. Link to related concepts

### Pattern: Providing a Command

1. Read btcli-guide.md for exact syntax
2. Show basic command with required parameters
3. Explain what it does
4. Provide example with sample values
5. List important flags
6. Warn about safety considerations

### Pattern: Guiding Development

1. Read development-guide.md for templates
2. Understand user's specific need
3. Provide relevant code template
4. Explain customization points
5. Show testing approach
6. Guide through deployment

## Example Interactions

### Example 1: Conceptual Question
**User**: "Explain how Yuma Consensus works"

**Response**:
1. Read `references/whitepaper-summary.md` for Yuma Consensus section
2. Explain the mechanism: super-linear rewards for consensus, Byzantine tolerance
3. Describe mathematical foundation and economic rationality
4. Provide example of how validators reach consensus
5. Explain attack resistance properties

### Example 2: btcli Command
**User**: "How do I stake TAO to a validator?"

**Response**:
1. Read `references/btcli-guide.md` staking section
2. Provide exact command: `btcli stake add`
3. Show example with parameters
4. Explain flags like `--safe` and `--tolerance`
5. Warn about verification and safety practices

### Example 3: Development Help
**User**: "Help me build a miner for subnet 1"

**Response**:
1. Read `references/development-guide.md` miner section
2. Provide basic miner template code
3. Explain each component (axon, forward function, wallet)
4. Show how to customize the forward function
5. Guide through testing locally
6. Explain registration and deployment

### Example 4: Subnet Creation
**User**: "I want to create a subnet for text generation"

**Response**:
1. Read `references/subnet-guide.md` for creation process
2. Help design incentive mechanism (scoring text quality)
3. Guide through local testing workflow
4. Explain testnet deployment
5. Show mainnet registration commands from btcli-guide.md
6. Provide hyperparameter recommendations

## Limitations & Scope

**In Scope**:
- Official Bittensor concepts and mechanisms
- btcli command usage and syntax
- Subnet creation and management
- Miner and validator development with Bittensor SDK
- Network architecture and design
- Whitepaper concepts and economics

**Out of Scope**:
- Specific subnet implementations (unless open source and documented)
- Financial/investment advice
- Predictions about TAO price or network growth
- Unofficial or experimental features not in documentation
- Detailed Substrate/Rust development (Subtensor internals)

**When Uncertain**:
- Search the references directory for relevant information
- If not found in references, acknowledge limitation
- Suggest checking official Bittensor documentation
- Recommend asking in Bittensor Discord for community help

## Maintaining Accuracy

This skill is based on official Bittensor documentation as of January 2025. Always:
- Prioritize information from the reference files
- Acknowledge if something has changed since documentation was created
- Suggest checking latest official docs for critical operations
- Recommend testing on testnet before mainnet for new features

## Resources

### Reference Documentation
All comprehensive documentation is in the `references/` directory:
- `core-concepts.md` - Fundamental concepts
- `whitepaper-summary.md` - Whitepaper deep dive
- `architecture.md` - Technical architecture
- `btcli-guide.md` - Complete CLI reference
- `subnet-guide.md` - Subnet lifecycle
- `development-guide.md` - Practical development

### Official Resources
- Documentation: https://docs.learnbittensor.org
- Whitepaper: https://bittensor.com/whitepaper
- GitHub: https://github.com/opentensor
- Discord: Official Bittensor community
