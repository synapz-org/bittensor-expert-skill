# Bittensor Development Guide: Building Miners & Validators

## Overview

This guide covers practical development for Bittensor network participants:
- Setting up development environment
- Building miners
- Building validators
- Testing and deployment
- Best practices and optimization

## Development Environment Setup

### Prerequisites

**System Requirements**:
- Python 3.9 or higher
- pip and virtualenv
- Git
- 8GB+ RAM recommended
- Internet connection

**For GPU workloads**:
- CUDA-capable GPU
- CUDA toolkit installed
- PyTorch with CUDA support

### Installing Bittensor

**Method 1: Quick Install (Recommended for Users)**
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/opentensor/bittensor/master/scripts/install.sh)"
```

**Method 2: pip Install (Recommended for Developers)**
```bash
# Create virtual environment
python3 -m venv bt_venv
source bt_venv/bin/activate  # On Windows: bt_venv\Scripts\activate

# Install bittensor
pip install bittensor

# With optional dependencies
pip install bittensor[torch]  # For ML workloads
pip install bittensor[cubit]  # For CUDA support
```

**Method 3: From Source (For SDK Development)**
```bash
git clone https://github.com/opentensor/bittensor.git
cd bittensor
pip install -e .  # Editable install
```

### Installing btcli

```bash
# Via pip
pip install bittensor-cli

# Via Homebrew (macOS)
brew install btcli

# Verify
btcli --version
```

### Setting Up Local Subtensor

For local development and testing:

```bash
# Clone subtensor
git clone https://github.com/opentensor/subtensor.git
cd subtensor

# Build and run
./scripts/localnet.sh

# In another terminal, configure btcli
btcli config set --network local
```

## Building a Miner

### Miner Architecture

A miner consists of:
1. **Axon Server**: Receives requests from validators
2. **Forward Function**: Processes requests and generates outputs
3. **Wallet**: Manages hotkey for signing
4. **Subtensor Connection**: Communicates with blockchain

### Basic Miner Template

```python
# miner.py
import bittensor as bt
import argparse

def get_config():
    parser = argparse.ArgumentParser()
    parser.add_argument('--netuid', type=int, default=1)
    parser.add_argument('--wallet.name', type=str, default='default')
    parser.add_argument('--wallet.hotkey', type=str, default='default')
    parser.add_argument('--axon.port', type=int, default=8091)
    parser.add_argument('--network', type=str, default='finney')
    parser.add_argument('--logging.debug', action='store_true')

    bt.subtensor.add_args(parser)
    bt.wallet.add_args(parser)
    bt.axon.add_args(parser)

    return bt.config(parser)

def forward(synapse: bt.Synapse) -> bt.Synapse:
    """
    Process incoming requests from validators.

    Args:
        synapse: Request object from validator

    Returns:
        synapse: Response object with filled outputs
    """
    bt.logging.info(f"Received request: {synapse}")

    # TODO: Implement your mining logic here
    # Example: synapse.output = your_model(synapse.input)

    return synapse

def main(config):
    # Setup
    bt.logging(config=config)
    wallet = bt.wallet(config=config)
    subtensor = bt.subtensor(config=config)
    metagraph = subtensor.metagraph(config.netuid)

    # Create axon
    axon = bt.axon(wallet=wallet, config=config)

    # Attach forward function
    axon.attach(forward_fn=forward)

    # Serve axon
    bt.logging.info(f"Serving axon on network: {config.network} with netuid: {config.netuid}")
    axon.serve(netuid=config.netuid, subtensor=subtensor)

    # Start axon
    bt.logging.info(f"Starting axon server on port: {config.axon.port}")
    axon.start()

    # Keep miner running
    bt.logging.info("Miner is running. Press Ctrl+C to stop.")
    try:
        while True:
            # Optional: Periodic tasks like syncing metagraph
            metagraph.sync(subtensor=subtensor)
            time.sleep(60)
    except KeyboardInterrupt:
        bt.logging.info("Miner stopped by user")
        axon.stop()

