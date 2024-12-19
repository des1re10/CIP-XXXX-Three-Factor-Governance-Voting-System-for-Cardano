# CIP-XXXX: Three-Factor Governance Voting System for Cardano
```yaml
CIP: XXXX
Title: Three-Factor Governance Voting System
Author: M.Eng. Robert Ledwig
Status: Draft
Category: Governance
Created: 2024-12-15
Discussions-to: https://github.com/cardano-foundation/CIPs/discussions/948
License: CC-BY-4.0
```

## Abstract
This CIP proposes a revised voting system that balances the excessive vote concentration among large ADA holders. The system introduces two new factors alongside ADA holdings to enable more balanced governance. By implementing a more democratic and equitable voting structure, this proposal aims to enhance long-term trust in the Cardano ecosystem, maintain sustainable decentralized governance, and preserve ADA's value through increased institutional confidence and broader community participation.

## Current System and Problem
The current system is based solely on ADA holdings:
- Voting weight = ADA amount
- 1 ADA = 1 vote
- Linear scaling without limits

This leads to:
- Disproportionate influence of large ADA holders
- Potential dominance of institutional actors
- Underrepresentation of the broader community

## Proposed Solution: Three-Factor System
This proposal introduces a balanced governance approach combining three weighted factors: a base voting weight providing democratic participation rights, a logarithmic ADA holdings factor ensuring stake-based influence while preventing dominance, and a holding time multiplier primarily serving as an anti-manipulation mechanism against wallet splitting by large entities. Together, these factors create a system that is both more equitable and resistant to common attack vectors in token-based governance.

### Overview of Voting Power Calculation

The total voting power of a wallet is calculated using the following formula:

```
Total Voting Power = (Base Weight + ADA Holdings Weight) × Time Multiplier
```

Each factor serves a specific purpose in the governance system:

1. Base Weight per Wallet (40%):
   - Provides basic democratic voting rights for qualifying wallets
   - Requires minimum balance of 1000 ADA and 30-day holding period
   - Ensures broad community participation
   - Proportionally distributed during wallet splits
   - Total base weight remains constant after splitting

2. ADA Holdings Weight (60%):
   - Uses logarithmic scaling to balance influence of large holders
   - Calculated as: log10(ada_amount + 1) / log10(10M)
   - Preserves stake-based voting while preventing dominance
   - Split wallets use original total for calculation
   - Distributed proportionally among split wallets

3. Time Multiplier (0.0x to 2.0x):
   - Multiplies the sum of Base Weight and ADA Holdings Weight
   - Rewards long-term holding
   - New wallets start with multiplier of 0.0x
   - Can be inherited through proven wallet splits
   - Tracked through UTXO history for verification
   - Linear growth up to 2.0x maximum over 2 years

This combination creates a balanced system where:
- Small holders maintain meaningful participation through proportional base weight
- Large holders retain influence but with diminishing returns
- Splitting wallets maintains exact total voting power through:
  * Proportional distribution of base weight
  * Equal distribution of ADA weight based on original amount
  * Inheritance of time multiplier
- UTXO history enables verification while preventing gaming
- System naturally encourages organic growth while allowing legitimate splits

### Parameter Adjustability
The proposed weighting distribution represents an initial configuration designed to balance democratic participation with stake-based influence. Parameters can be adjusted through governance voting to better serve the community's needs:

#### Adjustable Parameters
- Base weight per Wallet (currently 40%)
- ADA holdings weight percentage (currently 60%)
- Holding time multiplier (up to 2.0)

- Logarithmic base for ADA calculation (currently 10M ADA)
- Minimum qualifying balance (currently 1000 ADA)
- Maximum time multiplier cap (currently 2.0)
- Time-related parameters (depending on chosen implementation)

#### Adjustment Process
1. Parameters can be modified through governance voting
2. Changes require a qualified majority (exact threshold to be determined by community)
3. Implementation through parameter updates, not requiring hard forks
4. Regular review periods (e.g., yearly) to assess parameter effectiveness

#### Considerations for Adjustments
- Changes should maintain a balance between small and large holders
- Total of all weight percentages must equal 100%
- Parameters should be adjusted based on empirical network data
- Community feedback and participation metrics should guide changes
- Impact analysis required before major parameter updates

