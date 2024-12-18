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
Total Voting Power = Base Weight (30%) + [ADA Holdings Weight (45%) × Time Multiplier (25%)]
```

Each factor serves a specific purpose in the governance system:

1. Base Weight (30%):
   - Provides equal basic voting rights to all qualifying wallets
   - Requires minimum balance of 500 ADA and 30-day holding period
   - Ensures broad community participation

2. ADA Holdings Weight (45%):
   - Uses logarithmic scaling to balance influence of large holders
   - Calculated as: log10(ada_amount + 1) / log10(1M)
   - Preserves stake-based voting while preventing dominance

3. Time Multiplier (25%):
   - Rewards long-term holders with increased influence
   - Acts as multiplier only on the ADA holdings component
   - Helps prevent manipulation through wallet splitting

This combination creates a balanced system where:
- Small holders maintain meaningful participation through base weight
- Large holders retain influence but with diminishing returns
- Long-term holders gain additional weight proportional to their commitment
- Short-term traders and split wallets have reduced impact

### Parameter Adjustability
The proposed weighting distribution (30% base, 45% ADA holdings, 25% holding time) represents an initial configuration designed to balance democratic participation with stake-based influence. However, these parameters can be adjusted through governance voting to better serve the community's needs:

#### Adjustable Parameters
- Base voting weight percentage (currently 30%)
- ADA holdings weight percentage (currently 45%)
- Holding time weight percentage (currently 25%)
- Logarithmic base for ADA calculation (currently 1M ADA)
- Minimum qualifying balance (currently 500 ADA)
- Time-related parameters (depending on chosen implementation):
  * For Fixed Period Option: Time period length (currently 2 years)
  * For Relative Time Option: Maximum multiplier cap (currently 2.0)

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

#### 1. Base Voting Weight per Wallet (30%)
The base voting weight component provides democratic basic participation rights while implementing necessary safeguards against potential abuse:

##### Minimum Qualifying Requirements
1. Balance Threshold
   - Minimum balance: 500 ADA
   - Can include both liquid ADA and delegated ADA in stake pools
   - Balance must be maintained throughout the voting period

2. Holding Duration
   - Minimum holding period: 30 days
   - Calculated using weighted average holding time
   - Resets for newly received ADA

3. Qualifying Balance Calculation
   ```
   Qualifying Balance = Liquid ADA + Delegated ADA (in stake pools)
   ```

##### Rationale for Minimum Requirements
1. Security Considerations
   - Prevents sybil attacks through wallet splitting
   - Makes manipulation through multiple wallets economically unfeasible
   - Ensures committed participation in the ecosystem

2. Accessibility Balance
   - 500 ADA threshold (~$300 USD at current prices) remains accessible to individual participants
   - Including delegated ADA allows stakers to participate without unstaking
   - 30-day holding period prevents short-term manipulation while allowing reasonable entry

3. Technical Factors
   - Accounts for UTXO minimum requirements
   - Provides sufficient balance for transaction fees across multiple votes
   - Compatible with existing stake delegation mechanics

4. Economic Design
   - Cost of attack (creating multiple qualifying wallets) exceeds potential benefits
   - Encourages long-term holding and stake delegation
   - Maintains broad participation while ensuring skin in the game

##### Implementation Notes
- Balance checking occurs at vote registration and submission
- Holding period calculated using transaction history
- Stake delegation status verified through on-chain data
- Automatic disqualification if balance drops below minimum during voting

#### 2. ADA Holdings Weighting (45%)
- Logarithmic instead of linear scaling
- Formula: Voting weight = log10(ada_amount + 1) / log10(1M)
- Reduces the influence of very large holdings
- Example:
  * 1,000 ADA = 0.20 weighting
  * 10,000 ADA = 0.40 weighting
  * 100,000 ADA = 0.60 weighting
  * 1,000,000 ADA = 0.80 weighting

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

The holding time is calculated using the complete transaction history of each wallet:
- Each transaction timestamp marks the start/end of a balance period
- The effective holding time is weighted by the actual balance held during each period
- Only continuously held balances contribute to the holding time calculation
- Transfers between wallets start fresh holding time calculations for the transferred amount

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

### Implementation Options for Holding Time Factor

#### Option A: Fixed Time Period (2 years)
The fixed time period approach uses transaction history to calculate an effective holding time up to a maximum of 2 years:

```python
def calculate_effective_holding_time(wallet):
    tx_history = get_wallet_transactions(wallet)
    current_time = get_current_slot_time()
    
    # Calculate weighted holding time from transaction history
    weighted_time = calculate_weighted_holding_time(tx_history, current_time)
    
    # Cap at 2 years
    max_holding_time = 365 * 2  # 2 years
    return min(weighted_time, max_holding_time)
```

#### Option B: Relative Time Calculation
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

##### Time Multiplier Cap (200%) Explanation
The time multiplier uses a maximum cap of 2.0 (200%) to ensure fair voting power distribution:

1. Base Case (100%):
   - A wallet held for the network average time receives a 1.0 multiplier (100%)
   - Example: If network average is 100 days, a 100-day wallet gets 1.0

2. Maximum Boost (200%):
   - Long-term holders can earn up to a 2.0 multiplier (200%)
   - This means their ADA-based voting power can at most double
   - Any holding time beyond 2x the network average is capped

3. Proportional Reduction:
   - Newer wallets receive a proportionally reduced multiplier
   - Example: A wallet held for half the network average gets 0.5 multiplier (50%)

Example calculations with 100-day network average:
```
Wallet A (100 days = network average):
- relative_position = 100/100 = 1.0 (100%)
- time_multiplier = min(1.0, 2.0) = 1.0 (normal voting power)

