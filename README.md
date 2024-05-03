// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    address public owner;
    mapping(address => bool) private frozenAccounts;

    event AccountFrozen(address indexed account);
    event AccountUnfrozen(address indexed account);

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the owner can call this function");
        _;
    }

    modifier notFrozen(address _account) {
        require(!frozenAccounts[_account], "Account is frozen");
        _;
    }

    constructor() ERC20("MyToken", "MTK") {
        owner = msg.sender;
        _mint(msg.sender, 1000000 * 10 ** uint(decimals()));
    }

    function freezeAccount(address _account) external onlyOwner {
        require(_account != owner, "Owner account cannot be frozen");
        frozenAccounts[_account] = true;
        emit AccountFrozen(_account);
    }

    function unfreezeAccount(address _account) external onlyOwner {
        frozenAccounts[_account] = false;
        emit AccountUnfrozen(_account);
    }

    function transfer(address recipient, uint256 amount) public override notFrozen(msg.sender) notFrozen(recipient) returns (bool) {
        return super.transfer(recipient, amount);
    }

    function transferFrom(address sender, address recipient, uint256 amount) public override notFrozen(sender) notFrozen(recipient) returns (bool) {
        return super.transferFrom(sender, recipient, amount);
    }
}# Final-Project