### Detailed Factor Descriptions

### 1. Base Weight per Wallet (40%)
The base weight component provides democratic participation rights that are proportionally distributed during wallet splits while preventing artificial wallet creation through UTXO history verification.

## Base Weight Formula
```
Base_Weight(wallet) = 
    0.40 if (balance ≥ 1000 ADA) AND 
           (holding_period ≥ 30 days) AND 
           (no_split_history)
    0.40/N if (balance ≥ 1000 ADA) AND 
            (holding_period ≥ 30 days) AND 
            (split_wallet) 
            where N = number of splits
    0.00 otherwise
```

## Core Requirements and Rules
- Minimum balance: 1000 ADA (includes both liquid and delegated ADA)
- Minimum holding period: 30 days
- Balance must be maintained throughout voting period
- New wallets receive 40% base weight if requirements are met
- Split wallets receive proportional share (40%/N) of base weight
- Total base weight remains constant after splitting

## UTXO History Handling
- New wallets: Start with full base weight, must build time multiplier from 0
- Split wallets: Inherit parent's time multiplier and receive proportional base weight
- Regular transfers: Create new UTXOs, reset time multiplier to 0
- Chain of splits: Base weight divided by total number of resulting wallets

## Security Features
- UTXO history provides cryptographic proof of wallet lineage
- Transaction history prevents manipulation through artificial aging
- Split detection through UTXO chain analysis ensures accurate weight distribution
- System maintains equal total voting power while preserving democratic participation
- Split wallets must prove origin through UTXO history to receive proportional weight

### 2. ADA Holdings Weighting (60%)
- Logarithmic instead of linear scaling
- Formula: 
	```
	Voting weight = log10(ada_amount + 1) / log10(10M) * 0.60
	```
- Reduces the influence of very large holdings
- Example:
	* 1,000 ADA = 0.257 weighting (60% × 0.429)
	* 10,000 ADA = 0.343 weighting (60% × 0.571)
	* 100,000 ADA = 0.429 weighting (60% × 0.714)
	* 1,000,000 ADA = 0.514 weighting (60% × 0.857)

The base of 10M (10 million) ADA was chosen because:
- It provides stronger diminishing returns than the previous 1M base
- It ensures wallet splitting always results in reduced voting power
- It maintains proportional influence while preventing excessive concentration
- The steeper logarithmic curve better balances large holdings

The base can be adjusted through governance voting when:
- Average wallet sizes change significantly
- The distribution of ADA holdings shifts substantially
- The community desires an adjustment to the weighting curve

### 3. Holding Time Multiplier
The time multiplier serves as the primary mechanism preventing wallet splitting while allowing legitimate operations. It multiplies the base weight per wallet and ADA holdings component of the voting power formula:

Key characteristics:
- New wallets start with a time multiplier of 0
- Time multiplier can be inherited during wallet splits if UTXO lineage is proven
- Split wallets receive proportional base weight and inherit time multiplier
- Maintains equal total voting power across splits

Core operational rules:
1. New Wallets
   - Start with time multiplier of 0
   - Must build up holding time from scratch
   - No inheritance from other wallets without proof

2. Wallet Splits
   - Can inherit parent wallet's time multiplier if UTXO lineage is proven
   - Base weight and ADA weight proportionally distributed
   - Only applies to transferred amounts
   - New incoming funds start at 0 multiplier

3. Regular Transfers
   - Reset time multiplier to 0
   - Must rebuild holding time from new UTXO creation
   - No inheritance without split proof

The system provides two options for calculating the holding time factor:

#### Option A: Fixed Time Period (2 Years)
This option uses a fixed maximum time period approach:
- Maximum holding period capped at 2 years
- Linear scaling from minimum holding period to maximum
- Each UTXO's holding time individually capped at 2 years
- Simple and predictable growth in voting power
- Formula:
```
time_multiplier = min(effective_days / (2 * 365), 2.0)
```

The fixed time period approach calculates effective holding time directly from UTXO ages up to a maximum of 2 years:

