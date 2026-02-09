987654321// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/Strings.sol";

contract BaseCartoon is ERC721Enumerable, Ownable {
    using Strings for uint256;

    string public baseURI;
    uint256 public constant MAX_SUPPLY = 1000;
    uint256 public constant PRICE = 0.001 ether;

    constructor(string memory _initialBaseURI) ERC721("Base Cartoon", "BCART") Ownable(msg.sender) {
        baseURI = _initialBaseURI;
    }

    function mint(uint256 quantity) public payable {
        uint256 ts = totalSupply();
        require(ts + quantity <= MAX_SUPPLY, "Exceeds max supply");
        require(msg.value >= PRICE * quantity, "Ether sent is not correct");

        for (uint256 i = 0; i < quantity; i++) {
            _safeMint(msg.sender, ts + i);
        }
    }

    function _baseURI() internal view virtual override returns (string memory) {
        return baseURI;
    }

    function withdraw() public onlyOwner {
        uint256 balance = address(this).balance;
        payable(msg.sender).transfer(balance);
    }
}
