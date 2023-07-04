# Tapioca audit details
- Total Prize Pool: $390,000 USDC 
  - HM awards: $222,338 USDC 
  - Analysis awards: $13,476 USDC 
  - QA awards: $6,737 USDC 
  - Bot Race awards: $20,212 USDC 
  - Gas awards: $6,737 USDC 
  - Judge awards: $26,000 USDC 
  - Lookout awards: $12,000 USDC 
  - Scout awards: $500 USDC 
  - Mitigation Review: $82,000 USDC (*Opportunity goes to top 5 certified wardens based on placement in this audit.*)
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2023-07-tapioca-dao/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts July 05, 2023 20:00 UTC 
- Ends August 04, 2023 20:00 UTC 


## Automated Findings / Publicly Known Issues

Automated findings output for the audit can be found [here](add link to report) within 24 hours of audit opening.

*Note for C4 wardens: Anything included in the automated findings output is considered a publicly known issue and is ineligible for awards.*


# Overview

The Tapioca protocol is built with a lot of different smart contracts, scattered across 5 repositories.
It's an _Omnichain_ protocol working the LayerZero messaging layer. At its core, Tapioca ERC20/ERC721 contracts uses the LayerZero [OFTv2](https://github.com/LayerZero-Labs/solidity-examples/blob/main/contracts/token/oft/v2/OFTV2.sol) and [ONFT721](https://github.com/LayerZero-Labs/solidity-examples/blob/main/contracts/token/onft/ONFT721.sol) contracts.

The main repository is [tapioca-bar](./tapioca-bar-audit/), which contains [USDO](./tapioca-bar-audit/contracts/usd0/USDO.sol), a stablecoin. [BigBang](./tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol), a CDP based contract that mint and burn `USDO`. And [Singularity](./tapioca-bar-audit/contracts/markets/singularity/Singularity.sol), a lending and borrowing platform.

The other repos are here to support the ecosystem as well as to create a synergy between the tokenemics and the protocol features.

* [tap-token](./tap-token-audit/) Contracts related to the tokenemics, is linked to `tapioca-bar` in an asymmetric way.
* [tapiocaz](./tapiocaz-audit/) Contracts that contains a wrapper named `TOFT`, which is used to wrap gas tokens and transfer allow their usage through the LayerZero network.
* [tapioca-periph](./tapioca-periph-audit/) Periphery contracts. The main contract is `MagnetarV2`, acts as a helper that reduce the number of user taken actions/transactions.
* [YieldBox](./YieldBox/) A "BentoBox v2". Acts as a vault, that allow for yield strategies to be applied on the asset.
* [yieldbox-strategies](./tapioca-yieldbox-strategies-audit/) Yield strategies that will be used by a YieldBox asset.

![tapioca-userflow](./tapioca-userflow.jpg)

# Scope

## tapioca-bar
| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [./contracts/Penrose.sol](./contracts/Penrose.sol) | 377 | Owner contract for USDO & BB | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity)|
| [./contracts/markets/MarketERC20.sol](./contracts/markets/MarketERC20.sol) | 181 | Base contract for [Market.sol](./contracts/markets/Market.sol) | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/markets/Market.sol](./contracts/markets/Market.sol) | 316 | Base contract for BigBang & Singularity | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity) |
| [./contracts/markets/singularity/SGLLiquidation.sol](./contracts/markets/singularity/SGLLiquidation.sol) | 288 | Singularity module for liquidations | |
| [./contracts/markets/singularity/SGLCommon.sol](./contracts/markets/singularity/SGLCommon.sol) | 209 | Singularity base contract | |
| [./contracts/markets/singularity/SGLLeverage.sol](./contracts/markets/singularity/SGLLeverage.sol) | 135 | Singularity module for leverage | |
| [./contracts/markets/singularity/SGLStorage.sol](./contracts/markets/singularity/SGLStorage.sol) | 121 | Singularity storage layout | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity) |
| [./contracts/markets/singularity/SGLLendingCommon.sol](./contracts/markets/singularity/SGLLendingCommon.sol) | 78 | Singularity base contract | |
| [./contracts/markets/singularity/SGLBorrow.sol](./contracts/markets/singularity/SGLBorrow.sol) | 31 | Singularity borrowing module  | |
| [./contracts/markets/singularity/SGLCollateral.sol](./contracts/markets/singularity/SGLCollateral.sol) | 22 | Singularity collateral module  | |
| [./contracts/markets/singularity/Singularity.sol](./contracts/markets/singularity/Singularity.sol) | 452 | Lending & borrowing | |
| [./contracts/usd0/BaseUSDOStorage.sol](./contracts/usd0/BaseUSDOStorage.sol) | 56 | Base USDO contract | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/usd0/modules/USDOLeverageModule.sol](./contracts/usd0/modules/USDOLeverageModule.sol) | 277 | USDO Module for leverage | |
| [./contracts/usd0/modules/USDOOptionsModule.sol](./contracts/usd0/modules/USDOOptionsModule.sol) | 269 | USDO Module for [TapiocaBrokerOption.sol](./tap-token-audit/contracts/options/TapiocaOptionBroker.sol) calls | |
| [./contracts/usd0/modules/USDOMarketModule.sol](./contracts/usd0/modules/USDOMarketModule.sol) | 268 | USDO Module for Singularity | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity) |
| [./contracts/usd0/BaseUSDO.sol](./contracts/usd0/BaseUSDO.sol) | 358 | Custom LayerZero OFT logic, inherited in USDO | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/usd0/USDO.sol](./contracts/usd0/USDO.sol) | 69 | USDO stablecoin |  |
| [./contracts/markets/bigBang/BigBang.sol](./contracts/markets/bigBang/BigBang.sol) | 555 | Mint and burn USDO through CDP | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity) |