```python
def calculate_effective_holding_time(utxos):
    """
    Calculate effective holding time from UTXO ages with a fixed maximum period.
    """
    current_time = get_current_slot_time()
    MAX_HOLDING_TIME = 365 * 2  # 2 years in days
    
    total_weighted_time = 0
    total_balance = 0
    
    for utxo in utxos:
        amount = utxo.amount
        days_held = (current_time - utxo.creation_time).total_seconds() / (24 * 3600)
        # Cap individual UTXO holding time at 2 years
        capped_holding_time = min(days_held, MAX_HOLDING_TIME)
        
        total_weighted_time += amount * capped_holding_time
        total_balance += amount
        
    return total_weighted_time / total_balance if total_balance > 0 else 0

def calculate_time_multiplier_fixed(wallet_stats):
    """
    Calculate time multiplier using fixed period approach.
    """
    MIN_HOLDING_DAYS = 30
    MAX_HOLDING_TIME = 365 * 2  # 2 years in days
    MAX_MULTIPLIER = 2.0
    
    effective_days = wallet_stats['effective_holding_days']
    
    if effective_days < MIN_HOLDING_DAYS:
        return 0.0
    
    # Linear scaling between 0 and MAX_HOLDING_TIME
    relative_position = effective_days / MAX_HOLDING_TIME
    return min(relative_position * 2.0, MAX_MULTIPLIER)
```

#### Option B: Relative Time Calculation
This option uses network averages as a reference point:

- Compares individual wallet holding time to network average
- Dynamically adjusts based on network-wide holding patterns
- Maintains 2.0 maximum multiplier cap
- More adaptive to changing network conditions
- Formula:
```
time_multiplier = min(effective_days / network_avg_days, 2.0)
```

The relative time approach compares a wallet's holding time to the network average, with a maximum cap of 2.0 (200%) on the time multiplier:

```python
def calculate_relative_holding_time(wallet):
    tx_history = get_wallet_transactions(wallet)
    current_time = get_current_slot_time()
    
    # Calculate wallet's weighted holding time
    wallet_weighted_time = calculate_weighted_holding_time(tx_history, current_time)
    
    # Get network average holding time
    network_avg_time = get_network_avg_holding_time()
    
    # Calculate relative position and cap at 200% (2.0)
    relative_position = wallet_weighted_time / network_avg_time
    return min(relative_position, 2.0)  # Maximum 200% multiplier
```

#### Holding Time Multiplier Cap (200%) Explanation
The time multiplier uses a maximum cap of 2.0 (200%) to ensure fair voting power distribution:

```python
def calculate_time_multiplier(wallet_stats):
    MAX_MULTIPLIER = 2.0  # 200% maximum multiplier
    NETWORK_AVG_HOLDING_TIME = get_network_avg_holding_time()
    MIN_HOLDING_DAYS = 30  # 30 days minimum
    
    effective_days = wallet_stats['effective_holding_days']
    
    if effective_days < MIN_HOLDING_DAYS:
        return 0.0
    
    # Calculate relative position to network average
    relative_position = effective_days / NETWORK_AVG_HOLDING_TIME
    
    # Apply multiplier cap
    return min(relative_position, MAX_MULTIPLIER)
```

Example calculations with 100-day network average:
```
Wallet A (single UTXO, 100 days old):
- effective_days = 100
- relative_position = 100/100 = 1.0 (100%)
- time_multiplier = 1.0 (normal voting power)

Wallet B (single UTXO, 200 days old):
- effective_days = 200
- relative_position = 200/100 = 2.0 (200%)
- time_multiplier = 2.0 (maximum doubled power)

Wallet C (multiple UTXOs):
- UTXO1: 1000 ADA, 300 days old
- UTXO2: 2000 ADA, 50 days old
- effective_days = (1000*300 + 2000*50)/(1000 + 2000) = 133.33 days
- relative_position = 133.33/100 = 1.33 (133%)
- time_multiplier = 1.33

Wallet D (new UTXOs):
- All UTXOs less than 30 days old
- time_multiplier = 0.0 (minimum holding period not met)
```

#### Rationale for Implementation Choice
The proposal recommends implementing Option A (Fixed Time Period) initially due to:

- Greater predictability for participants
- Simpler implementation and verification
- Lower computational overhead
- Easier community understanding and adoption

