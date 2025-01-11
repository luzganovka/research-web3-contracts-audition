The Ethereum blockchain is made up of **_[[accounts]]_**, which you can think of like bank accounts. An account has a balance of **_[[Ether]]_** (the currency used on the Ethereum blockchain), and you can send and receive Ether payments to other accounts, just like your bank account can wire transfer money to other bank accounts.

Each account has an [[address]], which you can think of like a bank account number. It's a unique identifier that points to that account, and it looks like this:

`0x0cE446255506E92DF41614C46F1d6df9Cc969183`\

**an address is owned by a specific user** (or a smart contract).

Using [[Solidity|msg.sender]] gives you the security of the Ethereum blockchain — the only way someone can modify someone else's data would be to steal the private key associated with their Ethereum address.

[[Token]]
# Send 1 DAI
[What happens when you send 1 DAI](https://www.notonlyowner.com/learn/what-happens-when-you-send-one-dai)
DAI is a smart contract. Its main logic is implemented at address `0x6b175474e89094c44da98b954eedeac495271d0f` in Ethereum mainnet.

More specifically, DAI is an ERC20-compliant fungible token - quite a special type of contract. This means that _at least_ DAI should implement the interface detailed in the [ERC20 specification](https://eips.ethereum.org/EIPS/eip-20). In (somewhat stretched) web2 jargon, DAI is an immutable open-source web service running on Ethereum. Given it follows the ERC20 spec, it's possible to know in advance (without necessarily looking at the source code) the exact exposed endpoints to interact with it.