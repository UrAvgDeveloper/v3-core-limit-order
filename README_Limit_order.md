# Uniswap V3 with Native Limit Orders

This project is a fork of [Uniswap V3](https://github.com/Uniswap/v3-core) with the implementation of native limit orders. The current Uniswap V3 system allows users to provide liquidity at a single tick but requires manual removal once the position is filled. Native limit orders automate this process, removing the liquidity position automatically when it is filled, preventing it from being unfilled if the price moves back across the tick.

## Introduction

Uniswap V3 is a popular decentralized exchange protocol that allows users to provide liquidity within specified price ranges or "ticks." However, managing liquidity manually can be cumbersome, and users may miss opportunities to remove liquidity when a position is filled. Native limit orders solve this problem by introducing three new functions:

1. `createLimitOrder(address recipient, int24 tickLower, uint128 amount)`: This function creates a limit order for the specified recipient, targeting a price range between `tickLower` and `tickLower + 1`. Users can specify the amount they want to provide as liquidity.

2. `cancelLimitOrder(address recipient, int24 tickLower)`: Users can use this function to cancel their limit order. If the order is not yet filled, it returns the unfilled portion (and the swapped amount, in the case of partial fills) to the user. Cancellation is disallowed if the order is already filled.

3. `collectLimitOrder(address recipient, int24 tickLower)`: Once the limit order is filled, users can collect the swapped amount using this function. Collection is disallowed if the order is not filled or only partially filled.

## Working

### Minimal Code Changes

Have made minimal changes to the existing Uniswap V3 codebase to integrate these features seamlessly except current swap implementation, ensuring compatibility with the core protocol. To ensure this works fluently, the pool contract is the contract having owner of positions of all limit order and mainitainingmost info above the current working protocol.

### Changes to swap implementation

The swap implementation that exists uses a loop to iterate over ticks until the specified amount of swap is used, So the only change that has been done to code is that if tick is crossed, liquidate its position owned by the contract. Hence automating the process of removing liquidity specified in problem statement. And tokens can be collected.

## Improvements

There are several ways how this code can be improved, some of them are:
- Move the info related to tick to library and inside its info struct.
- Current contract exceeds maximum size supported by ethereum, reduce it.
- Move most code to library.
- In current implementation to create limit order you have to approve pool contract, which is not optimal, which needs to be moved to periphery.
- Imrove gas efficiency.
- Update tests according to new deployment costs and gas usage. 


## Run tests for limit orders

After install all requied libraries are installed run:

    ```bash
    yarn hardhat test test/LimitOrder.ts
    ```