# Bittensor Expert - Claude Code Skill

A comprehensive Claude Code skill that provides deep, authoritative knowledge of the Bittensor decentralized AI network.

## Overview

The **Bittensor Expert** skill transforms Claude into a Bittensor specialist, capable of:
- Explaining core concepts (subnets, metagraph, Yuma Consensus, TAO token)
- Guiding subnet development from design to deployment
- Providing detailed btcli command assistance
- Helping developers build miners and validators
- Troubleshooting network issues
- Answering whitepaper and architecture questions

## Features

### Comprehensive Knowledge Base

This skill includes extensive reference documentation covering:

- **Core Concepts** - Fundamental Bittensor concepts, roles, economics, and mechanisms
- **Whitepaper Summary** - Deep dive into Yuma Consensus, peer ranking, token economics, and architectural components
- **Architecture** - Subtensor blockchain, Bittensor SDK, subnet structure, and network communication
- **btcli Guide** - Complete command-line reference with examples for all operations
- **Subnet Guide** - Full lifecycle from design to deployment and management
- **Development Guide** - Practical templates and best practices for building miners and validators

### Use Cases

**Conceptual Questions**:
```
"Explain how Yuma Consensus works"
"What's the difference between a miner and a validator?"
"How does the metagraph structure work?"
```

**Development Tasks**:
```
"Help me build a miner for subnet 1"
"Guide me through creating a custom Synapse protocol"
"How do I optimize validator performance?"
```

**btcli Operations**:
```
"How do I register on a subnet?"
"Show me how to stake TAO to a validator"
"What's the command to create a subnet?"
```

**Subnet Creation**:
```
"I want to create a subnet for text generation"
"How do I configure hyperparameters?"
"Guide me through testnet deployment"
```

## Installation

### For Claude Code CLI

1. Download the latest release (`bittensor-expert.zip`)
2. Extract the skill to your Claude skills directory:
   ```bash
   unzip bittensor-expert.zip -d ~/.claude/skills/
   ```
3. The skill will be automatically available in Claude Code

### Manual Installation

```bash
# Clone the repository
git clone https://github.com/synapz-org/bittensor-expert-skill.git

# Copy to Claude skills directory
cp -r bittensor-expert-skill ~/.claude/skills/bittensor-expert
```

## Usage

The skill automatically activates when you ask Bittensor-related questions in Claude Code:

```
User: How does Yuma Consensus work?
Claude: [Uses bittensor-expert skill to provide detailed explanation]
```

No explicit invocation needed - Claude recognizes Bittensor topics and uses the skill automatically.

## Documentation Structure

```
bittensor-expert/
├── SKILL.md                          # Main skill instructions
└── references/                       # Comprehensive documentation
    ├── core-concepts.md              # Fundamental concepts
    ├── whitepaper-summary.md         # Whitepaper deep dive
    ├── architecture.md               # System architecture
    ├── btcli-guide.md                # Complete CLI reference
    ├── subnet-guide.md               # Subnet lifecycle
    └── development-guide.md          # Practical development
```

## What Makes This Skill Unique

### 1. Comprehensive Coverage
- Based on official Bittensor documentation
- Covers whitepaper, architecture, and practical development
- Includes real-world examples and workflows

### 2. Practical Focus
- Complete btcli command reference with examples
- Ready-to-use miner and validator templates
- Step-by-step subnet creation guide

### 3. Progressive Learning
- Starts with core concepts
- Builds to advanced topics
- Links related concepts for deeper understanding

### 4. Safety-First
- Emphasizes testing on testnet before mainnet
- Highlights security best practices
- Warns about irreversible operations

## Knowledge Base Contents

### Core Concepts (core-concepts.md)
- Bittensor architecture and principles
- Roles: miners, validators, subnet creators, stakers
- TAO token economics and emissions
- Metagraph structure and network state
- Key terminology and definitions

### Whitepaper Summary (whitepaper-summary.md)
- Yuma Consensus mechanism explained
- Peer-to-peer intelligence ranking
- Token economics: stake, inflation, bonds
- Collusion resistance and security
- Architectural innovations