However, the system design allows for future migration to Option B through governance voting if the community determines that dynamic adjustment would better serve the ecosystem's needs.

Both options maintain the core security properties:
- UTXO-based timestamp verification
- Reset on wallet transfers
- Protection against manipulation through wallet splitting
- Compatibility with smart contract interactions
- Support for stake delegation

Time Multiplier Cap of 200% was chosen because:
1. It provides meaningful incentive for long-term holding
2. Prevents excessive concentration of power in very old wallets
3. Creates clear upper bounds on time-based voting power
4. Maintains balanced influence across the network

This cap value of 2.0 is an adjustable parameter that can be modified through governance voting based on community feedback and network dynamics.

#### Rationale for Linear Time Scaling

The linear time scaling approach was chosen over alternatives like logarithmic or exponential scaling for several important reasons:

1. Uniform Time Cost
   - Linear scaling ensures that all wallets, regardless of their ADA holdings, must wait the same proportional time to reach their maximum multiplier
   - A wallet with 1,000 ADA and a wallet with 100,000 ADA both need to wait the same time period to double their voting power
   - This creates a fair and predictable growth rate that treats all holders equally

2. Manipulation Resistance
   - Since the time multiplier affects the ADA holdings component, linear scaling provides better protection against manipulation
   - Large holders cannot gain disproportionate voting power through rapid early time multiplier growth
   - The consistent time cost makes wallet-splitting attacks equally expensive in terms of time for all holder sizes

3. Predictability and Verification
   - Linear growth is easier for the community to understand and verify
   - The relationship between holding time and voting power multiplier is transparent
   - Simpler calculations reduce the risk of implementation errors or unexpected behavior

4. Fair Power Distribution
   - The steady growth rate prevents excessive early advantage for any holder size
   - New participants can clearly understand and plan for their voting power growth
   - The system maintains balance between short-term and long-term holders

Alternative approaches like logarithmic or exponential scaling were considered but rejected:
- Logarithmic scaling would provide faster early growth, potentially enabling quicker manipulation by large holders
- Exponential scaling would overly reward very long-term holders and create too strong a power concentration
- Both alternatives would make the system more complex without providing clear benefits over linear scaling

This linear approach, combined with the 2.0 (200%) cap, creates an optimal balance between rewarding long-term commitment and maintaining system security.

### Mathematical Proof of System Properties

#### Proof of Vote Power Conservation

This section mathematically proves that wallet splitting maintains equal total voting power, ensuring fair representation regardless of wallet structure.

##### Theorem
For any wallet W containing X ADA with time multiplier T, splitting into N wallets results in exactly equal total voting power as the original wallet.

The voting system consists of three core components that determine total voting power:

1. **Base Weight per Wallet (40%)**
   - Proportionally distributed among split wallets
   - Each split wallet receives BASE_WEIGHT / N
   - Provides democratic participation rights

2. **ADA Holdings Weight (60%)**
   - Calculated based on original total amount
   - Distributed equally among split wallets
   - Ensures proportional stake representation
   - Reduces extreme concentration of power

3. **Holding Time Multiplier (0.0x to 2.0x)**
   - New wallets start at 0.0x, must build up naturally
   - Split wallets inherit parent's time multiplier
   - Linear growth capped at 2.0x after 2 years

### Core Formula

For any wallet W, the voting power P is calculated as:

```
P(W) = (B(W) + A(W)) × T(W)

where:
B(W) = Base Weight = 
    0.40 if no inherited UTXO history (new/organic wallet)
    0.40/N if split wallet (N = number of splits)

A(W) = ADA Weight = 
    For original wallet:
        [log10(X + 1) / log10(10^7)] × 0.60
    For split wallet:
        [log10(original_X + 1) / log10(10^7)] × 0.60 / N
        where original_X is the pre-split total amount

T(W) = Time Multiplier =
    0    if new wallet without UTXO history
    min(days_held / (2 * 365), 2.0) if wallet has proven history
```

### System Behavior Analysis

#### 1. New Wallet Creation
When a completely new wallet is created:
```
- Base Weight: 0.40 (40%)
- Time Multiplier: 0.0x (starts at 0)
- Initial Power = (0.40 + A(W)) × 0 = 0
```