## tapiocaz
| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [./contracts/tOFT/BaseTOFTStorage.sol](./contracts/tOFT/BaseTOFTStorage.sol) | 55 | Base TOFT EVM storage layout | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/tOFT/BaseTOFT.sol](./contracts/tOFT/BaseTOFT.sol) | 455 | Base TOFT contract | |
| [./contracts/tOFT/modules/BaseTOFTLeverageModule.sol](./contracts/tOFT/modules/BaseTOFTLeverageModule.sol) | 305 | Base TOFT leverage module | |
| [./contracts/tOFT/modules/BaseTOFTMarketModule.sol](./contracts/tOFT/modules/BaseTOFTMarketModule.sol) | 285 | Base TOFT Singularity market module | |
| [./contracts/tOFT/modules/BaseTOFTOptionsModule.sol](./contracts/tOFT/modules/BaseTOFTOptionsModule.sol) | 283 | Base TOFT TapiocaOptionBroker market module | |
| [./contracts/tOFT/modules/BaseTOFTStrategyModule.sol](./contracts/tOFT/modules/BaseTOFTStrategyModule.sol) | 203 | Base TOFT YieldBox module | |
| [./contracts/TapiocaWrapper.sol](./contracts/TapiocaWrapper.sol) | 141 | TOFT create2 deployer | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/Balancer.sol](./contracts/Balancer.sol) | 223 | Contract that balance out a mTapiocaOFT supply | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts), [@rari-capital/solmate](https://github.com/transmissions11/solmate)  |
| [./contracts/tOFT/mTapiocaOFT.sol](./contracts/tOFT/mTapiocaOFT.sol) | 94 | Special TOFT implementation that can balance its supply | |
| [./contracts/tOFT/TapiocaOFT.sol](./contracts/tOFT/TapiocaOFT.sol) | 55 | OFTv2 compliant wrapped token, with new custom functions | |

## tap-token
| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [./contracts/Vesting.sol](./contracts/Vesting.sol) | 94 | Vesting contract | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [././contracts/twAML.sol](././contracts/twAML.sol) | 94 | Math library | |
| [./contracts/governance/twTAP.sol](./contracts/governance/twTAP.sol) | 341 | ONFT721 governance token | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/tokens/BaseTapOFT.sol](./contracts/tokens/BaseTapOFT.sol) | 264 | Base TapOFT contract | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/tokens/TapOFT.sol](./contracts/tokens/TapOFT.sol) | 127 | Tapioca protocol token | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/option-airdrop/aoTAP.sol](./contracts/option-airdrop/aoTAP.sol) | 88 | Forked version of oTAP | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/tokens/LTap.sol](./contracts/tokens/LTap.sol) | 30 | ERC20 aoTAP 1:1 redeemer | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/option-airdrop/AirdropBroker.sol](./contracts/option-airdrop/AirdropBroker.sol) | 338 | Smaller version of TapiocaOptionBroker to mint & exercise LTAP | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/options/oTAP.sol](./contracts/options/oTAP.sol) | 75 | ERC721 Option meta contract | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/options/TapiocaOptionLiquidityProvision.sol](./contracts/options/TapiocaOptionLiquidityProvision.sol) | 248 | Singularity ERC20 receipt token vault| [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/options/TapiocaOptionBroker.sol](./contracts/options/TapiocaOptionBroker.sol) | 398 | Mint & exercise oTAP | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |

## tapioca-periph
| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [./contracts/Magnetar/modules/MagnetarMarketModule.sol](./contracts/Magnetar/modules/MagnetarMarketModule.sol) | 656 | Magnetar Singularity module | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)  |
| [./contracts/Magnetar/MagnetarV2.sol](./contracts/Magnetar/MagnetarV2.sol) | 888 | Helper contract that interacts with Singularity, BigBang, TapiocaOptionBroker | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)  |
| [./contracts/Magnetar/MagnetarV2Storage.sol](./contracts/Magnetar/MagnetarV2Storage.sol) | 284 | Magnetar storage layout| [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity) |
| [./contracts/Swapper/BaseSwapper.sol](./contracts/Swapper/BaseSwapper.sol) | 151 | Base swapper contract for other swapper contract|  [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/Swapper/UniswapV3Swapper.sol](./contracts/Swapper/UniswapV3Swapper.sol) | 142 | UniV3 swapper contract|  [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts), [@uniswap/v3-core/](https://github.com/Uniswap/v3-core) [@uniswap/v3-periphery/](https://github.com/Uniswap/v3-periphery) |
| [./contracts/Swapper/UniswapV2Swapper.sol](./contracts/Swapper/UniswapV2Swapper.sol) | 121 | UniV2 swapper contract|  |
| [./contracts/Swapper/CurveSwapper.sol](./contracts/Swapper/CurveSwapper.sol) | 108 | Curve swapper contract | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/oracle/implementations/ARBTriCryptoOracle.sol](./contracts/oracle/implementations/ARBTriCryptoOracle.sol) | 87 | TriCrypto oracle | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts), [solady/](https://github.com/Vectorized/solady), [@chainlink/](https://github.com/smartcontractkit/chainlink) |
| [./contracts/Multicall/Multicall3.sol](./contracts/Multicall/Multicall3.sol) | 72 | Multicall contract | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/oracle/Seer.sol](./contracts/oracle/Seer.sol) | 66 | Oracle contract, uses best of ChainLink/UniV3 price feed | |
| [./contracts/oracle/implementations/SGOracle.sol](./contracts/oracle/implementations/SGOracle.sol) | 57 | Stargate finance oracle |[@chainlink/](https://github.com/smartcontractkit/chainlink) |
| [./contracts/TapiocaDeployer/TapiocaDeployer.sol](./contracts/TapiocaDeployer/TapiocaDeployer.sol) | 52 | Tapioca contract deployer|  |
| [./contracts/oracle/implementations/GLPOracle.sol](./contracts/oracle/implementations/GLPOracle.sol) | 38 | GLP Oracle| |


## tapioca-yieldbox-strats
| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [./contracts/convex/ConvexTricryptoStrategy.sol](./contracts/convex/ConvexTricryptoStrategy.sol) | 289 | TriCrypto strat |[@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)|
| [./contracts/glp/GlpStrategy.sol](./contracts/glp/GlpStrategy.sol) | 240 | GLP strat | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@uniswap/v3-core/](https://github.com/Uniswap/v3-core)|
| [./contracts/balancer/BalancerStrategy.sol](./contracts/balancer/BalancerStrategy.sol) | 208 | Balancer strat |[@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/stargate/StargateStrategy.sol](./contracts/stargate/StargateStrategy.sol) | 182 | Stargate strat | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/aave/AaveStrategy.sol](./contracts/aave/AaveStrategy.sol) | 190 | Stargate strat |  [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts) |
| [./contracts/curve/TricryptoLPStrategy.sol](./contracts/curve/TricryptoLPStrategy.sol) | 176 | TriCrypto LP strat | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)  |
| [./contracts/curve/TricryptoNativeStrategy.sol](./contracts/curve/TricryptoNativeStrategy.sol) | 175 | TriCrypto native strat |  [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)|
| [./contracts/lido/LidoEthStrategy.sol](./contracts/lido/LidoEthStrategy.sol) | 176 | TriCrypto LP strat | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)|
| [./contracts/compound/CompoundStrategy.sol](./contracts/compound/CompoundStrategy.sol) | 94 | Compound strat | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)|
| [./contracts/yearn/YearnStrategy.sol](./contracts/yearn/YearnStrategy.sol) | 82 | Yearn strat | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)|