Wallet B (200 days = 2x average):
- relative_position = 200/100 = 2.0 (200%)
- time_multiplier = min(2.0, 2.0) = 2.0 (maximum doubled power)

Wallet C (300 days = 3x average):
- relative_position = 300/100 = 3.0 (300%)
- time_multiplier = min(3.0, 2.0) = 2.0 (capped at maximum)

Wallet D (50 days = 0.5x average):
- relative_position = 50/100 = 0.5 (50%)
- time_multiplier = min(0.5, 2.0) = 0.5 (reduced power)
```

This 200% cap was chosen because:
1. It provides meaningful incentive for long-term holding
2. Prevents excessive concentration of power in very old wallets
3. Creates clear upper bounds on time-based voting power
4. Maintains balanced influence across the network

This cap value of 2.0 is an adjustable parameter that can be modified through governance voting based on community feedback and network dynamics.

### Technical Implementation

#### Core Voting Power Calculation
```python
def calculate_voting_power(wallet):
    # Get wallet stats from transaction history
    stats = calculate_wallet_stats(wallet)
    
    # Only proceed if minimum balance requirement is met
    if stats.current_balance < MIN_VOTING_BALANCE:
        return 0
        
    # Base voting weight (30%)
    base_power = 1.0 * 0.3
    
    # ADA weighting (45%) - logarithmic
    ada_power = (math.log10(stats.current_balance + 1) / 
                math.log10(1000000)) * 0.45
    
    # Holding time weighting (25%)
    # Used as multiplication factor only for ADA-based voting power
    time_multiplier = calculate_time_multiplier(stats.effective_holding_days)
    
    # Base power remains constant, time multiplier only affects ADA power
    total_power = base_power + (ada_power * time_multiplier)
    
    return total_power

def calculate_wallet_stats(wallet_address):
    """
    Calculate wallet statistics using transaction history
    """
    txs = get_wallet_transactions(wallet_address)
    current_time = get_current_slot_time()
    
    # Sort transactions chronologically
    txs.sort(key=lambda x: x.timestamp)
    
    balance_periods = []
    current_balance = 0
    last_timestamp = None
    
    # Build list of balance periods from transactions
    for tx in txs:
        if last_timestamp is not None:
            balance_periods.append({
                'balance': current_balance,
                'start_time': last_timestamp,
                'end_time': tx.timestamp
            })
        
        current_balance += tx.amount  # (negative for outgoing tx)
        last_timestamp = tx.timestamp
    
    # Add final period up to current time
    if last_timestamp is not None:
        balance_periods.append({
            'balance': current_balance,
            'start_time': last_timestamp,
            'end_time': current_time
        })
    
    # Calculate weighted holding time
    total_weighted_time = 0
    total_weights = 0
    
    for period in balance_periods:
        if period['balance'] <= 0:
            continue
            
        period_length = (period['end_time'] - period['start_time']).total_seconds()
        period_length_days = period_length / (24 * 3600)
        
        total_weighted_time += period['balance'] * period_length_days
        total_weights += period['balance']
    
    if total_weights == 0:
        return {
            'effective_holding_days': 0,
            'current_balance': 0
        }
    
    return {
        'effective_holding_days': total_weighted_time / total_weights,
        'current_balance': current_balance
    }

def check_minimum_qualifying_balance(wallet_balance):
    """
    Check if wallet meets minimum balance requirement for base voting weight.
    
    Minimum Balance Requirements:
    - Primary: 500 ADA minimum balance
    - Additional: Must be held for at least 30 days
    - Exception: Delegated ADA in stake pools counts towards minimum
    
    Returns:
    - Boolean indicating if wallet qualifies for base voting weight
    """
    MIN_ADA_REQUIREMENT = 500  # 500 ADA minimum
    MIN_HOLDING_DAYS = 30      # 30 days minimum holding period
    
    # Get effective balance including delegated ADA
    total_effective_balance = wallet_balance.liquid_ada + wallet_balance.delegated_ada
    
    # Get holding duration
    holding_duration = calculate_effective_holding_time(wallet_balance)
    
    # Check both requirements
    meets_balance = total_effective_balance >= MIN_ADA_REQUIREMENT
    meets_duration = holding_duration >= MIN_HOLDING_DAYS
    
    return meets_balance and meets_duration
```

### Security Considerations

#### Transaction History Integrity
- All calculations are based on immutable blockchain history
- Each balance period is precisely defined by transaction timestamps
- No opportunity for manipulation through temporary balance changes

#### Attack Prevention
1. Wallet Splitting Protection
- Base voting weight requires minimum qualifying balance (500 ADA)
- Holding time multiplier only affects ADA-based voting power
- Even split wallets retain only base voting rights initially
- Cost of attack exceeds potential benefits

2. Flash Loan Protection
- Effective holding time calculation ensures only long-term holdings count
- Short-term balance spikes have minimal impact

3. Exchange Holdings
- Exchange-controlled wallets are naturally limited by holding time factor
- Encourages users to maintain personal custody

### Technical Requirements
This change requires a hard fork of the Cardano network due to:
- Implementation of new on-chain data structures for voting weight calculation
- Modification of governance mechanisms
- Change of consensus rules for voting validation

## Transition Plan
1. Community Discussion (1 month)
   - Discussion of advantages and disadvantages of both time calculation options
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