#### 2. Natural Growth
As a wallet ages naturally:
```
- Base Weight: Maintains 0.40
- Time Multiplier: Grows linearly to 2.0x
- Maximum Power = (0.40 + A(W)) × 2.0
```

#### 3. Wallet Splitting with UTXO History
When splitting a wallet with proven UTXO history:
```
- Base Weight: 0.40/N per wallet (N = number of splits)
- Time Multiplier: Inherited from parent wallet
- ADA Weight: Original amount's weight divided by N
```

### Mathematical Properties

#### 1. Time Multiplier Growth
The time multiplier grows linearly with holding duration:
```
T(t) = min(t / (2 * 365), 2.0)
where t = holding time in days
```

#### 2. ADA Weight Scaling
The ADA weight component scales logarithmically:
```
A(X) = [log10(X + 1) / log10(10^7)] × 0.60
where X = original total ADA amount
```

#### 3. Split Behavior
For a wallet W with amount X and time multiplier T, splitting into N equal parts:
```
Original Power:
P(W) = (0.40 + [log10(X + 1)/log10(10^7) × 0.60]) × T

Split Total Power:
P_split(W,N) = N × ((0.40/N + [log10(X + 1)/log10(10^7) × 0.60/N)) × T
```

#### Proof of Power Conservation
To prove that splitting maintains equal voting power, we show that P(W) = P_split(W,N) for all N ≥ 1:

```
P(W) = P_split(W,N)

(0.40 + [log10(X + 1)/log10(10^7) × 0.60]) × T = 
N × ((0.40/N + [log10(X + 1)/log10(10^7) × 0.60/N)) × T

Dividing both sides by T (since T > 0):

0.40 + [log10(X + 1)/log10(10^7) × 0.60] = 
N × (0.40/N + [log10(X + 1)/log10(10^7) × 0.60/N)

0.40 + 0.60 × log10(X + 1)/log10(10^7) = 
0.40 + 0.60 × log10(X + 1)/log10(10^7)

This equality holds for all N ≥ 1, proving power conservation.
```

### Numerical Examples

#### Example 1: 100,000 ADA wallet with 1.5 time multiplier

1. Original Wallet:
```
Base = 0.40
ADA = log10(100,000 + 1)/log10(10^7) × 0.60 = 0.432
Total = (0.40 + 0.432) × 1.5 = 1.248
```

2. Split into Two 50,000 ADA Wallets:
```
Base per wallet = 0.40/2 = 0.20
ADA per wallet = log10(100,000 + 1)/log10(10^7) × 0.60/2 = 0.216
Total per wallet = (0.20 + 0.216) × 1.5 = 0.624
Total for both = 2 × 0.624 = 1.248

Net difference = 1.248 - 1.248 = 0 (exactly equal)
```

#### Example 2: New Wallet Growth (100,000 ADA)

```
At t=0:
Power = (0.40 + 0.432) × 0 = 0

At t=365 days (1 year):
Power = (0.40 + 0.432) × 1.0 = 0.832

At t=730 days (2 years):
Power = (0.40 + 0.432) × 2.0 = 1.664
```

### Security Properties

1. **Historical Verification**
   - All time multiplier inheritance requires UTXO proof
   - Chain of custody must be cryptographically verifiable
   - No possibility of fabricating historical ownership

2. **Growth Incentives**
   - New wallets must build time multiplier naturally
   - Base weight distribution maintains democratic rights
   - Clear path to maximum voting power through holding

3. **Split Behavior**
   - Splits with proven history preserve time multiplier
   - Base weight and ADA weight distributed proportionally
   - Total voting power remains exactly equal after splitting

### Parameter Adjustability

The system parameters can be modified through governance:

1. **Base Weight per Wallet** (currently 0.40)
   - Range: [0.35, 0.45]
   - Adjusts importance of democratic participation

2. **ADA Weight** (currently 0.60)
   - Range: [0.55, 0.65]
   - Balances stake-based influence

3. **Holding Time Multiplier Cap** (currently 2.0)
   - Range: [1.5, 2.5]
   - Controls maximum time-based power

