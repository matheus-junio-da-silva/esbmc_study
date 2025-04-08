# Test contracts

```solidity
contract DivisionByZero {
    uint256 public result;
    function divide(uint256 a, uint256 b) public {
        result = a / b; // <-- vulnerável se b == 0
    }
}
```  

