# Test contracts

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

