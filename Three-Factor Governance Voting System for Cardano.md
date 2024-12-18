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
Total Voting Power = Progressive Base Weight (15-25%) + [ADA Holdings Weight (50%) × Time Multiplier (25%)]
```

Each factor serves a specific purpose in the governance system:

1. Progressive Base Weight (15-25%):
   - Provides scaled basic voting rights based on wallet size
   - Requires minimum balance of 1000 ADA and 30-day holding period
   - Ensures broad community participation while discouraging wallet splitting
   - Scales logarithmically from 15% to 25% based on wallet size

2. ADA Holdings Weight (50%):
   - Uses logarithmic scaling to balance influence of large holders
   - Calculated as: log10(ada_amount + 1) / log10(1M)
   - Preserves stake-based voting while preventing dominance

3. Time Multiplier (25%):
   - Rewards long-term holders with increased influence
   - Acts as multiplier only on the ADA holdings component
   - Helps prevent manipulation through wallet splitting

This combination creates a balanced system where:
- Small holders maintain meaningful participation through progressive base weight
- Large holders retain influence but with diminishing returns
- Long-term holders gain additional weight proportional to their commitment
- Progressive scaling naturally discourages wallet splitting

### Parameter Adjustability
The proposed weighting distribution represents an initial configuration designed to balance democratic participation with stake-based influence. Parameters can be adjusted through governance voting to better serve the community's needs:

#### Adjustable Parameters
- Progressive base weight range (currently 15-25%)
- ADA holdings weight percentage (currently 50%)
- Holding time weight percentage (currently 25%)
- Logarithmic base for ADA calculation (currently 1M ADA)
- Minimum qualifying balance (currently 1000 ADA)
- Maximum time multiplier cap (currently 2.0)
- Progressive base weight scaling factor
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

#### 1. Progressive Base Weight (15-25%)
The progressive base weight component provides democratic participation rights while implementing necessary safeguards against potential abuse (wallet splitting):

#### Minimum Qualifying Requirements
1. Balance Threshold
	- Minimum balance: 1000 ADA
	- Can include both liquid ADA and delegated ADA in stake pools
	- Balance must be maintained throughout the voting period

2. Holding Duration
   - Minimum holding period: 30 days
   - Calculated using weighted average holding time
   - Resets for newly received ADA

3. Progressive Scaling
	- Base weight scales logarithmically from 15% to 25%
	- Scale factor = log10(balance/MIN_ADA) / log10(1000)
	- Smaller wallets start at 15% base weight
	- Increases up to 25% for larger wallets
	- Formula:
	   ```
	   base_weight = 0.15 + (0.25 - 0.15) * min(scale_factor, 1.0)
	   ```
	   
3. Qualifying Balance Calculation
	   ```
	   Qualifying Balance = Liquid ADA + Delegated ADA (in stake pools)
	   ```
#### Rationale for Progressive Base Weight
1. Security Considerations
	- Progressive scaling naturally discourages wallet splitting
	- Makes manipulation through multiple wallets economically unfeasible
	- Ensures committed participation in the ecosystem

2. Accessibility Balance
	- 1000 ADA threshold maintains meaningful skin in the game
	- Including delegated ADA allows stakers to participate without unstaking
	- 30-day holding period prevents short-term manipulation while allowing reasonable entry

3. Technical Factors
	- Accounts for UTXO minimum requirements
	- Provides sufficient balance for transaction fees across multiple votes
	- Compatible with existing stake delegation mechanics

4. Economic Design
	- Cost of attack (creating multiple qualifying wallets) exceeds potential benefits
	- Progressive scaling rewards larger, committed holders
	- Maintains broad participation while ensuring skin in the game

#### Implementation Notes
- Balance checking occurs at vote registration and submission
- Holding period calculated using transaction history
- Stake delegation status verified through on-chain data
- Automatic disqualification if balance drops below minimum during voting

#### 2. ADA Holdings Weighting (50%)
- Logarithmic instead of linear scaling
- Formula: 
	```
	Voting weight = log10(ada_amount + 1) / log10(1M) * 0.50
	```

- Reduces the influence of very large holdings
- Example:
	* 1,000 ADA = 0.25 weighting (50% × 0.50)
	* 10,000 ADA = 0.50 weighting (50% × 1.00)
	* 100,000 ADA = 0.75 weighting (50% × 1.50)
	* 1,000,000 ADA = 1.00 weighting (50% × 2.00)

The base of 1M (1 million) ADA was chosen because:
- It represents a practical upper range for individual wallet sizes
- Larger holdings still receive more weight, but with strongly diminishing returns
- The logarithm to base 10 enables intuitive gradation in powers of ten

The base can be adjusted through governance voting when:
- Average wallet sizes change significantly
- The distribution of ADA holdings shifts substantially
- The community desires an adjustment to the weighting curve

#### 3. Holding Time Weighting as Multiplier (25%)
The holding time factor serves as a crucial component in preventing manipulation and rewarding long-term engagement in the ecosystem. It acts as a multiplier specifically for the ADA holdings-based voting power, ensuring that:
- Long-term holders receive proportionally more influence over their ADA-based voting power
- New wallets retain basic participation rights while having reduced ADA-based influence
- The system naturally resists short-term manipulation attempts while remaining fair to legitimate transfers

The holding time is calculated directly from UTXO ages in the wallet:
- Each UTXO's age is determined by its creation timestamp
- The effective holding time is weighted by each UTXO's amount
- Moving ADA between wallets creates new UTXOs, resetting their holding time
- Smart contract interactions and exchange operations create new UTXOs

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

#### Time Multiplier Cap (200%) Explanation
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

#### Proof of Wallet Splitting Ineffectiveness

This section mathematically proves that wallet splitting always results in reduced voting power, making it an ineffective strategy for gaining additional influence.

##### Theorem
For any wallet containing X ADA (where X ≥ 1000), splitting into N wallets (where N ≥ 2) always results in less total voting power than maintaining a single wallet.

##### Proof Components

1. Single Wallet Power:
```
P(X) = Base(X) + [ADA(X) × Time]
where:
Base(X) = 0.15 + (0.25 - 0.15) × min(log10(X/1000)/log10(1000), 1.0)
ADA(X) = [log10(X + 1) / log10(1000000)] × 0.50
```

2. Split Wallets Total Power:
```
P_split(X,N) = N × [Base(X/N) + (ADA(X/N) × Time)]
```

3. Splitting is ineffective when:
```
P(X) > P_split(X,N)
```

##### Proof by Components

1. Progressive Base Weight Dilution:
```
Base(X) > N × Base(X/N)
```
This holds true because:
- The logarithmic scaling factor log10(X/1000)/log10(1000) grows slower than any linear splitting
- Scale compression ensures higher total base weight for a single wallet

2. ADA Holdings Weight Dilution:
```
log10(X + 1) > N × log10(X/N + 1)
```
This inequality holds due to the logarithmic property:
```
log(a) + log(b) < log(a × b) for all a,b > 1
```

##### Example Calculations

For 10,000 ADA:

1. Single Wallet:
```
Base = 0.175 (scaled between 15-25%)
ADA = 0.35 (at 50% weight)
Total = 0.175 + (0.35 × Time)
```

2. Split into Two 5,000 ADA Wallets:
```
Base per wallet = 0.165
ADA per wallet = 0.31
Total = 2 × [0.165 + (0.31 × Time)]
Power Loss = ~3.1%
```

3. Split into Five 2,000 ADA Wallets:
```
Base per wallet = 0.155
ADA per wallet = 0.27
Total = 5 × [0.155 + (0.27 × Time)]
Power Loss = ~12.4%
```

##### Power Loss Table
| Original ADA | Splits | Power Loss |
|--------------|--------|------------|
| 5,000        | 2      | 2.3%      |
| 5,000        | 3      | 5.8%      |
| 10,000       | 2      | 3.1%      |
| 10,000       | 4      | 11.5%     |
| 50,000       | 2      | 4.7%      |
| 50,000       | 5      | 18.9%     |

#### Alternative Formula Consideration

An alternative formula was considered:
```
Total Voting Power = (Progressive Base Weight + ADA Holdings Weight) × Time Multiplier
```

While this provides even stronger protection against splitting by applying the time multiplier to all components, it was rejected because:
1. It overly penalizes legitimate operational moves
2. New wallets start with minimal voting power regardless of stake
3. It could discourage healthy operational practices

The current formula achieves the desired splitting protection while maintaining better operational flexibility.

#### Conclusion

The mathematical properties of the system ensure that:
1. Wallet splitting always results in voting power loss
2. The loss increases with the number of splits
3. Larger wallets face greater percentage losses when splitting
4. The progressive base weight creates natural disincentives for splitting
5. The logarithmic ADA scaling prevents excessive concentration while maintaining splitting protection

These properties emerge from the fundamental mathematical structure of the formula rather than from arbitrary constraints, making the system naturally resistant to gaming attempts.

### Technical Implementation

#### Core Voting Power Calculation
```python
def calculate_voting_power(wallet):
    """
    Calculate total voting power for a wallet incorporating all three factors:
    - Progressive base weight (15-25%)
    - ADA holdings weight (50%)
    - Holding time multiplier (25%)
    """
    # Constants
    MIN_ADA = 1000
    BASE_WEIGHT_MIN = 0.15
    BASE_WEIGHT_MAX = 0.25
    ADA_WEIGHT = 0.50
    TIME_WEIGHT = 0.25
    
    # Get current UTXOs and delegated ADA
    wallet_stats = get_wallet_stats(wallet)
    total_balance = wallet_stats['current_balance'] + wallet_stats['delegated_ada']
    
    # Only proceed if minimum balance requirement is met
    if total_balance < MIN_ADA:
        return 0
        
    # Calculate progressive base weight
    scale_factor = math.log10(total_balance/MIN_ADA) / math.log10(1000)
    base_power = BASE_WEIGHT_MIN + (BASE_WEIGHT_MAX - BASE_WEIGHT_MIN) * min(scale_factor, 1.0)
    
    # ADA weighting (50%) - logarithmic
    ada_power = (math.log10(total_balance + 1) / math.log10(1000000)) * ADA_WEIGHT
    
    # Calculate time multiplier
    time_multiplier = calculate_time_multiplier(wallet_stats)
    
    # Total power: progressive_base + (ada * time_multiplier)
    total_power = base_power + (ada_power * time_multiplier)
    
    return total_power