if __name__ == "__main__":
    config = get_config()
    main(config)
```

### Advanced Miner Features

#### Multiple Forward Functions

```python
# Different endpoints for different request types
def forward_text(synapse: TextSynapse) -> TextSynapse:
    synapse.output = generate_text(synapse.input)
    return synapse

def forward_image(synapse: ImageSynapse) -> ImageSynapse:
    synapse.output = generate_image(synapse.prompt)
    return synapse

axon.attach(forward_fn=forward_text, forward_class=TextSynapse)
axon.attach(forward_fn=forward_image, forward_class=ImageSynapse)
```

#### Blacklisting/Whitelisting

```python
def blacklist(synapse: bt.Synapse) -> tuple[bool, str]:
    """
    Decide whether to process request.

    Returns:
        (should_blacklist, reason)
    """
    # Check stake
    if metagraph.S[synapse.dendrite.hotkey] < MIN_STAKE:
        return True, "Insufficient stake"

    # Rate limiting
    if rate_limiter.is_limited(synapse.dendrite.hotkey):
        return True, "Rate limited"

    return False, ""

axon.attach(forward_fn=forward, blacklist_fn=blacklist)
```

#### Priority Function

```python
def priority(synapse: bt.Synapse) -> float:
    """
    Assign priority to requests.
    Higher priority requests processed first.

    Returns:
        Priority score (float)
    """
    # Prioritize by validator stake
    return float(metagraph.S[synapse.dendrite.hotkey])

axon.attach(forward_fn=forward, priority_fn=priority)
```

## Building a Validator

### Validator Architecture

A validator consists of:
1. **Dendrite Client**: Queries miners
2. **Scoring Logic**: Evaluates miner responses
3. **Weight Setting**: Commits assessments to blockchain
4. **Wallet**: Manages hotkey and stake

### Basic Validator Template

```python
# validator.py
import bittensor as bt
import argparse
import time
import torch

def get_config():
    parser = argparse.ArgumentParser()
    parser.add_argument('--netuid', type=int, default=1)
    parser.add_argument('--wallet.name', type=str, default='default')
    parser.add_argument('--wallet.hotkey', type=str, default='default')
    parser.add_argument('--network', type=str, default='finney')
    parser.add_argument('--epoch_length', type=int, default=100)

    bt.subtensor.add_args(parser)
    bt.wallet.add_args(parser)

    return bt.config(parser)

def generate_query():
    """
    Generate request for miners.

    Returns:
        Synapse object with query data
    """
    # TODO: Implement your query generation logic
    return bt.Synapse()

def score_responses(responses):
    """
    Evaluate miner responses.

    Args:
        responses: List of synapse responses from miners

    Returns:
        scores: Tensor of scores [n_miners]
    """
    scores = []
    for response in responses:
        # TODO: Implement your scoring logic
        score = calculate_quality(response)
        scores.append(score)

    return torch.tensor(scores)

def main(config):
    # Setup
    bt.logging(config=config)
    wallet = bt.wallet(config=config)
    subtensor = bt.subtensor(config=config)
    dendrite = bt.dendrite(wallet=wallet)
    metagraph = subtensor.metagraph(config.netuid)

    bt.logging.info(f"Validator starting on network: {config.network}")

    step = 0

    while True:
        try:
            # Sync metagraph
            metagraph.sync(subtensor=subtensor)

            # Generate query
            synapse = generate_query()

            # Query all miners
            bt.logging.info(f"Querying {len(metagraph.axons)} miners")
            responses = dendrite.query(
                axons=metagraph.axons,
                synapse=synapse,
                timeout=12
            )

            # Score responses
            scores = score_responses(responses)
            bt.logging.info(f"Scores: {scores}")

            # Convert scores to weights
            weights = torch.nn.functional.normalize(scores, p=1, dim=0)

            # Set weights on chain
            if step % config.epoch_length == 0:
                bt.logging.info("Setting weights on chain")
                subtensor.set_weights(
                    wallet=wallet,
                    netuid=config.netuid,
                    uids=metagraph.uids,
                    weights=weights,
                    wait_for_inclusion=True
                )

            step += 1
            time.sleep(12)  # Wait before next query

        except KeyboardInterrupt:
            bt.logging.info("Validator stopped by user")
            break
        except Exception as e:
            bt.logging.error(f"Error in validation loop: {e}")
            time.sleep(60)

