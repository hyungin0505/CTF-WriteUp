# meme
[ssumeme](https://sepolia.etherscan.io/address/0xc48bdba1481c391cf67249818d2e972f737976d8#code)

EtherScan website..
```js
/**
 *Submitted for verification at Etherscan.io on 2025-01-19
*/

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

contract Ssumeme {
    address public owner;
    string private flag;

    constructor() {
        owner = msg.sender;

        uint256 blockHashPart = uint256(blockhash(block.number - 1)) % 1000000000;
        flag = string(abi.encodePacked("ssu{", uint2str(blockHashPart), "_m3m3}"));
    }

    function getFlag() public view returns (string memory) {
        require(msg.sender == owner, "Only the contract owner can access the flag. Contact to soongsil.asc@gmail.com :)");
        return flag;
    }

    function uint2str(uint256 _i) internal pure returns (string memory) {
        if (_i == 0) {
            return "0";
        }
        uint256 j = _i;
        uint256 length;
        while (j != 0) {
            length++;
            j /= 10;
        }
        bytes memory bstr = new bytes(length);
        uint256 k = length;
        while (_i != 0) {
            k = k - 1;
            uint8 temp = (48 + uint8(_i - _i / 10 * 10));
            bytes1 b1 = bytes1(temp);
            bstr[k] = b1;
            _i /= 10;
        }
        return string(bstr);
    }
}
```
To get flag, just find `unit2str(blockHaskPart)`.

---

```js
constructor() {
        owner = msg.sender;

        uint256 blockHashPart = uint256(blockhash(block.number - 1)) % 1000000000;
        flag = string(abi.encodePacked("ssu{", uint2str(blockHashPart), "_m3m3}"));
    }
```
You can get `blockHashPart` with `block.number` and `blockhash`.  
`block.number` can be found in creator of `ssumeme` as 7523642.  

There is hash in `7523642 - 1` block.  

`ssu{809209385_m3m3}`

# compressor
I solved it with unintendedly..  

Just input nothing and press Enter.  

`ssu{have_you_heard_about_the_quine?}`

The intended writeup is to use [gzip-quine](https://github.com/Honno/gzip-quine/blob/main/quine.gz)