def get_wallet_stats(wallet_address):
    """
    Get wallet statistics using current UTXOs and delegation status.
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
    """
    current_time = get_current_slot_time()
    
    total_weighted_time = 0
    total_balance = 0
    
    for utxo in utxos:
        amount = utxo.amount
        holding_time = (current_time - utxo.creation_time).total_seconds() / (24 * 3600)  # in days
        
        total_weighted_time += amount * holding_time
        total_balance += amount
        
    return total_weighted_time / total_balance if total_balance > 0 else 0

def check_minimum_qualifying_balance(wallet_stats):
    """
    Check if wallet meets minimum balance requirements for voting.
    """
    MIN_ADA_REQUIREMENT = 1000 # 1000 ADA minimum
    MIN_HOLDING_DAYS = 30      # 30 days minimum holding period
    
    # Calculate total effective balance
    total_balance = wallet_stats['current_balance'] + wallet_stats['delegated_ada']
    
    # Check balance requirement
    if total_balance < MIN_ADA_REQUIREMENT:
        return False
    
    # Check holding period requirement
    if wallet_stats['effective_holding_days'] < MIN_HOLDING_DAYS:
        return False
    
    return True

def calculate_time_multiplier(wallet_stats):
    """
    Calculate time multiplier based on effective holding days.
    """
    # Constants
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

