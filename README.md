# Band Protocol's Solidity Standard Reference Contracts

## Overview

This repository contains the Solidity code for Band Protocol's StdReference contracts. The live contract addresses can
be found in our [documentation](https://docs.bandchain.org/band-standard-dataset/supported-blockchains.html).

## Usage

To query prices from Band Protocol's StdReference contracts, the contract looking to use the price values should
reference Band Protocol's `StdReference` contract. This contract exposes the `getReferenceData`
and `getReferenceDataBulk` functions.

These can be imported via the `IStdReference` interface.

```solidity
import interfaces/IStdReference.sol
```

### ReferenceData

The `ReferenceData` struct is defined as:

```solidity
struct ReferenceData {
    uint256 rate;
    uint256 lastUpdatedBase;
    uint256 lastUpdatedQuote;
}
```

where the struct variables:

- `rate` is defined as the base/quote exchange rate multiplied by 1e18.
- `lastUpdatedBase` is defined as the UNIX epoch of the last time the base price was updated.
- `lastUpdatedQuote` is defined as the UNIX epoch of the last time the quote price was updated.

### getReferenceData

#### Input
- The base symbol as type `string`
- The quote symbol as type `string`

#### Output
- The base quote pair result as type `ReferenceData`

#### Example

For example, if we wanted to query the price of `BTC/USD`, the demo contract below shows how this can be done.

```solidity
import interfaces/IStdReference.sol

contract Demo {
    IStdReference public ref;

    constructor(IStdReference _ref) public {
        ref = _ref;
    }

    function demo() external view returns (IStdReference.ReferenceData memory) {
        return ref.getReferenceData("BTC", "USD");
    }
}
```

The result from `demo()` would yield:

```
ReferenceData(23131270000000000000000, 1659588229, 1659589497)
```

Where the results can be interpreted as:

- BTC/USD
    - `rate = 23131.27 BTC/USD`
    - `lastUpdatedBase = 1659588229`
    - `lastUpdatedQuote = 1659589497`

### getReferenceDataBulk

`getReferenceDataBulk` takes two lists as the inputs, the base and quote symbols. The return value is `ReferenceData[]`.

#### Input
- An array of base symbols as type `string[]`
- An array of quote symbol as type `string[]`

#### Output
- An array of the base quote pair results as type `ReferenceData[]`

#### Example

For example, if we wanted to query the price of `BTC/USD` and `ETH/BTC`, the demo contract below shows how this can be
done.

```solidity
import interfaces/IStdReference.sol

contract DemoBulk {
    IStdReference public ref;

    constructor(IStdReference _ref) public {
        ref = _ref;
    }

    function demo_bulk() external view returns (IStdReference.ReferenceData[] memory) {
        return ref.getReferenceDataBulk(["BTC","ETH"], ["USD","BTC"]);
    }
}
```

The result from `demo_bulk()` would yield:

```
[
    ReferenceData(23131270000000000000000, 1659588229, 1659589497),
    ReferenceData(71601775432131482, 1659588229, 1659588229)
]
```

Where the results can be interpreted as:

- BTC/USD
    - `rate = 23131.27 BTC/USD`
    - `lastUpdatedBase = 1659588229`
    - `lastUpdatedQuote = 1659589497`
- ETH/BTC
    - `rate = 0.07160177543213148 ETH/BTC`
    - `lastUpdatedBase = 1659588229`
    - `lastUpdatedQuote = 1659588229`
