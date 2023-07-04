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

The main repository is [tapioca-bar](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668), which contains [USDO](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668contracts/usd0/USDO.sol), a stablecoin. [BigBang](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668contracts/markets/bigBang/BigBang.sol), a CDP based contract that mint and burn `USDO`. And [Singularity](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/2286f80f928f41c8bc189d0657d74ba83286c668contracts/markets/singularity/Singularity.sol), a lending and borrowing platform.

The other repos are here to support the ecosystem as well as to create a synergy between the tokenemics and the protocol features.

* [tap-token](https://github.com/Tapioca-DAO/tap-token-audit/tree/59749be5bc2286f0bdbf59d7ddc258ddafd49a9f) Contracts related to the tokenemics, is linked to `tapioca-bar` in an asymmetric way.
* [tapiocaz](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/bcf61f79464cfdc0484aa272f9f6e28d5de36a8f) Contracts that contains a wrapper named `TOFT`, which is used to wrap gas tokens and transfer allow their usage through the LayerZero network.
* [tapioca-periph](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3) Periphery contracts. The main contract is `MagnetarV2`, acts as a helper that reduce the number of user taken actions/transactions.
* [YieldBox](https://github.com/Tapioca-DAO/YieldBox/tree/f5ad271b2dcab8b643b7cf622c2d6a128e109999) A "BentoBox v2". Acts as a vault, that allow for yield strategies to be applied on the asset.
* [yieldbox-strategies](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/05ba7108a83c66dada98bc5bc75cf18004f2a49b) Yield strategies that will be used by a YieldBox asset.

![tapioca-userflow](https://github.com/code-423n4/2023-07-tapioca/blob/main/tapioca-userflow.jpg)


# Notes
- The docs provide a lot of information about the protocol and the user flow, given the size of the protocol, we encourage checking it at https://docs.tapioca.xyz/tapioca/.
- `MagnetarV2` does not have access control by design. The underlying is the one that implement those (Can be found on `TOFT`, `Singularity`, `USDO`, `TapiocaOptionBroker`).
- Re-entrency on ownable contract should be considered as a vulnerability only if the last call leads to an external call with potential vulnerability.


# Files in scope
|File|[SLOC](#nowhere "(nSLOC, SLOC, Lines)")|Description|Libraries|
|:-|:-:|:-|:-|
|_Contracts (17)_|
|[tapioca-bar-audit/contracts/markets/singularity/SGLCollateral.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCollateral.sol)|[22](#nowhere "(nSLOC:12, SLOC:22, Lines:42)")|Singularity collateral module||
|[tapioca-bar-audit/contracts/markets/singularity/SGLBorrow.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLBorrow.sol)|[31](#nowhere "(nSLOC:22, SLOC:31, Lines:57)")|Singularity borrowing module||
|[tapioca-bar-audit/contracts/usd0/BaseUSDOStorage.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDOStorage.sol) [🖥](#nowhere "Uses Assembly") [💰](#nowhere "Payable Functions") [🧮](#nowhere "Uses Hash-Functions")|[56](#nowhere "(nSLOC:54, SLOC:56, Lines:99)")|Base USDO contract| `tapioca-sdk/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapioca-bar-audit/contracts/usd0/USDO.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/USDO.sol)|[69](#nowhere "(nSLOC:61, SLOC:69, Lines:123)")|USDO stablecoin| `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapioca-bar-audit/contracts/markets/singularity/SGLLendingCommon.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLendingCommon.sol) [📤](#nowhere "Initiates ETH Value Transfer")|[78](#nowhere "(nSLOC:59, SLOC:78, Lines:99)")|Singularity base contract||
|[tapioca-bar-audit/contracts/markets/singularity/SGLStorage.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLStorage.sol)|[121](#nowhere "(nSLOC:121, SLOC:121, Lines:201)")|Singularity storage layout| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3) `tapioca-sdk/*`|
|[tapioca-bar-audit/contracts/markets/singularity/SGLLeverage.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLeverage.sol) [💰](#nowhere "Payable Functions") [📤](#nowhere "Initiates ETH Value Transfer")|[135](#nowhere "(nSLOC:109, SLOC:135, Lines:187)")|Singularity module for leverage| [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapioca-bar-audit/contracts/markets/MarketERC20.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/MarketERC20.sol) [🧮](#nowhere "Uses Hash-Functions")|[181](#nowhere "(nSLOC:137, SLOC:181, Lines:299)")|Base contract for Market.sol| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tapioca-bar-audit/contracts/markets/singularity/SGLCommon.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLCommon.sol) [📤](#nowhere "Initiates ETH Value Transfer")|[209](#nowhere "(nSLOC:171, SLOC:209, Lines:259)")|Singularity base contract||
|[tapioca-bar-audit/contracts/usd0/modules/USDOMarketModule.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOMarketModule.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall") [♻️](#nowhere "TryCatch Blocks") [Σ](#nowhere "Unchecked Blocks")|[268](#nowhere "(nSLOC:239, SLOC:268, Lines:299)")|USDO Module for Singularity| `tapioca-sdk/*` [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapioca-bar-audit/contracts/usd0/modules/USDOOptionsModule.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOOptionsModule.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall") [♻️](#nowhere "TryCatch Blocks") [Σ](#nowhere "Unchecked Blocks")|[269](#nowhere "(nSLOC:240, SLOC:269, Lines:300)")|USDO Module for TapiocaBrokerOption.sol calls| `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapioca-bar-audit/contracts/usd0/modules/USDOLeverageModule.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/modules/USDOLeverageModule.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall") [♻️](#nowhere "TryCatch Blocks") [Σ](#nowhere "Unchecked Blocks")|[277](#nowhere "(nSLOC:250, SLOC:277, Lines:310)")|USDO Module for leverage| `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapioca-bar-audit/contracts/markets/singularity/SGLLiquidation.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/SGLLiquidation.sol) [📤](#nowhere "Initiates ETH Value Transfer")|[288](#nowhere "(nSLOC:248, SLOC:288, Lines:365)")|Singularity module for liquidations||
|[tapioca-bar-audit/contracts/usd0/BaseUSDO.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/usd0/BaseUSDO.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall")|[358](#nowhere "(nSLOC:295, SLOC:358, Lines:502)")|Custom LayerZero OFT logic, inherited in USDO| `tapioca-sdk/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapioca-bar-audit/contracts/Penrose.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/Penrose.sol) [🖥](#nowhere "Uses Assembly") [💰](#nowhere "Payable Functions") [📤](#nowhere "Initiates ETH Value Transfer") [🌀](#nowhere "create/create2") [Σ](#nowhere "Unchecked Blocks")|[377](#nowhere "(nSLOC:316, SLOC:377, Lines:578)")|Owner contract for USDO & BB| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapioca-bar-audit/contracts/markets/singularity/Singularity.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/singularity/Singularity.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall")|[452](#nowhere "(nSLOC:367, SLOC:452, Lines:644)")|Lending & borrowing| [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3) `tapioca-sdk/*`|
|[tapioca-bar-audit/contracts/markets/bigBang/BigBang.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/bigBang/BigBang.sol) [📤](#nowhere "Initiates ETH Value Transfer") [👥](#nowhere "DelegateCall")|[555](#nowhere "(nSLOC:451, SLOC:555, Lines:792)")|Mint and burn USDO through CDP| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|_Abstracts (1)_|
|[tapioca-bar-audit/contracts/markets/Market.sol](https://github.com/Tapioca-DAO/tapioca-bar-audit/tree/master/contracts/markets/Market.sol) [🖥](#nowhere "Uses Assembly")|[316](#nowhere "(nSLOC:267, SLOC:316, Lines:468)")|Base contract for BigBang & Singularity| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|Total (over 18 files):| [4062](#nowhere "(nSLOC:3419, SLOC:4062, Lines:5624)") ||

|File|[SLOC](#nowhere "(nSLOC, SLOC, Lines)")|Description|Libraries|
|:-|:-:|:-|:-|
|_Contracts (10)_|
|[tapiocaz-audit/contracts/tOFT/TapiocaOFT.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/TapiocaOFT.sol) [💰](#nowhere "Payable Functions")|[50](#nowhere "(nSLOC:43, SLOC:50, Lines:91)")|OFTv2 compliant wrapped token, with new custom functions||
|[tapiocaz-audit/contracts/tOFT/BaseTOFTStorage.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFTStorage.sol) [🖥](#nowhere "Uses Assembly") [💰](#nowhere "Payable Functions")|[55](#nowhere "(nSLOC:53, SLOC:55, Lines:79)")|Base TOFT EVM storage layout| `tapioca-sdk/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapiocaz-audit/contracts/tOFT/mTapiocaOFT.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/mTapiocaOFT.sol) [💰](#nowhere "Payable Functions")|[94](#nowhere "(nSLOC:84, SLOC:94, Lines:153)")|Special TOFT implementation that can balance its supply||
|[tapiocaz-audit/contracts/TapiocaWrapper.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/TapiocaWrapper.sol) [💰](#nowhere "Payable Functions") [🧮](#nowhere "Uses Hash-Functions")|[141](#nowhere "(nSLOC:120, SLOC:141, Lines:229)")|TOFT create2 deployer| [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3) [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tapiocaz-audit/contracts/tOFT/modules/BaseTOFTStrategyModule.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTStrategyModule.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall")|[203](#nowhere "(nSLOC:164, SLOC:203, Lines:249)")|Base TOFT YieldBox module| `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapiocaz-audit/contracts/Balancer.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/Balancer.sol) [💰](#nowhere "Payable Functions")|[223](#nowhere "(nSLOC:181, SLOC:223, Lines:343)")|Contract that balance out a mTapiocaOFT supply| [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3) `solmate/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tapiocaz-audit/contracts/tOFT/modules/BaseTOFTMarketModule.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTMarketModule.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall") [♻️](#nowhere "TryCatch Blocks") [Σ](#nowhere "Unchecked Blocks")|[276](#nowhere "(nSLOC:247, SLOC:276, Lines:316)")|Base TOFT Singularity market module| `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapiocaz-audit/contracts/tOFT/modules/BaseTOFTOptionsModule.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTOptionsModule.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall") [♻️](#nowhere "TryCatch Blocks") [Σ](#nowhere "Unchecked Blocks")|[283](#nowhere "(nSLOC:254, SLOC:283, Lines:315)")|Base TOFT TapiocaOptionBroker market module| `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapiocaz-audit/contracts/tOFT/modules/BaseTOFTLeverageModule.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/modules/BaseTOFTLeverageModule.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall") [♻️](#nowhere "TryCatch Blocks") [Σ](#nowhere "Unchecked Blocks")|[305](#nowhere "(nSLOC:279, SLOC:305, Lines:340)")|Base TOFT leverage module| `tapioca-sdk/*` [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tapiocaz-audit/contracts/tOFT/BaseTOFT.sol](https://github.com/Tapioca-DAO/tapiocaz-audit/tree/master/contracts/tOFT/BaseTOFT.sol) [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall")|[455](#nowhere "(nSLOC:373, SLOC:455, Lines:574)")|Base TOFT contract||
|Total (over 10 files):| [2085](#nowhere "(nSLOC:1798, SLOC:2085, Lines:2689)") ||

|File|[SLOC](#nowhere "(nSLOC, SLOC, Lines)")|Description|Libraries|
|:-|:-:|:-|:-|
|_Contracts (9)_|
|[tap-token-audit/contracts/tokens/LTap.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/LTap.sol) [📤](#nowhere "Initiates ETH Value Transfer")|[30](#nowhere "(nSLOC:30, SLOC:30, Lines:57)")|ERC20 aoTAP 1:1 redeemer| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tap-token-audit/contracts/options/oTAP.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/oTAP.sol)|[75](#nowhere "(nSLOC:63, SLOC:75, Lines:130)")|ERC721 Option meta contract| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) `tapioca-sdk/*`|
|[tap-token-audit/contracts/option-airdrop/aoTAP.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/aoTAP.sol)|[88](#nowhere "(nSLOC:76, SLOC:88, Lines:143)")|Forked version of oTAP| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) `tapioca-sdk/*`|
|[tap-token-audit/contracts/Vesting.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/Vesting.sol)|[94](#nowhere "(nSLOC:94, SLOC:94, Lines:174)")|Vesting contract| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity)|
|[tap-token-audit/contracts/tokens/TapOFT.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/TapOFT.sol)|[127](#nowhere "(nSLOC:121, SLOC:127, Lines:256)")|Tapioca protocol token| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tap-token-audit/contracts/options/TapiocaOptionLiquidityProvision.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionLiquidityProvision.sol) [📤](#nowhere "Initiates ETH Value Transfer") [🧮](#nowhere "Uses Hash-Functions") [Σ](#nowhere "Unchecked Blocks")|[248](#nowhere "(nSLOC:213, SLOC:248, Lines:371)")|Singularity ERC20 receipt token vault| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) `tapioca-sdk/*`|
|[tap-token-audit/contracts/option-airdrop/AirdropBroker.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/option-airdrop/AirdropBroker.sol) [📤](#nowhere "Initiates ETH Value Transfer") [🧮](#nowhere "Uses Hash-Functions") [Σ](#nowhere "Unchecked Blocks")|[338](#nowhere "(nSLOC:289, SLOC:338, Lines:537)")|Smaller version of TapiocaOptionBroker to mint & exercise LTAP| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|[tap-token-audit/contracts/governance/twTAP.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/governance/twTAP.sol) [🖥](#nowhere "Uses Assembly") [📤](#nowhere "Initiates ETH Value Transfer") [Σ](#nowhere "Unchecked Blocks")|[341](#nowhere "(nSLOC:313, SLOC:341, Lines:587)")|ONFT721 governance token| `tapioca-sdk/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tap-token-audit/contracts/options/TapiocaOptionBroker.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/options/TapiocaOptionBroker.sol) [📤](#nowhere "Initiates ETH Value Transfer") [Σ](#nowhere "Unchecked Blocks")|[398](#nowhere "(nSLOC:359, SLOC:398, Lines:578)")|Mint & exercise oTAP| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|_Abstracts (2)_|
|[tap-token-audit/contracts/twAML.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/twAML.sol) [🖥](#nowhere "Uses Assembly")|[94](#nowhere "(nSLOC:79, SLOC:94, Lines:159)")|Math library||
|[tap-token-audit/contracts/tokens/BaseTapOFT.sol](https://github.com/Tapioca-DAO/tap-token-audit/tree/master/contracts/tokens/BaseTapOFT.sol) [💰](#nowhere "Payable Functions") [♻️](#nowhere "TryCatch Blocks") [Σ](#nowhere "Unchecked Blocks")|[264](#nowhere "(nSLOC:228, SLOC:264, Lines:355)")|Base TapOFT contract| `tapioca-sdk/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`tapioca-periph/*`](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/a3b45512580f8a76be45c19f635689f48c0128c3)|
|Total (over 11 files):| [2097](#nowhere "(nSLOC:1865, SLOC:2097, Lines:3347)") ||

|File|[SLOC](#nowhere "(nSLOC, SLOC, Lines)")|Description|Libraries|
|:-|:-:|:-|:-|
|_Contracts (12)_|
|[tapioca-periph-audit/contracts/oracle/implementations/GLPOracle.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/GLPOracle.sol)|[38](#nowhere "(nSLOC:30, SLOC:38, Lines:57)")|GLP Oracle||
|[tapioca-periph-audit/contracts/TapiocaDeployer/TapiocaDeployer.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/TapiocaDeployer/TapiocaDeployer.sol) [🖥](#nowhere "Uses Assembly") [💰](#nowhere "Payable Functions") [🌀](#nowhere "create/create2")|[52](#nowhere "(nSLOC:40, SLOC:52, Lines:94)")|Tapioca contract deployer||
|[tapioca-periph-audit/contracts/oracle/implementations/SGOracle.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/SGOracle.sol)|[57](#nowhere "(nSLOC:51, SLOC:57, Lines:103)")|Stargate finance oracle| `@chainlink/*`|
|[tapioca-periph-audit/contracts/oracle/Seer.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/Seer.sol)|[66](#nowhere "(nSLOC:60, SLOC:66, Lines:97)")|Oracle contract, uses best of ChainLink/UniV3 price feed||
|[tapioca-periph-audit/contracts/Multicall/Multicall3.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Multicall/Multicall3.sol) [🖥](#nowhere "Uses Assembly") [💰](#nowhere "Payable Functions") [Σ](#nowhere "Unchecked Blocks")|[72](#nowhere "(nSLOC:68, SLOC:72, Lines:104)")|Multicall contract| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tapioca-periph-audit/contracts/oracle/implementations/ARBTriCryptoOracle.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/oracle/implementations/ARBTriCryptoOracle.sol)|[87](#nowhere "(nSLOC:77, SLOC:87, Lines:143)")|TriCrypto oracle| `@chainlink/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) `solady/*`|
|[tapioca-periph-audit/contracts/Swapper/CurveSwapper.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/CurveSwapper.sol)|[108](#nowhere "(nSLOC:87, SLOC:108, Lines:168)")|Curve swapper contract| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tapioca-periph-audit/contracts/Swapper/UniswapV2Swapper.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV2Swapper.sol)|[121](#nowhere "(nSLOC:100, SLOC:121, Lines:172)")|UniV2 swapper contract||
|[tapioca-periph-audit/contracts/Swapper/UniswapV3Swapper.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/UniswapV3Swapper.sol)|[142](#nowhere "(nSLOC:126, SLOC:142, Lines:207)")|UniV3 swapper contract| `@uniswap/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tapioca-periph-audit/contracts/Magnetar/MagnetarV2Storage.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2Storage.sol) [💰](#nowhere "Payable Functions")|[284](#nowhere "(nSLOC:284, SLOC:284, Lines:341)")|Magnetar storage layout| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-periph-audit/contracts/Magnetar/modules/MagnetarMarketModule.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/modules/MagnetarMarketModule.sol) [💰](#nowhere "Payable Functions") [📤](#nowhere "Initiates ETH Value Transfer") [♻️](#nowhere "TryCatch Blocks")|[676](#nowhere "(nSLOC:580, SLOC:676, Lines:799)")|Magnetar Singularity module| `tapioca-sdk/*` [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|[tapioca-periph-audit/contracts/Magnetar/MagnetarV2.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Magnetar/MagnetarV2.sol) [🖥](#nowhere "Uses Assembly") [💰](#nowhere "Payable Functions") [👥](#nowhere "DelegateCall") [Σ](#nowhere "Unchecked Blocks")|[866](#nowhere "(nSLOC:782, SLOC:866, Lines:1088)")|Helper contract that interacts with Singularity, BigBang, TapiocaOptionBroker| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|_Abstracts (1)_|
|[tapioca-periph-audit/contracts/Swapper/BaseSwapper.sol](https://github.com/Tapioca-DAO/tapioca-periph-audit/tree/master/contracts/Swapper/BaseSwapper.sol)|[151](#nowhere "(nSLOC:110, SLOC:151, Lines:174)")|Base swapper contract for other swapper contract| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) `tapioca-sdk/*`|
|Total (over 13 files):| [2720](#nowhere "(nSLOC:2395, SLOC:2720, Lines:3547)") ||

|File|[SLOC](#nowhere "(nSLOC, SLOC, Lines)")|Description|Libraries|
|:-|:-:|:-|:-|
|_Contracts (10)_|
|[tapioca-yieldbox-strategies-audit/contracts/yearn/YearnStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/yearn/YearnStrategy.sol)|[82](#nowhere "(nSLOC:74, SLOC:82, Lines:151)")|Yearn strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/compound/CompoundStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/compound/CompoundStrategy.sol) [💰](#nowhere "Payable Functions")|[94](#nowhere "(nSLOC:86, SLOC:94, Lines:165)")|Compound strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/lido/LidoEthStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/lido/LidoEthStrategy.sol) [💰](#nowhere "Payable Functions")|[100](#nowhere "(nSLOC:92, SLOC:100, Lines:170)")|TriCrypto LP strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoNativeStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoNativeStrategy.sol)|[175](#nowhere "(nSLOC:167, SLOC:175, Lines:253)")|TriCrypto native strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/curve/TricryptoLPStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/curve/TricryptoLPStrategy.sol)|[176](#nowhere "(nSLOC:168, SLOC:176, Lines:255)")|TriCrypto LP strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/stargate/StargateStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/stargate/StargateStrategy.sol) [💰](#nowhere "Payable Functions")|[182](#nowhere "(nSLOC:174, SLOC:182, Lines:272)")|Stargate strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/aave/AaveStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/aave/AaveStrategy.sol)|[190](#nowhere "(nSLOC:182, SLOC:190, Lines:276)")|Stargate strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/balancer/BalancerStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/balancer/BalancerStrategy.sol) [💰](#nowhere "Payable Functions")|[208](#nowhere "(nSLOC:200, SLOC:208, Lines:302)")|Balancer strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/glp/GlpStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/glp/GlpStrategy.sol) [🧪](#nowhere "Experimental Features") [♻️](#nowhere "TryCatch Blocks")|[240](#nowhere "(nSLOC:232, SLOC:240, Lines:335)")|GLP strat| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `@uniswap/*` `tapioca-sdk/*`|
|[tapioca-yieldbox-strategies-audit/contracts/convex/ConvexTricryptoStrategy.sol](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies-audit/tree/master/contracts/convex/ConvexTricryptoStrategy.sol) [🧮](#nowhere "Uses Hash-Functions")|[289](#nowhere "(nSLOC:279, SLOC:289, Lines:381)")|TriCrypto strat| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) `tapioca-sdk/*`|
|Total (over 10 files):| [1736](#nowhere "(nSLOC:1654, SLOC:1736, Lines:2560)") ||

|File|[SLOC](#nowhere "(nSLOC, SLOC, Lines)")|Description|Libraries|
|:-|:-:|:-|:-|
|_Contracts (3)_|
|[YieldBox/contracts/NativeTokenFactory.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/NativeTokenFactory.sol)|[72](#nowhere "(nSLOC:72, SLOC:72, Lines:146)")|Creates ERC1155 tokens||
|[YieldBox/contracts/YieldBoxURIBuilder.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxURIBuilder.sol)|[123](#nowhere "(nSLOC:118, SLOC:123, Lines:136)")|Inherited by YieldBox| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts) [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity)|
|[YieldBox/contracts/YieldBox.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBox.sol) [🧪](#nowhere "Experimental Features") [💰](#nowhere "Payable Functions")|[263](#nowhere "(nSLOC:226, SLOC:263, Lines:503)")|Main Yieldbox contract| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity) [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|_Abstracts (1)_|
|[YieldBox/contracts/YieldBoxPermit.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxPermit.sol) [🧮](#nowhere "Uses Hash-Functions")|[67](#nowhere "(nSLOC:43, SLOC:67, Lines:122)")|EIP-2612 for YieldBox| [`@openzeppelin/*`](https://www.openzeppelin.com/contracts)|
|_Libraries (2)_|
|[YieldBox/contracts/BoringMath.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/BoringMath.sol)|[26](#nowhere "(nSLOC:21, SLOC:26, Lines:31)")|Simple math lib||
|[YieldBox/contracts/YieldBoxRebase.sol](https://github.com/Tapioca-DAO/Yieldbox/tree/master/contracts/YieldBoxRebase.sol) [🧪](#nowhere "Experimental Features")|[40](#nowhere "(nSLOC:30, SLOC:40, Lines:62)")|Math lib for internal accounting| [`@boringcrypto/*`](https://github.com/boringcrypto/BoringSolidity)|
|Total (over 6 files):| [591](#nowhere "(nSLOC:510, SLOC:591, Lines:1000)") ||


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

# QuickStart
```
export ALCHEMY_API_KEY="<your-alchemy-api-key>" && export PRIVATE_KEY="ae330c71c0930902aae1bdabdca36457e5b92a095c8ad171fd3ae6519961cc2a" && rm -Rf 2023-07-tapioca || true && gc https://github.com/code-423n4/2023-07-tapioca.git -j8 && cd 2023-07-tapioca && git submodule update --init && nvm install 18.0 && cd tapiocaz-audit && git submodule update --init && yarn && npx hardhat compile && cd .. && cd YieldBox && git submodule update --init && yarn && npx hardhat compile && cd .. && cd tapioca-bar-audit && git submodule update --init && yarn && npx hardhat compile && cd .. && cd tapioca-periph-audit && git submodule update --init && yarn && npx hardhat compile && cd .. && cd tapioca-yieldbox-strategies-audit && git submodule update --init && yarn && npx hardhat compile && cd .. && cd tap-token-audit && git submodule update --init && yarn && npx hardhat compile && cd ..  && cd tapiocaz-audit && REPORT_GAS=true npx hardhat test && cd .. && cd YieldBox && REPORT_GAS=true npx hardhat test && cd .. && cd tapioca-bar-audit && REPORT_GAS=true npx hardhat test && cd .. && cd tapioca-periph-audit && export BINANCE_WALLET_ADDRESS=0x28C6c06298d514Db089934071355E5743bf21d60 && export UniswapV2Router02=0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D && export UniswapV2Factory=0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f && export UniswapV3Router=0xE592427A0AEce92De3Edee1F18E0157C05861564 && export UniswapV3Factory=0x1F98431c8aD98523631AE4a59f267346ea31F984 && export Curve3Pool=0xbebc44782c7db0a1a60cb6fe97d0b483032ff1c7 && export USDT=0xdac17f958d2ee523a2206206994597c13d831ec7 && export USDC=0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48 && export WETH=0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2 && REPORT_GAS=true npx hardhat test && cd .. && cd tapioca-yieldbox-strategies-audit && REPORT_GAS=true NODE_ENV=mainnet npx hardhat test && cd .. && cd tap-token-audit && REPORT_GAS=true npx hardhat test && cd ..
```
# Tests
Some tests are skipped, either because it requires a specific chain to be on (Some tests might run solely on Mainnet, while others on Arbitrum). Others are skipped due to being there for helping purposes, or being too old but aren't cleaned.

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

# Slither
Slither does not currently work on `tapioca-periph-audit` repo. If you find a workaround, please share in the discord.

# Coverage
Coverage is broken for some repos because we use `IR` compilation. Disabling it might output a stack too deep compilation error.