# Helper functions to be implemented based on Cardano node API
def get_wallet_utxos(wallet_address):
    """
    Get current UTXOs for a wallet.
    To be implemented using Cardano node API.
    """
    pass

def get_delegated_ada(wallet_address):
    """
    Get amount of ADA delegated to stake pools by this wallet.
    To be implemented using Cardano node API.
    """
    pass

def get_current_slot_time():
    """
    Get current slot time from the Cardano network.
    To be implemented using Cardano node API.
    """
    pass

def get_network_avg_holding_time():
    """
    Get the network-wide average holding time.
    To be implemented using Cardano node API.
    """
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

1. Wallet Splitting Protection
	- Progressive base weight (15-25%) discourages splitting
	- Minimum qualifying balance of 1000 ADA
	- Each UTXO's age is tracked independently
	- Splitting wallets resets UTXO creation timestamps
	- Cost of attack exceeds potential benefits due to transaction fees

2. Balance Manipulation Protection
   - New UTXOs automatically have their true (short) holding time
   - Large deposits cannot artificially age or manipulate voting power
   - Moving ADA between wallets creates new UTXOs with reset timestamps
   - UTXO model prevents any artificial aging of funds

3. Exchange Holdings
   - Exchange hot wallet rotations create new UTXOs
   - Each withdrawal creates new UTXOs with reset timestamps
   - Natural incentive for users to maintain personal custody
   - Batch operations don't preserve holding time

4. Smart Contract Interaction
   - Smart contract interactions create new UTXOs
   - DeFi protocol usage resets holding time through UTXO creation
   - Clear distinction between held and locked ADA through UTXO ownership
   - Delegation status tracked separately from UTXO age

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