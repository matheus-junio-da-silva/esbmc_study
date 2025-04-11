### Test contracts

contract.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract DivisionByZero {
    uint256 public result;
    function divide(uint256 a, uint256 b) public {
        result = a / b; // <-- vulnerável se b == 0
    }
}
```  

---

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AssertionExample {
    uint256 public value;

    function set(uint256 x) public {
        value = x;
        assert(x < 100); // <-- assert que pode falhar
        value = x + 1;    // <-- código inalcançável se assert falhar
    }
}

```
