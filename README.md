# Non-Fungible Tokens for collectibles

This repo provides the reference codes for implementation of NFT smart-contracts that govern the behavior of collectible units.

This contracts represent a set of NFTs that contain recorded data in JSON format on-chain. NFTs can be grouped in "classes" to refer to class-related metadata which is shared among the NFTs belonging to one class (this optimizes on-chain data usage). User can also record his own metadata in text format to his owned NFT.

Owner of the contract can create new classes at the contract and assign them to a Sale system. Sales systems sell NFTs according to the chosen rules. There can be several types of sales.

Linear auctions allow users to buy NFTs at a fixed price stright from the contract.

Biddable auctions allow users to bid on NFTs and the highest bid wins after fixed time period.

ERC20 Sale System allow users to buy NFTs at a fixed price stright from the contract using an ERC20 or ERC223 token choosen by the contract owner.

Multicurrency Sale System allow users to buy NFTs at a fixed price, in USD, stright from the contract using several ERC20 or ERC223 tokens choosen by the contract owner.

# Structure

- [MetaverseNFT.sol](https://github.com/Dexaran/ImageNFT/blob/main/MetaverseNFT.sol) - Main NFT contract, it controls the Classes creation, NFT minting, Classes modifications, Second Hand Market and Minter Role

- [NFTMultiCurrencySaleSystem.sol](https://github.com/Dexaran/ImageNFT/blob/main/NFTMultiCurrencySaleSystem.sol) Sale system explained before.

- [NFTMulticlassBiddableAuction.sol](https://github.com/Dexaran/ImageNFT/blob/main/NFTMulticlassBiddableAuction.sol) - Auction system explained before.

- [NFTMulticlassERC20SaleSystem.sol](https://github.com/Dexaran/ImageNFT/blob/main/NFTMulticlassERC20SaleSystem.sol) - Sale system explained before.

- [NFTMulticlassLinearAuction.sol](https://github.com/Dexaran/ImageNFT/blob/main/NFTMulticlassLinearAuction.sol) - Sale system explained before.

- [Proxy.sol](https://github.com/Dexaran/ImageNFT/blob/main/Proxy.sol) - upgradeable proxy to keep contracts upgradeable during the development process.

# Deployment (contracts)

Solidity version 0.8.0. These contracts are designed to work with Ethereum Virtual Machine and therefore the contracts can be compiled and deployed to any chain that supports EVM including Binance Smart Chain, Ethereum CLassic, TRX, Callisto Network, PIRL etc.

Deployment process must be as follows:

1. Pick a contract and compile it (MetaverseNFT.sol) - the relevant contract is [contract MetaverseNFT](https://github.com/CallistoEnterprise/SC_MetaverseNFT/blob/main/MetaverseNFT.sol#L1018-L1063). Deploy the bytecode of the contract to a desired network (mainnet or testnet).
2. Compile the Proxy.sol - the relevant contract is [NFTUpgradeableProxy](https://github.com/CallistoEnterprise/SC_MetaverseNFT/blob/main/Proxy.sol#L458-L463)
3. Deploy the compiled Proxy contract and assign constructor parameters - `admin` is the owner address, `logic` is the address of the ImagesNFTv2 contract created at step 1, `data` is a special value that encodes the initialization call, data must be `b119490e000000000000000000000000000000000000000000000000000000000000006000000000000000000000000000000000000000000000000000000000000000a0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000074172746566696e0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000034152540000000000000000000000000000000000000000000000000000000000` in case of this particular contracts. Then  call [initialize()](https://github.com/CallistoEnterprise/SC_MetaverseNFT/blob/main/MetaverseNFT.sol#L1020-L1029) upon creation.

# Deployment (auctions)

1. Replace the hardcoded address of the revenue receiver with your address (it can be changed later with setRevenueAddress() function).
2. Compile any Sale or Auction contract listed above.
3. Deploy the compiled code to the desired network.
4. `Auction contract:` Configure the NFT contract with the setNFTContract function.
5. `NFT contract:` In order to be allowed to create NFTs the auction contract must be assigned the "Minter Role". Assign the minter permissions to the Auction contract address created at step 3 with the [setMinterRole](https://github.com/CallistoEnterprise/SC_MetaverseNFT/blob/main/MetaverseNFT.sol#L128-L146) function at the NFT contract.
6. `NFT contract:` Create a new NFT class that will represent a group of assets that can be sold at the auction contract with addNewTokenClass() function at the NFT contract.
7. `NFT contract:` (OPTIONAL) Assign class properties with addTokenClassProperties function.
8. `NFT contract:` (OPTIONAL) Configure the content of token properties with the modifyClassProperty function. NOTE: Properties can not be removed in the current implementation of the contract. However the owner can `modify` a token property to make it empty.
9. `Auction contract:` Assign a new auction at the auction contract with the createNFTAuction] function. `uint256 _classID` parameter must be the ID of created class at the auction contract. Auction can only sell assets of a created class. NOTE: One class can be only assigned to one type of auction. This contracts are not designed to allow one class of NFTs to be tradeable on multiple auctions at the same time.
