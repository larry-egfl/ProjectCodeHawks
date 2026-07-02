# Audit Findings: PuppyRaffle


---

## [H-01] Didn't check the related between msg.sender and the player that be call as a parameter.

**Location:** `src/PuppyRaffle.sol` line 96  
**Severity:** High

**Description:**  
Didn't check the related between `msg.sender` and the `playerIndex` that be call as a parameter,whitch make everyone entrancFee be stolen.

**Proof of Concept:**  
```solidity

```

**Recommendation:**  
Add a map that save the related the `manager` and the `palyer` who played the raffle.And check `msg.sender` if it is the `manager`.

---

## [H-02] Anyone can call the `selectWinner` function

**Location:** `src/PuppyRaffle.sol` line 128  
**Severity:** High

**Description:**  
Anyone can call the `selectWinner` function,witch allow attacker caculate the winnerIndex to make himself address becaming a winnerIndex. 

**Proof of Concept:**  
```solidity

```

**Recommendation:**  
Add access control (e.g. onlyOwner or a role-based check) to restrict who can call this function.

---


## [M-01] The design of the line is not reasonable

**Location:** `src/PuppyRaffle.sol` line 98  
**Severity:** medium

**Description:**  
 That should be anyone who never enter this raffle have the right to play this

**Proof of Concept:**  
```solidity

```

**Recommendation:**  
Delete the line 98


---


## [M-02] The design of the line is not reasonable

**Location:** `src/PuppyRaffle.sol` line 151  
**Severity:** medium

**Description:**  
 Consider put  `(bool success,) = winner.call{value: prizePool}("")` ahead of `delete player` to ensure all storage is revalued properly.

**Proof of Concept:**  
```solidity

```

**Recommendation:**  
`(bool success,) = winner.call{value: prizePool}("");` should put ahead of `delete player`

---

## [L-01] `getActivePlayerIndex` that is be created is not used in the contract

**Location:** `src/PuppyRaffle.sol` line 110  
**Severity:** low

**Description:**  
 The fuction that is be created is not used in the contract.

**Proof of Concept:**  
```solidity

```

**Recommendation:**  
Delete the line 98