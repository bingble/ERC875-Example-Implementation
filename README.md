# ERC875-Example
An example implementation of our new ERC spec draft.

Create your own here: https://alphawallet.github.io/ERC875-token-factory/index

Test it out here: https://rinkeby.etherscan.io/address/0xffab5ce7c012bc942f5ca0cd42c3c2e1ae5f0005

Referenced here: https://github.com/ethereum/EIPs/issues/875

    contract ERC
    {
        event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
        event Approval(address indexed _owner, address indexed _approved, uint256 _tokenId);

        function name() constant returns (string name);
        function symbol() constant returns (string symbol);
        function balanceOf(address _owner) public view returns (uint256[] _balances);
        function transfer(address _to, uint256[] _tokens) public;
        function transferFrom(address _from, address _to, uint256[] _tokens) public;

        //optional
        function totalSupply() constant returns (uint256 totalSupply);
        function trade(uint256 expiryTimeStamp, uint256[] tokenIndices, uint8 v, bytes32 r, bytes32 s) public payable
        function ownerOf(uint256 _tokenId) public view returns (address _owner);
    }

## Summary
A simple non fungible token standard that allows batching tokens into lots and settling p2p atomic transfers in one transaction.

## Purpose
While other standards allow the user to transfer a non-fungible token, they require one transaction per token, this is heavy on gas and partially responsible for clogging the ethereum network. There are also few definitions for how to do a simple atomic swap.

## Rinkeby example
This standard has been implemented in an example contract on rinkeby: https://rinkeby.etherscan.io/address/0xffab5ce7c012bc942f5ca0cd42c3c2e1ae5f0005

## Specification

### function name() constant returns (string name)

returns the name of the contract e.g. CarLotContract

### function symbol() constant returns (string symbol)

Returns a short string of the symbol of the in-fungible token, this should be short and generic as each token is non-fungible.

### function balanceOf(address _owner) public view returns (uint256[] balance)

Returns an array of the users balance.

### function transfer(address _to, uint256[] _tokens) public;

Transfer your unique tokens to an address by adding an array of the token indices. This compares favourable to ERC721 as you can transfer a bulk of tokens in one go rather than one at a time. This has a big gas saving as well as being more convenient.

### function transferFrom(address _from, address _to, uint256[] _tokens) public;

Transfer a variable amount of tokens from one user to another. This can be done from an authorised party with a specified key e.g. contract owner.

## Optional functions

### function totalSupply() constant returns (uint256 totalSupply);

Returns the total amount of tokens in the given contract, this should be optional as assets might be allocated and issued on the fly. This means that supply is not always fixed.

### function ownerOf(uint256 _tokenId) public view returns (address _owner);

Returns the owner of a particular token, I think this should be optional as not every token contract will need to track the owner of a unique token and it costs gas to loop and map the token id owners each time the balances change. 

### function trade(uint256 expiryTimeStamp, uint256[] tokenIndices, uint8 v, bytes32 r, bytes32 s) public payable

A function which allows a user to sell a batch of non-fungible tokens without paying for the gas fee (only the buyer has to). This is achieved by signing an attestation containing the amount of tokens to sell, the contract address, an expiry timestamp, the price and a prefix containing the ERC spec name and chain id. A buyer can then pay for the deal in one transaction by attaching the appropriate ether to satisfy the deal.

This design is also more efficient as it allows orders to be done offline until settlement as opposed to creating orders in a smart contract and updating them. The expiry timestamp protects the seller against people using old orders.

This opens up the gates for a p2p atomic swap but should be optional to this standard as some may not have use for it.

Some protections need to be added to the message such as encoding the chain id, contract address and the ERC spec name to prevent replays and spoofing people into signing message that allow a trade.




# Donations
If you support the cause, we could certainly use donations to help fund development:

0xbc8dAfeacA658Ae0857C80D8Aa6dE4D487577c63