4. **Logarithmic Base** (currently 10^7)
   - Range: [10^6, 10^8]
   - Adjusts curve steepness
   - Higher values create stronger diminishing returns

### Conclusions

The system provides:
1. Verifiable UTXO-based history inheritance
2. Natural incentives for long-term holding
3. Perfect power conservation under splitting
4. Democratic participation through proportional base weight
5. Mathematically proven power conservation
6. Robust and predictable governance system

All properties emerge from the fundamental structure of the formula and are mathematically proven to hold across all valid input ranges.

### Technical Implementation

#### Core Voting Power Calculation
```python
def calculate_voting_power(wallet, is_split=False):
    """
    Calculate total voting power with split handling.
    Total Voting Power = (Base Weight + ADA Holdings Weight) × Time Multiplier
    
    Args:
        wallet: Wallet object containing UTXO and delegation information
        is_split: Boolean indicating if wallet is result of a split
    
    Returns:
        float: Total voting power
    """
    # Constants
    MIN_ADA = 1000
    BASE_WEIGHT = 0.40
    ADA_WEIGHT = 0.60 
    
    # Get wallet stats
    wallet_stats = get_wallet_stats(wallet)
    total_balance = wallet_stats['current_balance'] + wallet_stats['delegated_ada']
    
    if total_balance < MIN_ADA:
        return 0
    
    # Get split information
    source_wallet = verify_split_source(wallet) if is_split else None
    num_splits = get_number_of_splits(source_wallet) if source_wallet else 1
        
    # Base weight is proportionally distributed for split wallets
    base_power = BASE_WEIGHT / num_splits if is_split else BASE_WEIGHT
    
    # For split wallets, calculate ADA weight based on original total
    if is_split:
        original_balance = get_original_wallet_balance(source_wallet)
        ada_power = (math.log10(original_balance + 1) / math.log10(10**7)) * ADA_WEIGHT / num_splits
    else:
        ada_power = (math.log10(total_balance + 1) / math.log10(10**7)) * ADA_WEIGHT
    
    # Time multiplier inheritance for splits
    time_multiplier = inherit_time_multiplier(wallet) if is_split else calculate_time_multiplier(wallet_stats)
    
    # Total power = (base_power + ada_power) × time_multiplier
    total_power = (base_power + ada_power) * time_multiplier
    
    return total_power

def inherit_time_multiplier(wallet):
    """
    Inherit time multiplier from source wallet if split is proven
    
    Args:
        wallet: Wallet object to check for split inheritance
    
    Returns:
        float: Inherited time multiplier or 0 if not verified
    """
    source_wallet = verify_split_source(wallet)
    if source_wallet:
        return get_source_time_multiplier(source_wallet)
    return 0

def get_wallet_stats(wallet_address):
    """
    Get wallet statistics using current UTXOs and delegation status.
    
    Args:
        wallet_address: String representing wallet address
    
    Returns:
        dict: Dictionary containing wallet statistics
    """
    # Get current UTXOs in wallet
    utxos = get_wallet_utxos(wallet_address)
    
    # Get delegation info
    delegated_ada = get_delegated_ada(wallet_address)
    
    # Calculate total current balance
    current_balance = sum(utxo.amount for utxo in utxos)
    
    # Calculate effective holding time directly from UTXOs
    effective_holding_days = calculate_effective_holding_time(utxos)
    
    return {
        'current_balance': current_balance,
        'delegated_ada': delegated_ada,
        'effective_holding_days': effective_holding_days,
        'utxos': utxos  # Keep UTXO reference for detailed analysis if needed
    }

def calculate_effective_holding_time(utxos):
    """
    Calculate effective holding time directly from UTXO ages.
    Each UTXO's holding time is weighted by its amount.
    
    Args:
        utxos: List of UTXO objects
    
    Returns:
        float: Effective holding time in days
    """
    current_time = get_current_slot_time()
    
    total_weighted_time = 0
    total_balance = 0
    
    for utxo in utxos:
        amount = utxo.amount
        # For split UTXOs, use original creation time
        creation_time = get_original_utxo_time(utxo) if is_split_utxo(utxo) else utxo.creation_time
        holding_time = (current_time - creation_time).total_seconds() / (24 * 3600)  # in days
        
        total_weighted_time += amount * holding_time
        total_balance += amount
        
    return total_weighted_time / total_balance if total_balance > 0 else 0

def calculate_time_multiplier(wallet_stats):
    """
    Calculate time multiplier based on effective holding days.
    Linear scaling from 0 to 2.0x over 2 years.
    
    Args:
        wallet_stats: Dictionary containing wallet statistics
    
    Returns:
        float: Time multiplier between 0.0 and 2.0
    """
    MAX_MULTIPLIER = 2.0  # 200% maximum multiplier
    MAX_HOLDING_TIME = 365 * 2  # 2 years in days
    MIN_HOLDING_DAYS = 30  # 30 days minimum
    
    effective_days = wallet_stats['effective_holding_days']
    
    if effective_days < MIN_HOLDING_DAYS:
        return 0.0
    
    # Linear scaling between 0 and MAX_HOLDING_TIME
    relative_position = effective_days / MAX_HOLDING_TIME
    return min(relative_position * 2.0, MAX_MULTIPLIER)

# Helper functions to be implemented based on Cardano node API
def get_wallet_utxos(wallet_address):
    """Get current UTXOs for a wallet."""
    pass

def get_delegated_ada(wallet_address):
    """Get amount of ADA delegated to stake pools by this wallet."""
    pass

def get_current_slot_time():
    """Get current slot time from the Cardano network."""
    pass

def get_source_time_multiplier(wallet):
    """Get the time multiplier from the source wallet."""
    pass

def verify_split_source(wallet):
    """Verify if wallet is result of a legitimate split."""
    pass

def get_number_of_splits(source_wallet):
    """Get the number of split wallets from the original source."""
    pass

def get_original_wallet_balance(source_wallet):
    """Get the original balance before the split occurred."""
    pass

def get_original_utxo_time(utxo):
    """Get the creation time of the original UTXO before split."""
    pass

def is_split_utxo(utxo):
    """Check if a UTXO resulted from a split transaction."""
    pass
```

