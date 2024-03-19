# ETH Samba ENS Workshop

**Foundry is a blazing fast, portable and modular toolkit for Ethereum application development written in Rust.**

Foundry consists of:

- **Forge**: Ethereum testing framework (like Truffle, Hardhat and DappTools).
- **Cast**: Swiss army knife for interacting with EVM smart contracts, sending transactions and getting chain data.
- **Anvil**: Local Ethereum node, akin to Ganache, Hardhat Network.
- **Chisel**: Fast, utilitarian, and verbose solidity REPL.

## Requirements

- [Metamask](https://metamask.io)
- [Foundry](https://book.getfoundry.sh/)

## Usage

### Installation

```shell
curl -L https://foundry.paradigm.xyz | bash
foundryup
```

### Start project

```shell
forge init eth-samba-ens
```

Remove `lib/forge-std` and add it as a submodule:

```shell
rm -rf lib/forge-std
forge install https://github.com/foundry-rs/forge-std --no-commit 
```

Create `.gitignore`

```gitignore
.env
out/
cache/
```

### Deploy Public Resolver on localhost

```shell
forge create src/Counter.sol:Counter \
--rpc-url http://localhost:8545 \
--private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
```

#### Interact

```shell
# Set number
cast send 0x5fbdb2315678afecb367f032d93f642f64180aa3 "function setNumber(uint256 newNumber)" \
--private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 \
--rpc-url http://localhost:8545 10

# Read number
cast call 0x5fbdb2315678afecb367f032d93f642f64180aa3 "function number()(uint256)" \
--private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 \
--rpc-url http://localhost:8545
```

### Deploy Public Resolver on Sepolia testnet

[ENS Contracts Sepolia deployments](https://github.com/ensdomains/ens-contracts/tree/staging/deployments/sepolia)
[Constructor argumetns reference](https://github.com/ensdomains/ens-contracts/blob/staging/deploy/resolvers/00_deploy_public_resolver.ts)

1. Registry: 0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e
2. NameWrapper: 0x0635513f179D50A207757E05759CbD106d7dFcE8
3. ETHRegistrarController: 0xFED6a969AaA60E4961FCD3EBF1A2e8913ac65B72
4. ReverseRegistrar: 0xA0a1AbcDAe1a2a4A2EF8e9113Ff0e02DD81DC0C6

```shell
forge create lib/ens-contracts/contracts/resolvers/PublicResolver.sol:PublicResolver \
--constructor-args \
0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e \
0x0635513f179D50A207757E05759CbD106d7dFcE8 \
0xFED6a969AaA60E4961FCD3EBF1A2e8913ac65B72 \
0xA0a1AbcDAe1a2a4A2EF8e9113Ff0e02DD81DC0C6 \
--private-key <private_key>
```

#### Interact

[Public Resolver interface](https://docs.ens.domains/resolvers/interfaces)
[Namehash online](https://swolfeyes.github.io/ethereum-namehash-calculator/)

```shell
# Read Ethereum address
cast call 0x2E662240DDF295d3055071a84a7a0d100AAD8b30 "addr(bytes32 node)(address)" \
0x2ea8501801807d961e71597b8b554a157afb1f744488938033fd69f66117b721

# Change Ethereum address
cast send 0x2E662240DDF295d3055071a84a7a0d100AAD8b30 "setAddr(bytes32 node, address addr)" \
--private-key <private-key> \
--rpc-url <rpc-url> \
0x2ea8501801807d961e71597b8b554a157afb1f744488938033fd69f66117b721 \
0x86f012BC12B986FcF58AD672f03817cD210528ed

# Read twitter handle
cast call 0x2E662240DDF295d3055071a84a7a0d100AAD8b30 "text(bytes32 node, string calldata key)(string)" \
0xf790f34d644b981e4f85ed0b8ed66e48d4117829e666903f2efcd597c14f5de1 avatar

# Change twitter handle
cast send 0x2E662240DDF295d3055071a84a7a0d100AAD8b30 "setText(bytes32 node,string key,string value)" \
--private-key <private-key> \
--rpc-url <rpc-url> \
0xf790f34d644b981e4f85ed0b8ed66e48d4117829e666903f2efcd597c14f5de1 avatar blockful.png
```
