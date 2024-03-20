# ETH Samba ENS Workshop
O objetivo do workshop é que possamos deployar um contrato de Resolver da ENS com algumas informações pessoais, criar um domínio da ENS e depois usar esse proprio contrato que deployamos junto a interface frontend da ENS.

## Mint de eth na Sepholia
Para assinar a transação e criar um domínio de teste ENS, é necessário obter um pouco de ether na carteira. Para fazer isso, siga os próximos passos:

- Acesse um dos faucets da Sepolia, como https://sepolia-faucet.pk910.de/#/
Resolva o captcha e insira o endereço da sua carteira:
![](<images/get sepholia eth.png>)

- Start mining to get founds:
![](<images/start mining eth.png>)

- Depois de um minuto, clique em Parar de mineirar, e então os fundos vão ser transferidos para o seu endereço.
![](<images/mining result.png>)

## Create ENS domain

Agora que já temos fundos na cadeia da Sepolia, é possível comprar nosso domínio de teste na ENS.

- Acesse o aplicativo da ENS (https://app.ens.domains/) e conecte sua carteira:
![](<images/connect metamask at ens.png>)

- Selecione sua conta com seploliaETH minerado.
![](<images/connect metamask 2.0.png>)

- Escolha um nome para o seu domínio ENS.
![](<images/select ens name.png>)

- Selecione a duração do domínio.
![](<images/domain time.png>)

- Inicie a transação.
![](<images/start transaction.png>)

- Aguarde a conclusão da transação.
![](<images/waiting transaction.png>)

- Confirmar transação.
![](<images/confirm transaction again.png>)

- Profile criado!
![](cimages/reated.png)

## Deploy do Resolver

### Requirements

- [Metamask](https://metamask.io)
- [Foundry](https://book.getfoundry.sh/)

## Remix Deploy

[Remix Ethereum IDE](https://remix.ethereum.org)

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.2 <0.9.0;

import "https://github.com/ensdomains/ens-contracts/blob/staging/contracts/resolvers/PublicResolver.sol";

contract MyResolver is PublicResolver {
    
    constructor(
        ENS _ens,
        INameWrapper wrapperAddress,
        address _trustedETHController,
        address _trustedReverseRegistrar
    ) PublicResolver(
         _ens,
         wrapperAddress,
         _trustedETHController,
         _trustedReverseRegistrar
    ) {

    }
}
```

## Local deploy

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

## Discord

Join us on Discord:

![](images/qr-code.png)