### Security Considerations

#### Transaction History Integrity
- All calculations are based on immutable UTXO history
- Holding time is precisely determined by UTXO creation timestamps
- Each ADA amount's age is cryptographically verifiable through the UTXO model
- Voting power calculation uses only current UTXOs, making manipulation impossible
- Network consensus ensures timestamp integrity

#### Attack Prevention

1. UTXO-Based Security Model
   - Time multiplier inheritance maintains voting power conservation:
      * Split wallets receive proportional base weight and ADA weight
      * Inheriting time multiplier requires cryptographic proof of wallet lineage
      * New incoming funds always start with 0 time multiplier
   - UTXO history provides immutable proof of holding time:
      * Each UTXO's age is tracked independently
      * Regular transfers create new UTXOs with reset timestamps
      * No way to artificially age funds or manipulate voting power
   - Minimum qualifying balance of 1000 ADA ensures meaningful participation

2. Exchange and Smart Contract Protections
   - Exchange operations:
      * Hot wallet rotations and withdrawals create new UTXOs
      * Batch operations don't preserve holding time
      * Users incentivized to maintain personal custody
   - Smart contract interactions:
      * DeFi protocol usage preserves clear ownership boundaries
      * Delegation status tracked separately from holding time
      * Locked ADA in smart contracts maintains clear lineage

### Technical Requirements
This change requires a hard fork of the Cardano network due to:
- Implementation of new on-chain data structures for voting weight calculation
- Modification of governance mechanisms
- Change of consensus rules for voting validation

## Transition Plan
1. Community Discussion (1 month)
   - Collection of feedback on parameter choices
   - Fine-tuning of weightings
   - Determination of minimum qualifying balance

2. Test Phase on Testnet (2 months)
   - Implementation of transaction history analysis
   - Collection of empirical data
   - Stress tests and security analyses
   - Performance optimization of calculations

3. Implementation with Next Hard Fork
   - Integration of community-chosen option
   - Coordinated network update
   - Begin tracking new voting weights
   - Historical transaction data already available for holding time calculation

## Copyright
This CIP is licensed under CC-BY-4.0.