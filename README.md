# ETH Samba ENS Workshop

**Foundry is a blazing fast, portable and modular toolkit for Ethereum application development written in Rust.**

Foundry consists of:

- **Forge**: Ethereum testing framework (like Truffle, Hardhat and DappTools).
- **Cast**: Swiss army knife for interacting with EVM smart contracts, sending transactions and getting chain data.
- **Anvil**: Local Ethereum node, akin to Ganache, Hardhat Network.
- **Chisel**: Fast, utilitarian, and verbose solidity REPL.

## Requirements 

- [ ] [Metamask](https://metamask.io)
- [ ] [Foundry](https://book.getfoundry.sh/)

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

### Localhost

#### Deploy

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