if __name__ == "__main__":
    config = get_config()
    main(config)
```

### Advanced Validator Features

#### Parallel Querying

```python
import asyncio

async def query_miner(dendrite, axon, synapse):
    """Query single miner asynchronously"""
    try:
        response = await dendrite.query_async(axon, synapse, timeout=12)
        return response
    except Exception as e:
        bt.logging.error(f"Query failed for {axon.hotkey}: {e}")
        return None

async def query_all_miners(dendrite, axons, synapse):
    """Query all miners in parallel"""
    tasks = [query_miner(dendrite, axon, synapse) for axon in axons]
    return await asyncio.gather(*tasks)

# Use in main loop
responses = asyncio.run(query_all_miners(dendrite, metagraph.axons, synapse))
```

#### Weighted Scoring

```python
def calculate_weights(scores, metagraph):
    """
    Convert scores to weights with considerations.

    Args:
        scores: Raw quality scores
        metagraph: Current network state

    Returns:
        weights: Normalized weight distribution
    """
    # Remove low scores
    scores = torch.where(scores < THRESHOLD, torch.zeros_like(scores), scores)

    # Apply non-linearity (reward top performers)
    scores = torch.pow(scores, 2)

    # Normalize to sum to 1
    weights = torch.nn.functional.normalize(scores, p=1, dim=0)

    # Enforce max weight limit
    weights = torch.clamp(weights, max=MAX_WEIGHT)
    weights = weights / weights.sum()

    return weights
```

#### Moving Average Weights

```python
class WeightTracker:
    def __init__(self, alpha=0.9):
        self.alpha = alpha
        self.weights = None

    def update(self, new_weights):
        """
        Exponential moving average of weights.

        Args:
            new_weights: Current epoch weights

        Returns:
            Smoothed weights
        """
        if self.weights is None:
            self.weights = new_weights
        else:
            self.weights = self.alpha * self.weights + (1 - self.alpha) * new_weights

        return self.weights

# Usage
weight_tracker = WeightTracker()
smoothed_weights = weight_tracker.update(current_weights)
```

## Custom Protocol Definition

### Defining Synapse Classes

```python
# protocol.py
import bittensor as bt
from pydantic import Field
from typing import Optional, List

class TextGenerationSynapse(bt.Synapse):
    """Protocol for text generation tasks"""

    # Input fields
    prompt: str = Field(..., description="Text prompt for generation")
    max_tokens: int = Field(100, description="Maximum tokens to generate")
    temperature: float = Field(0.7, description="Sampling temperature")

    # Output fields
    generated_text: Optional[str] = Field(None, description="Generated text output")

    # Metadata
    model_name: Optional[str] = Field(None, description="Model used by miner")

    class Config:
        validate_assignment = True

class ImageGenerationSynapse(bt.Synapse):
    """Protocol for image generation tasks"""

    prompt: str = Field(..., description="Image description")
    size: str = Field("512x512", description="Image dimensions")

    # Output as base64 encoded image
    image_data: Optional[str] = Field(None, description="Base64 encoded image")

    class Config:
        validate_assignment = True
```

### Using Custom Protocols

**In Miner**:
```python
from protocol import TextGenerationSynapse

def forward(synapse: TextGenerationSynapse) -> TextGenerationSynapse:
    synapse.generated_text = your_model.generate(
        synapse.prompt,
        max_tokens=synapse.max_tokens,
        temperature=synapse.temperature
    )
    synapse.model_name = "my-model-v1"
    return synapse

axon.attach(forward_fn=forward, forward_class=TextGenerationSynapse)
```

**In Validator**:
```python
from protocol import TextGenerationSynapse