### Architecture (architecture.md)
- Subtensor blockchain layer
- Bittensor SDK components
- Subnet structure and communication
- Consensus and incentive mechanisms
- Deployment environments

### btcli Guide (btcli-guide.md)
- Complete command reference
- Wallet management
- Staking operations
- Subnet operations
- Governance and administration
- Common workflows and troubleshooting

### Subnet Guide (subnet-guide.md)
- Subnet creation process
- Incentive mechanism design
- Local testing, testnet, mainnet deployment
- Hyperparameter configuration
- Economic considerations
- Best practices and anti-gaming measures

### Development Guide (development-guide.md)
- Environment setup
- Basic and advanced miner templates
- Validator implementation patterns
- Custom protocol (Synapse) definition
- Testing strategies
- Deployment procedures
- Performance optimization

## Examples

### Example 1: Understanding Yuma Consensus
```
User: Explain how Yuma Consensus prevents collusion

Claude: [Reads whitepaper-summary.md]

Yuma Consensus prevents collusion through super-linear reward scaling:

1. **Consensus Rewards**: Validators who agree with the majority (>50% stake)
   receive exponentially higher rewards than linear distribution would provide.

2. **Economic Rationality**: Even if dishonest actors form a minority coalition,
   honest majority participants earn more per capita, making collusion unprofitable.

3. **Byzantine Tolerance**: The system remains secure with up to 50% Byzantine
   actors due to the exponential consensus incentive.

[Continues with detailed explanation and examples]
```

### Example 2: Building a Miner
```
User: Help me build a miner for subnet 1

Claude: [Reads development-guide.md]

I'll help you build a miner for subnet 1. Here's the basic structure:

[Provides complete miner template code]
[Explains each component]
[Guides through testing and deployment]
```

### Example 3: btcli Command
```
User: How do I stake TAO?

Claude: [Reads btcli-guide.md]

To stake TAO, use the `btcli stake add` command:

```bash
# Basic staking
btcli stake add --amount 100 --netuid 1

# With safety checks
btcli stake add --amount 100 --netuid 1 --safe --tolerance 0.1
```

[Provides complete explanation with flags and safety warnings]
```

## Contributing

Contributions are welcome! This skill is based on official Bittensor documentation as of January 2025.

To contribute:
1. Fork the repository
2. Create a feature branch
3. Make your improvements
4. Submit a pull request

Areas for contribution:
- Updating documentation with latest changes
- Adding more examples
- Improving explanations
- Fixing errors or outdated information

## Roadmap

- [ ] Regular updates with latest Bittensor releases
- [ ] Additional code examples and templates
- [ ] Video tutorials and guides integration
- [ ] Community-contributed FAQ section
- [ ] Specific subnet implementation examples

## Credits

### Documentation Sources
- [Bittensor Official Documentation](https://docs.learnbittensor.org)
- [Bittensor Whitepaper](https://bittensor.com/whitepaper)
- [Bittensor GitHub](https://github.com/opentensor)

### Skill Framework
- Built using [Claude Code Skill Creator](https://github.com/anthropics/claude-code)

## License

MIT License

Copyright (c) 2025

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Support

- **Issues**: [GitHub Issues](https://github.com/synapz-org/bittensor-expert-skill/issues)
- **Discussions**: [GitHub Discussions](https://github.com/synapz-org/bittensor-expert-skill/discussions)
- **Bittensor Discord**: For general Bittensor questions
- **Claude Code**: For Claude Code-specific questions

## Changelog

### v1.0.0 (2025-01-02)
- Initial release
- Comprehensive Bittensor knowledge base
- Complete btcli command reference
- Miner and validator development guides
- Subnet creation and management guide
- Whitepaper and architecture documentation

## Disclaimer

This skill provides information based on official Bittensor documentation. Always:
- Verify critical operations with official documentation
- Test on testnet before mainnet deployment
- Secure your wallet mnemonics and keys
- This is not financial advice

The Bittensor network and its features are under active development. Some information may become outdated. Always check the official documentation for the latest information.

---

Made with ❤️ for the Bittensor community
