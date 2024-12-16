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

### 1. Base Voting Weight per Wallet (40%)
- Democratic basic participation
- Strengthens small holders and active community members
- Ensures minimum voting weight independent of ADA holdings
- Requires minimum qualifying balance to prevent abuse

### 2. ADA Holdings Weighting (35%)
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

### 3. Holding Time Weighting as Multiplier (25%)
The holding time factor serves as a crucial component in preventing manipulation and rewarding long-term engagement in the ecosystem. It acts as a multiplier specifically for the ADA holdings-based voting power, ensuring that:
- Long-term holders receive proportionally more influence over their ADA-based voting power
- New wallets retain basic participation rights while having reduced ADA-based influence
- The system naturally resists short-term manipulation attempts while remaining fair to legitimate transfers

The holding time is calculated using the complete transaction history of each wallet:
- Each transaction timestamp marks the start/end of a balance period
- The effective holding time is weighted by the actual balance held during each period
- Only continuously held balances contribute to the holding time calculation
- Transfers between wallets start fresh holding time calculations for the transferred amount

## Implementation Options for Holding Time Factor

### Option A: Fixed Time Period (2 years)
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

### Option B: Relative Time Calculation
The relative time approach compares a wallet's effective holding time to the network average:

```python
def calculate_relative_holding_time(wallet):
    tx_history = get_wallet_transactions(wallet)
    current_time = get_current_slot_time()
    
    # Calculate wallet's weighted holding time
    wallet_weighted_time = calculate_weighted_holding_time(tx_history, current_time)
    
    # Get network average holding time
    network_avg_time = get_network_avg_holding_time()
    
    # Calculate relative position with 2.0 cap
    relative_position = wallet_weighted_time / network_avg_time
    return min(relative_position, 2.0)
```

#### Multiplier Cap Explanation
The maximum multiplier of 2.0 means that a wallet can receive at most double the voting power from the time factor compared to a wallet with average holding time. This creates an upper bound on the influence of very old wallets while still rewarding long-term holders.

Example calculation with a network average holding time of 100 days:
```
Wallet A (100 days) = network average:
- relative_position = 100/100 = 1.0
- time_multiplier = min(1.0, 2.0) = 1.0

Wallet B (200 days) = 2x average:
- relative_position = 200/100 = 2.0
- time_multiplier = min(2.0, 2.0) = 2.0

Wallet C (300 days) = 3x average:
- relative_position = 300/100 = 3.0
- time_multiplier = min(3.0, 2.0) = 2.0 (capped)

Wallet D (50 days) = 0.5x average:
- relative_position = 50/100 = 0.5
- time_multiplier = min(0.5, 2.0) = 0.5
```

This cap value of 2.0 is an adjustable parameter that can be modified through governance voting based on community feedback and network dynamics.

## Parameter Adjustability
The proposed weighting distribution (40% base, 35% ADA holdings, 25% holding time) represents an initial configuration designed to balance democratic participation with stake-based influence. However, these parameters can be adjusted through governance voting to better serve the community's needs:

### Adjustable Parameters
- Base voting weight percentage (currently 40%)
- ADA holdings weight percentage (currently 35%)
- Holding time weight percentage (currently 25%)
- Logarithmic base for ADA calculation (currently 1M ADA)
- Minimum qualifying balance for voting
- Time-related parameters (depending on chosen implementation):
  * For Fixed Period Option: Time period length (currently 2 years)
  * For Relative Time Option: Maximum multiplier cap (currently 2.0)

### Adjustment Process
1. Parameters can be modified through governance voting
2. Changes require a qualified majority (exact threshold to be determined by community)
3. Implementation through parameter updates, not requiring hard forks
4. Regular review periods (e.g., yearly) to assess parameter effectiveness

### Considerations for Adjustments
- Changes should maintain a balance between small and large holders
- Total of all weight percentages must equal 100%
- Parameters should be adjusted based on empirical network data
- Community feedback and participation metrics should guide changes
- Impact analysis required before major parameter updates

## Technical Implementation

### Core Voting Power Calculation
```python
def calculate_voting_power(wallet):
    # Get wallet stats from transaction history
    stats = calculate_wallet_stats(wallet)
    
    # Only proceed if minimum balance requirement is met
    if stats.current_balance < MIN_VOTING_BALANCE:
        return 0
        
    # Base voting weight (40%)
    base_power = 1.0 * 0.4
    
    # ADA weighting (35%) - logarithmic
    ada_power = (math.log10(stats.current_balance + 1) / 
                math.log10(1000000)) * 0.35
    
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
```

## Security Considerations

### Transaction History Integrity
- All calculations are based on immutable blockchain history
- Each balance period is precisely defined by transaction timestamps
- No opportunity for manipulation through temporary balance changes

### Attack Prevention
1. Wallet Splitting Protection
- Base voting weight requires minimum qualifying balance
- Holding time multiplier only affects ADA-based voting power
- Even split wallets retain only base voting rights initially
- Cost of attack exceeds potential benefits

2. Flash Loan Protection
- Effective holding time calculation ensures only long-term holdings count
- Short-term balance spikes have minimal impact

3. Exchange Holdings
- Exchange-controlled wallets are naturally limited by holding time factor
- Encourages users to maintain personal custody

## Technical Requirements
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