# Audit Findings: SantasList

---

## [H-01] Missing access control on `checkList`

**Location:** `src/SantasList.sol` line 121  
**Severity:** High

**Description:**  
`checkList` lacks the `onlySanta` modifier, allowing anyone to set 
their own status to `NICE` or `EXTRA_NICE` without Santa's approval.

**Proof of Concept:**  
```solidity
// 攻击者直接把自己设成 NICE
santasList.checkList(attacker, Status.NICE);
```

**Recommendation:**  
Add `onlySanta` modifier to `checkList`.

---

## [H-02] Enum default value allows anyone to collect present

**Location:** `src/SantasList.sol` line 62  
**Severity:** High

**Description:**  
`NICE` is at index 0 in the enum, which is the default value for 
uninitialized mapping entries. Any address never checked by Santa 
will pass the `collectPresent` check automatically.

**Proof of Concept:**  
```solidity
// 从未被 Santa 检查过的地址，直接调用即可领取
santasList.collectPresent();
```

**Recommendation:**  
Move `NOT_CHECKED_TWICE` to index 0 in the enum definition.

---

## [H-03] `buyPresent` burns receiver's token instead of caller's

**Location:** `src/SantasList.sol` line 172  
**Severity:** High

**Description:**  
`buyPresent` burns `presentReceiver`'s token instead of `msg.sender`'s,
and mints the NFT to `msg.sender` instead of `presentReceiver`.
The caller gains an NFT for free while the receiver loses a token.

**Proof of Concept:**  
```solidity
// 攻击者调用后：自己多一个 NFT，alice 损失 1 个 SantaToken
santasList.buyPresent(alice);
```

**Recommendation:**  
```solidity
// 修改为：
i_santaToken.burn(msg.sender);
_safeMint(presentReceiver, s_tokenCounter++);
```

---

## [H-04] `AlreadyCollected` check can be bypassed by transferring NFT

**Location:** `src/SantasList.sol` line 152  
**Severity:** High

**Description:**  
The duplicate-collect check uses `balanceOf(msg.sender) > 0`, which 
becomes 0 after the NFT is transferred out. An attacker can collect,
transfer, then collect again indefinitely.

**Proof of Concept:**  
```solidity
santasList.collectPresent();              // 领第一个
santasList.transferFrom(me, other, 0);   // 转走
santasList.collectPresent();              // 再领一个
```

**Recommendation:**  
Use a separate `mapping(address => bool) private s_alreadyCollected` 
to track collection status instead of relying on `balanceOf`.