synapse = TextGenerationSynapse(
    prompt="Write a poem about AI",
    max_tokens=200,
    temperature=0.8
)

responses = dendrite.query(metagraph.axons, synapse)
```

## Testing

### Local Testing

**Setup Local Network**:
```bash
# Terminal 1: Run local subtensor
cd subtensor
./scripts/localnet.sh

# Terminal 2: Register test neurons
btcli config set --network local
btcli wallet create --wallet-name alice
btcli wallet create --wallet-name bob
btcli subnets register --netuid 1 --wallet-name alice
btcli subnets register --netuid 1 --wallet-name bob
```

**Run Test Miner**:
```bash
python miner.py --network local --wallet.name alice --netuid 1
```

**Run Test Validator**:
```bash
python validator.py --network local --wallet.name bob --netuid 1
```

### Unit Testing

```python
# test_miner.py
import pytest
import bittensor as bt
from miner import forward

def test_forward_function():
    """Test miner forward logic"""
    synapse = bt.Synapse()
    synapse.input = "test input"

    result = forward(synapse)

    assert result.output is not None
    assert len(result.output) > 0

def test_scoring():
    """Test validator scoring"""
    from validator import score_responses

    responses = [
        MockResponse(quality=0.9),
        MockResponse(quality=0.5),
        MockResponse(quality=0.1)
    ]

    scores = score_responses(responses)

    assert scores[0] > scores[1] > scores[2]
```

### Integration Testing

```python
# test_integration.py
import bittensor as bt

def test_miner_validator_interaction():
    """Test full query-response cycle"""
    # Setup
    wallet = bt.wallet()
    subtensor = bt.subtensor(network='local')
    dendrite = bt.dendrite(wallet=wallet)

    # Start miner (in background or separate process)
    # ...

    # Query miner
    synapse = MyCustomSynapse(input="test")
    response = dendrite.query(axon, synapse)

    # Verify response
    assert response.output is not None
```

## Deployment

### Testnet Deployment

```bash
# 1. Create testnet wallet
btcli wallet create --wallet-name testnet_miner
btcli config set --network test

# 2. Get test TAO from faucet
# (Follow testnet documentation)

# 3. Register on subnet
btcli subnets register --netuid 1 --wallet-name testnet_miner

# 4. Run your miner/validator
python miner.py --network test --wallet.name testnet_miner --netuid 1
```

### Mainnet Deployment

**Pre-Deployment Checklist**:
- [ ] Tested on testnet
- [ ] Code reviewed and audited
- [ ] Monitoring in place
- [ ] Backup systems ready
- [ ] Documentation complete

**Deployment**:
```bash
# 1. Create mainnet wallet
btcli wallet create --wallet-name prod_miner

# 2. Fund wallet with TAO

# 3. Register on subnet
btcli config set --network finney
btcli subnets register --netuid <target_netuid> --wallet-name prod_miner

# 4. Deploy with process manager
pm2 start miner.py --name bittensor-miner -- \
    --network finney \
    --wallet.name prod_miner \
    --netuid <target_netuid>
```

## Best Practices

### Performance Optimization

**Miner**:
- Cache model loaded in memory
- Use GPU when available
- Implement request batching
- Optimize inference speed
- Use async where possible

**Validator**:
- Parallel miner queries
- Efficient scoring algorithms
- Cache metagraph when possible
- Batch weight updates
- Monitor query timeouts

### Resource Management

```python
import torch
import gc

def cleanup_resources():
    """Periodic resource cleanup"""
    gc.collect()
    if torch.cuda.is_available():
        torch.cuda.empty_cache()

# Call periodically
cleanup_resources()
```

### Error Handling

```python
import traceback

def safe_forward(synapse):
    """Forward with error handling"""
    try:
        return forward(synapse)
    except Exception as e:
        bt.logging.error(f"Forward error: {e}")
        bt.logging.debug(traceback.format_exc())
        return synapse  # Return empty/error response