## yieldbox
| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [./contracts/YieldBox.sol](./contracts/YieldBox.sol) | 263 | Main Yieldbox contract | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)  |
| [./contracts/YieldBoxURIBuilder.sol](./contracts/YieldBoxURIBuilder.sol) | 123 | Inherited by YieldBox | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity), [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)  |
| [./contracts/NativeTokenFactory.sol](./contracts/NativeTokenFactory.sol) | 72 | Creates ERC1155 tokens |  |
| [./contracts/YieldBoxPermit.sol](./contracts/YieldBoxPermit.sol) | 67 | EIP-2612 for YieldBox | [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)  |
| [./contracts/YieldBoxRebase.sol](./contracts/YieldBoxRebase.sol) | 64 | Math lib for internal accounting | [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity) |
| [./contracts/BoringMath.sol](./contracts/BoringMath.sol) | 26 | Simple math lib | |

## Out of scope

- [@boringcrypto/boring-solidity/contracts](https://github.com/boringcrypto/BoringSolidity)
- [@openzeppelin/contracts/](https://www.openzeppelin.com/contracts)
- [@chainlink/](https://github.com/smartcontractkit/chainlink)
- [solady/](https://github.com/Vectorized/solady)
- [@rari-capital/solmate](https://github.com/transmissions11/solmate)

# Additional Context

twAML is a simple model that is used in `twTAP` and `TapiocaOptionBroker`. A detailed explanation of how it works can be found [here](https://docs.tapioca.xyz/tapioca/core-technologies/twaml).

## Scoping Details 
```
- If you have a public code repo, please share it here:  https://github.com/Tapioca-DAO/Tapioca-bar https://github.com/Tapioca-DAO/tap-token https://github.com/Tapioca-DAO/TapiocaZ https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies https://github.com/Tapioca-DAO/YieldBox
- How many contracts are in scope?:   62
- Total SLoC for these contracts?:  13499
- How many external imports are there?: 15
- How many separate interfaces and struct definitions are there for the contracts within scope?:  50
- Does most of your code generally use composition or inheritance?:   Inheritance
- How many external calls?:   10
- What is the overall line coverage percentage provided by your tests?:  90
- Is there a need to understand a separate part of the codebase / get context in order to audit this part of the protocol?:  false 
- Please describe required context:   n/a
- Does it use an oracle?:  Custom oracle that may use Chainlink or UniV3 or best of Chainlink/UniV3
- Does the token conform to the ERC20 standard?:  True / also non-ERC20 token
- Are there any novel or unique curve logic or mathematical models?: twAML inherited contracts uses a math model that can be found on the page 5 of the paper https://www.tapioca.xyz/docs/twAML.pdf
- Does it use a timelock function?:  True
- Is it an NFT?: 
- Does it have an AMM?:   
- Is it a fork of a popular project?: True; Heavily modified version of Kashi lending & borrowing. It implements a new Permit system for both lending & borrowing actions, a new liquidation system and a module based architecture.
- Does it use rollups?:   
- Is it multi-chain?:  True
- Does it use a side-chain?: False
- Describe any specific areas you would like addressed. E.g. Please try to break XYZ.":
Tap-Token repo: 
Integrity of twAML model within the used contracts (TapiocaOptionBroker, TapiocaDAOPortal). 
Correct user participation and exit on twAML contracts (tOB, tDP). 
Proper OTC deal execution on tOB. 

Tapioca-Bar repo:
Lending & borrowing mechanism.
Function access with lend/borrow approval/permit.
Closed liquidations.

TapiocaZ repo:
mTapiocaOFT/Balancer contract balancing mechanism.
```

# Tests

## Hardhat tests:

### Setup
```
yarn
npx hardhat compile
```
### Test
```
npx hardhat test
```

### Gas cost
Set `enabled` key to `true` in `hardhat.export.ts>config.gasReporter`
```ts
  gasReporter: {
      enabled: true,
  }
```

# Notes
- The docs provide a lot of information about the protocol and the user flow, given the size of the protocol, we encourage checking it at https://docs.tapioca.xyz/tapioca/.
- `MagnetarV2` does not have access control by design. The underlying is the one that implement those (Can be found on `TOFT`, `Singularity`, `USDO`, `TapiocaOptionBroker`).
- Re-entrency on ownable contract should be considered as a vulnerability only if the last call leads to an external call with potential vulnerability.
