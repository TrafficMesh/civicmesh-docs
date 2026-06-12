# On-Chain Financial Transparency

**Immutable, verifiable ledger of all node earnings and revenue distribution.**

---

## Problem Statement

Revenue from enforcement activity should flow to communities, not disappear into corporate black boxes. Yet centralized databases can be edited, and corporate financials can be opaque. 

CivicMesh puts all revenue on a public blockchain. Any community member (or automated bot) can:
- Query node earnings
- Verify revenue splits match stated percentages
- Track D-Credit token flows
- Audit CCSC patronage fund distribution
- Prove their own earnings

No CivicMesh administrator can alter the ledger.

---

## Architecture

### On-Chain Data Structure

```solidity
// Polygon (Ethereum L2): Low cost, high throughput

contract CivicMeshLedger {
  
  // Node identity (does NOT link to vehicle number/route)
  mapping(bytes32 => NodePublicKey) nodeRegistry;
  // nodeRegistry[did:civicmesh:node:abc123] = PublicKey(...)
  
  // Monthly revenue record (immutable)
  struct MonthlyRevenue {
    bytes32 nodeDid;           // did:civicmesh:node:xyz789
    uint256 period;            // 202606 (June 2026)
    uint256 citationCount;     // 142 citations attributed
    uint256 grossRevenue;      // 15,620.00 (in cents, to avoid floats)
    
    Distribution distribution; // struct with splits
    uint256 timestamp;         // block.timestamp
    bytes32 txHash;            // for verification
  }
  
  struct Distribution {
    uint256 municipalShare;    // 72% = 11,246.40
    uint256 platformFee;       // 15% = 2,343.00
    uint256 ccscPatronage;     // 8% = 1,249.60
    uint256 nodeOperatorReward; // 5% = 781.00
  }
  
  // All-time ledger (append-only)
  MonthlyRevenue[] ledger;
  
  // Public query functions
  function nodeEarnings(bytes32 nodeDid, uint256 startPeriod, uint256 endPeriod) 
    public view returns (uint256 total);
  
  function cityCombinedEarnings(string city, uint256 period) 
    public view returns (uint256 total);
  
  function ccscPatronageDistribution(uint256 period) 
    public view returns (address[] recipients, uint256[] amounts);
}
```

### Civilian Rewards (Anonymous Wallets)

```solidity
// Civilian earnings (separate ledger for privacy)
contract CivicMeshCivilianRewards {
  
  // Wallet → earnings (wallet address is pseudonymous)
  mapping(address => uint256) earnings;
  
  // Record reward issuance
  struct RewardIssue {
    address wallet;         // Civilian's Ethereum address (anonymous)
    uint256 amount;         // Reward in D-Credit tokens
    uint256 incidentId;     // civicmesh:incident:ABC123 (for verification)
    uint256 timestamp;
  }
  
  RewardIssue[] ledger;
  
  // Civilian can prove earnings
  function walletEarnings(address wallet) 
    public view returns (uint256 total, RewardIssue[] history);
  
  // Aggregate monthly reward pool
  function monthlyRewardPool(uint256 period) 
    public view returns (uint256 totalDistributed);
}
```

---

## Data Visibility

### Public Queries (Anyone, No Auth)

✅ **Node Earnings**
```
Query: nodeEarnings(did:civicmesh:node:bus_456, 202604, 202606)
Response: 
{
  "period_202604": {
    "citations": 128,
    "gross": 14080.00,
    "distribution": {
      "municipal": 10137.60,
      "platform": 2112.00,
      "ccsc": 1126.40,
      "operator": 704.00
    }
  },
  ...
}
```

✅ **City-Level Aggregate**
```
Query: cityCombinedEarnings("Ottawa", 202606)
Response: 
{
  "total_citations": 2847,
  "gross_revenue": 312830.00,
  "all_nodes": 47,
  "distribution": { ... }
}
```

✅ **CCSC Patronage Distribution**
```
Query: ccscPatronageDistribution(202606)
Response:
{
  "period": 202606,
  "total_patronage": 24998.40,
  "recipients": [
    {
      "cooperative": "Training Coop",
      "amount": 12499.20,
      "purpose": "Officer training programs"
    },
    {
      "cooperative": "Community Fund",
      "amount": 12499.20,
      "purpose": "Street safety initiatives"
    }
  ]
}
```