axon.attach(forward_fn=safe_forward)
```

### Logging

```python
# Comprehensive logging
bt.logging.info("Normal operations")
bt.logging.warning("Potential issues")
bt.logging.error("Errors encountered")
bt.logging.debug("Detailed debugging info")

# Log to file
bt.logging.enable_logging(
    logging_dir='/var/log/bittensor',
    debug=True,
    trace=True
)
```

### Security

**Wallet Security**:
- Never expose mnemonics
- Use hotkeys for operations
- Keep coldkeys offline
- Regular backups

**Input Validation**:
```python
def validate_input(synapse):
    """Validate incoming requests"""
    if len(synapse.input) > MAX_LENGTH:
        raise ValueError("Input too long")

    if not is_safe_input(synapse.input):
        raise ValueError("Unsafe input detected")

    return True
```

**Rate Limiting**:
```python
from collections import defaultdict
import time

class RateLimiter:
    def __init__(self, max_requests=10, window=60):
        self.requests = defaultdict(list)
        self.max_requests = max_requests
        self.window = window

    def is_allowed(self, hotkey):
        now = time.time()
        self.requests[hotkey] = [
            t for t in self.requests[hotkey]
            if now - t < self.window
        ]

        if len(self.requests[hotkey]) >= self.max_requests:
            return False

        self.requests[hotkey].append(now)
        return True
```

## Monitoring & Maintenance

### Key Metrics

**Miner Metrics**:
- Requests received per hour
- Response time (p50, p95, p99)
- Error rate
- Resource usage (CPU, GPU, RAM)
- Network bandwidth

**Validator Metrics**:
- Queries sent per hour
- Response success rate
- Weight setting frequency
- Stake amount
- Emission received

### Monitoring Tools

```python
import wandb

# Initialize wandb for monitoring
wandb.init(project="bittensor-miner", name="miner-1")

# Log metrics
wandb.log({
    "requests_per_hour": requests_count,
    "avg_response_time": avg_time,
    "error_rate": error_rate
})
```

### Automated Restart

```bash
# Using PM2
pm2 start miner.py --name bittensor-miner --max-restarts 10

# Using systemd
# Create /etc/systemd/system/bittensor-miner.service
```

### Updates and Upgrades

```python
# Version checking
CURRENT_VERSION = "1.0.0"

def check_updates():
    """Check for subnet updates"""
    latest_version = fetch_latest_version()
    if latest_version > CURRENT_VERSION:
        bt.logging.warning(f"Update available: {latest_version}")
        return True
    return False

# Run periodically
if check_updates():
    # Notify or auto-update
    pass
```

## Common Issues & Solutions

### Issue: "AxonServeError"
**Cause**: Port already in use or firewall blocking
**Solution**: Change port or configure firewall
```bash
python miner.py --axon.port 8092
```

### Issue: "Insufficient Stake"
**Cause**: Validator doesn't have enough stake
**Solution**: Add more stake
```bash
btcli stake add --amount 100 --netuid 1
```

### Issue: "Query Timeout"
**Cause**: Miner too slow or network issues
**Solution**: Optimize forward function or increase timeout
```python
responses = dendrite.query(axons, synapse, timeout=30)
```

### Issue: "Weight Setting Failed"
**Cause**: Rate limit or insufficient permissions
**Solution**: Check rate limit parameters
```bash
btcli subnets hyperparameters --netuid 1
```

## Resources

### Official Documentation
- Bittensor Docs: https://docs.learnbittensor.org
- API Reference: https://docs.bittensor.com/python-api
- GitHub: https://github.com/opentensor

### Community
- Discord: Official Bittensor Discord
- GitHub Discussions: For technical questions
- Example Subnets: Study existing implementations

### Tools
- btcli: Command-line interface
- Bittensor SDK: Python library
- Monitoring: wandb, prometheus, grafana

## Conclusion

Building on Bittensor requires:
- Understanding of incentive mechanisms
- Strong Python development skills
- Testing discipline
- Monitoring and maintenance

Start with local testing, move to testnet, then carefully deploy to mainnet with proper monitoring and support systems in place.
