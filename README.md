
# ETH + AVAX PROOF: Intermediate EVM Course Module 4
#### this code showcases the smart contract for degen token

Contract initialization , address mapping, other important initializations
```
// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

contract UniqueERC20 {
    address public immutable owner;
    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    struct Item {
        uint itemId;
        string itemName;
        uint itemPrice;
    }

    Item[] public items;

    // Mapping to track redeemed items for each user
    mapping(address => mapping(uint => bool)) public redeemedItems;

    // Event to log item redemption
    event ItemRedeemed(address indexed user, uint indexed itemId, string itemName, uint itemPrice);

    // Event to log token transfers
    event Transfer(address indexed from, address indexed to, uint amount);

    constructor() {
        owner = msg.sender;
        totalSupply = 0;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the contract owner can execute this function");
        _;
    }

    string public constant name = "UniqueToken";
    string public constant symbol = "UTK";
    uint8 public constant decimals = 18;
```
functions for Transfer token , Mint token, burn token, add item , get item , redeem item respectively.
```
    function transfer(address recipient, uint amount) external returns (bool) {
        require(balanceOf[msg.sender] >= amount, "Insufficient balance");

        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;

        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function mint(address receiver, uint amount) external onlyOwner {
        balanceOf[receiver] += amount;
        totalSupply += amount;
        emit Transfer(address(0), receiver, amount);
    }

    function burn(uint amount) external {
        require(amount > 0, "Amount should be greater than zero");
        require(balanceOf[msg.sender] >= amount, "Insufficient balance");
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;

        emit Transfer(msg.sender, address(0), amount);
    }

    function addItem(string memory itemName, uint itemPrice) external onlyOwner {
        uint itemId = items.length;
        items.push(Item(itemId, itemName, itemPrice));
    }

    function getItems() external view returns (Item[] memory) {
        return items;
    }

    function redeem(uint itemId) external {
        require(itemId < items.length, "Invalid item ID");
        Item memory item = items[itemId];

        require(balanceOf[msg.sender] >= item.itemPrice, "Insufficient balance to redeem");
        require(!redeemedItems[msg.sender][itemId], "Item already redeemed");

        balanceOf[msg.sender] -= item.itemPrice;
        balanceOf[owner] += item.itemPrice;

        redeemedItems[msg.sender][itemId] = true;

        emit Transfer(msg.sender, owner, item.itemPrice);
        emit ItemRedeemed(msg.sender, itemId, item.itemName, item.itemPrice);
    }
}
```
#### Thank you