### Private Queries (Authenticated)

🔐 **Civilian Personal Earnings**
```
Query: walletEarnings(0x1234...5678) [requires wallet signature]
Response:
{
  "total": 425.50,
  "incidents": [
    {
      "id": "civicmesh:incident:ABC123",
      "timestamp": 1717420335,
      "amount": 50.00,
      "status": "paid"
    },
    ...
  ]
}
```

---

## Revenue Distribution Model

### Every Citation Creates a Ledger Entry

```
Citation issued: Speeding 75 in 50 zone
Fine amount: $110
On-chain record created:

{
  "nodeDid": "did:civicmesh:node:bus_456",
  "period": "202606",
  "citationCount": [incrementing],
  "incidentId": "civicmesh:incident:CM-2026-00421",
  "fineAmount": 11000,  // in cents
  
  "distribution": {
    "municipal": 7920,      // 72% to Toronto enforcement
    "platform": 1650,       // 15% to CivicMesh operations
    "ccsc": 880,            // 8% to community patronage
    "operator": 550         // 5% to node operator reward
  },
  
  "timestamp": 1717420335,
  "blockNumber": 19234567,
  "txHash": "0xabc...789"
}
```

### Monthly Rollup

Once per month (automated):
1. Sum all daily entries for each node
2. Create aggregated MonthlyRevenue record
3. Record on blockchain
4. Broadcast to all stakeholders

**Example:** June 2026 for Bus #456
```
MonthlyRevenue {
  nodeDid: did:civicmesh:node:bus_456,
  period: 202606,
  citationCount: 142,
  grossRevenue: 1562000,  // $15,620.00 (in cents)
  distribution: {
    municipal: 1124640,   // 72%
    platform: 234300,     // 15%
    ccsc: 124960,         // 8%
    operator: 78100       // 5%
  },
  timestamp: 1719792000,
  txHash: 0xdef...012
}
```

---

## Verification Process

### Anyone Can Verify (Immutable Ledger)

**Step 1: Query node earnings**
```javascript
const earnings = await ledger.nodeEarnings(
  'did:civicmesh:node:bus_456',
  202601, 202612
);
```

**Step 2: Verify distribution math**
```javascript
const total = 1562000;  // From ledger
const expected = {
  municipal: total * 0.72,  // 1124640 ✓
  platform: total * 0.15,   // 234300 ✓
  ccsc: total * 0.08,       // 124960 ✓
  operator: total * 0.05    // 78100 ✓
};
```

**Step 3: Check against public report**
- Cross-reference blockchain total against annual transparency report
- If they differ: red flag, immediate investigation

**Step 4: Verify CCSC distribution**
```javascript
const ccscAllocation = await ledger.ccscPatronageDistribution(202606);
// ccscAllocation.total must equal sum of all node ccsc shares for month
```

---

## Smart Contract Guarantees

### Immutability (No Deletion)

```solidity
// Ledger is append-only
// No UPDATE, DELETE, or REVERT on historical entries
function _addMonthlyRevenue(MonthlyRevenue memory rev) private {
  // Only APPEND is allowed
  ledger.push(rev);
  // Cannot be removed or edited afterward
}

// Attempting to delete throws revert:
// "Cannot delete historical ledger entries"
```

### Correctness (Math is Verified)

```solidity
// Distribution must sum to 100%
function validateDistribution(Distribution memory d) private pure {
  uint256 total = d.municipal + d.platform + d.ccsc + d.operator;
  require(total == 10000, "Distribution must sum to 100%");  // In basis points
  
  // Percentages must match governance
  require(d.municipal == 7200, "Municipal must be 72%");
  require(d.platform == 1500, "Platform must be 15%");
  require(d.ccsc == 800, "CCSC must be 8%");
  require(d.operator == 500, "Operator must be 5%");
}
```

### Node Identity (No Leakage)

```solidity
// Node DID is mapped to wallet, not to vehicle
mapping(bytes32 => address) nodeWallets;
// did:civicmesh:node:bus_456 → 0x1234...5678 (to receive rewards)

// Vehicle number NEVER appears on-chain
// Plate NEVER appears on-chain
// Route NEVER appears on-chain
// Only: nodeDid (anonymized) + earnings data
```

---

## Civilian Reward Pool

### How Civilians Earn (Anonymous)

```
1. Submit evidence of violation
   └─ Video clip, timestamp, location

2. Violation confirmed & cited
   └─ Officer approves, citation issued

3. Reward issued
   └─ 50% of platform fee allocated to civilian rewards
   └─ Amount distributed per month (variable, based on caseload)
   └─ Sent to civilian's Ethereum wallet (anonymous)

4. Example (June 2026)
   └─ Total platform fee: $234,300
   └─ Civilian reward pool: $117,150 (50%)
   └─ 500 civilian submissions
   └─ Average reward: $234.30

5. Civilian verifies earnings
   └─ Query blockchain with wallet address
   └─ See all reward history
   └─ No CivicMesh staff can see civilian identity
```

### D-Credit Token (Future)

If cooperative uses D-Credit currency (blockchain-based):
- Rewards issued as D-Credit tokens
- Can be exchanged for CAD, goods, or cooperative services
- Tokens are transferable (can sell to other cooperatives)
- All transfers recorded on-chain (transparent flow of value)

---

## Audit Process

### Community Right to Audit

**Any CCSC member can request:**
1. Complete ledger export (JSON, timestamped)
2. Verification of all transactions
3. Cross-check against annual transparency report
4. Spot-check distribution percentages

**Automated audit bots** can run 24/7:
```python
# Bot monitors ledger
for month in all_months:
  ledger_total = query_ledger(month)
  report_total = parse_transparency_report(month)
  
  if ledger_total != report_total:
    alert("Discrepancy found: ledger vs report")
    # File issue on GitHub
    # Community investigation starts
```

---

## Privacy Safeguards

### What is ON-CHAIN (Public)

✅ Node DID (anonymized, not linked to vehicle)
✅ Monthly revenue totals
✅ Distribution percentages
✅ Period (month/year)

### What is OFF-CHAIN (Private)

✗ Individual citation details (stored in central DB)
✗ Officer/civilian identities (never on-chain)
✗ Specific violation types (aggregate only)
✗ GPS coordinates (stored in central DB, not on-chain)

### Wallet Privacy (For Civilians)

- Civilians receive rewards to anonymous Ethereum wallet
- No name linked to wallet address
- Only civil law enforcement (subpoena) can de-anonymize
- Blockchain itself is permanent (but pseudonymous)

---

## Technology Selection

### Recommended: Polygon (Ethereum L2)

**Why Polygon:**
- ✅ Low transaction costs ($0.01–0.10 per write)
- ✅ High throughput (7500 TPS)
- ✅ EVM-compatible (standard tools)
- ✅ Public mainnet (anyone can query)
- ✅ Immutable (no reorg risk after ~6 hours)
- ✅ Active community (used by major projects)

**Alternative: Hyperledger Fabric**
- ✅ Permissioned (controlled access)
- ✅ Faster finality (instant)
- ✗ Requires infrastructure (not everyone can query)
- ✗ More operational complexity

**Recommendation:** Start with Polygon, migrate to Hyperledger if city requires permissioned ledger.

---

## Integration with Governance

- **Annual report (GOV-003):** Blockchain ledger used to generate official numbers
- **Community audit (GOV-002):** Blockchain data used for audit verification
- **Transparency dashboard:** Real-time query of blockchain for current month
- **CCSC patronage:** Blockchain allocation visible to cooperative members

---

## Summary

**Revenue is transparent and immutable:**
- Node earnings: Queryable by any community member
- Distribution: Verified by smart contract (no fudging percentages)
- CCSC allocation: Public (cooperative members can audit)
- Civilian rewards: Anonymous (only wallet address visible)
- Every number is auditable, every transaction is permanent

---

Next: See [[COMMUNITY_EVIDENCE_VIEWING.md]] for how communities see enforcement in action. See [[TRANSPARENCY_LAYER.md]] for how all transparency mechanisms fit together.

