---
CIP: 23
Title: Fair Min Fees
Authors: Shawn McMurdo <shawn_mcmurdo@yahoo.com>
Comments-URI: https://forum.cardano.org/t/fair-min-fees-cip/47534
Status: Proposed
Type: Standards
Created: 2021-02-04
License: CC-BY-4.0
Requires: CIP-0009 Initial Protocol Parameters
---

## Simple Summary

Create a more fair marketplace for stakepools by reducing the minimum fixed pool fee and adding a minimum variable pool fee.

## Abstract

The current minimum fixed pool fee places a large and unfair burden on delegators to pools with smaller amounts of stake.
This incentivizes people to delegate to pools with higher stake causing centralization and creating an unequal playing field for stakepool operators.
Reducing the minimum fixed pool fee and adding a minimum variable pool fee reduces the imbalance between stakepools with less or more stake to a more reasonable range that allows for more fair competition between stakepools and more fair rewards for delegators to stakepools with less stake.

## Motivation

Reducing the minimum fixed pool fee and adding a minimum variable pool fee creates a more fair marketplace for all stakepool operators, gives delegators to pools with less stake more fair rewards, and increases decentralization, which is a goal of Cardano.

## Specification

```
| Name of the Parameter   | New Parameter (Y/N)  | Deleted Parameter (Y/N) | Proposed Value   | Summary Rationale for Change |
|-----------------------  |--------------------  |------------------------ |---------------   | ---------------------------- |
| minPoolCost             | N                    | N                       | 50000000         | See Rationale section.       |
|-----------------------  |--------------------  |------------------------ |---------------   | ---------------------------- |
| minPoolRate             | Y                    | N                       | .015             | See Rationale section.       |
|-----------------------  |--------------------  |------------------------ |---------------   | ---------------------------- |
```

## Rationale

The PHP code in minfees.php in the pull request allows exploration of the effects of choosing different values for the minimum fixed and variable fees.
Running minfees without any arguments gives the usage message as below.

```
php minfees.php

Usage: php minfees.php <min_fixed_fee> <min_rate_fee> <pledge>
  min_fixed_fee:  Minimum fixed fee in ADA.
               An integer greater than or equal to 0.
  min_rate_fee: Minimum rate fee in decimal. Example: 1.5% = .015
                    A real number greater than or equal to 0.
  pledge: Optional pledge amount in ADA. Defaults to 100000
                    A real number greater than or equal to 0.
```

Running minfees with the proposed values gives the following comparison of current and proposed pool operator and staker results at various pool stake levels.

```
php minfees.php 50 .015 100000

Reserve: 13b
Total stake: 32b
Tx fees: 0
Rewards available in epoch: 31.2m
Pool saturation: 64m
Pledge: 100k
Staker Delegation: 100k
Current Fixed Fee: 340
Current Rate: 0%
New Fixed Fee: 50
New Rate: 1.5%

                +---------- Current ----------+ +-------- Proposed ---------+
Pool    Total   Pool    Staker  Staker  Current Pool    Staker  Staker  New
Stake   Rewards Cur Fee Cur Fee Cur Rew Fee %   New Fee New Fee New Rew Fee %
2m      1501    340     17      58.1    22.7%   71.8    3.6     71.5    4.8%
5m      3752    340     6.8     68.2    9.1%    105.5   2.1     72.9    2.8%
10m     7503    340     3.4     71.6    4.5%    161.8   1.6     73.4    2.2%
20m     15007   340     1.7     73.3    2.3%    274.4   1.4     73.7    1.8%
30m     22511   340     1.1     73.9    1.5%    386.9   1.3     73.7    1.7%
64m     48023   340     0.5     74.5    0.7%    769.6   1.2     73.8    1.6%
```

Definitions:
Pool Stake - Total stake delegated to pool.
Total Rewards - Total rewards generated by the pool in one epoch.
Pool Cur Fee - The total amount of fees taken by the pool with current parameters.
Staker Cur Fee - The amount of fees paid by a staker who delegates 100k ADA  with currarameters.
Staker Cur Rew - The amount of rewards received by a staker who delegates 100k ADA  with currarameters.
Current Fee % - The percentage of rewards taken by the pool as fees  with currarameters.
Pool New Fee - The total amount of fees taken by the pool with proposed parameters.
Staker New Fee - The amount of fees paid by a staker who delegates 100k ADA with proposed parameters.
Staker New Rew - The amount of rewards received by a staker who delegates 100k ADA with proposed parameters.
New Fee % - The percentage of rewards taken by the pool as fees with proposed parameters.
Note: All amounts other than %s are in ADA.

The table above shows that currently a delegator staking 100k ADA to a stakepool with 2m ADA total delegation to the pool is paying an exorbitant 22.7% in fees while the same delegator staking with a fully saturated pool would only pay 0.7% in fees.
This is a substantial and unfair advantage that large pools have in the stakepool marketplace.
This is a strong incentive to centralize stake to fewer larger pools which reduces the resiliency of the network.

The proposed minimum fees bring this imbalance into a more reasonable range of 1.6% to 4.8%.
It is much more likely that a small stakepool with other advantages or selling points would be able to convince a delegator to accept about 2 less ADA in rewards per epoch for their 100k delegation than about 17 ADA as in the current case.
This is particularly true as the price of ADA increases.
At current price of $0.90 USD, a delegator staking 100k ADA is giving up over $1000 USD per year by delegating to a small pool!
This does not even include the amount lost by comounding rewards being staked over the year.

16.5 ADA/epoch * 73 epochs/year =  1204.5 ADA/year
1204.5 ADA/year * $0.90 USD/ADA = $1084.05 USD/year

With proposed parameters the same delegator would only be giving up about $150 USD per year to support a small pool.

2.3 ADA/epoch * 73 epochs/year =  167.9 ADA/year
167.9 ADA/year * $0.90 USD/ADA = $151.11 USD/year

The calculations below show that given the price increase in ADA compared to when the protocol parameters were first set, we can maintain viable funding for stakepool operators with the proposed parameter changes.

Annual pool operator funding given initial parameters:
340 ADA/epoch * $0.08 USD/ADA = $27.20 USD/epoch
$27.20 USD/epoch * 73 epochs/year = $1985.60 USD/year

Annual pool operator funding given proposed parameters for stakepool with 2 million ADA delegation:
71.8 ADA/epoch * $0.90 USD/ADA = $64.62 USD/epoch
$64.62 USD/epoch * 73 epochs/year = $4717.26 USD/year

Annual pool operator funding given proposed parameters for fully saturated stakepool:
769.6 ADA/epoch * $0.90 USD/ADA = $692.64 USD/epoch
$692.64 USD/epoch * 73 epochs/year = $50,562.72 USD/year

In summary, the proposed parameter changes would create a more fair marketplace for stakepools, provide more fair rewards for delegators to smaller pools and would lower incentives for centralization providing a more resilient network.

## Backward Compatibility

There are 2 ways to handle the existing pool certificates with minimum variable pool fee less than the proposed value:
1. These pool certificates would be treated as if they had the new mimimum variable pool fee.
2. These pool certificates would be invaalid and pool operators would be required to create new certificates with valid values.
If option 1 is possible, it would provide a better user experience for both stakepool operators and delegators and would cause less disruption to the network.

## Test Cases

See the minfees.php code to test different potential values of the parameters.

## Implementations

This would require new code to be written in the cardano-node to check the provided variable rate when creating pool certificates to make sure it meets the new requirements.

If backward compatibility option 1 is chosen it would require some new code in the cardano-node to treat the below minimum rate as the minimum rate.

## Copyright

This CIP is licensed under [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/legalcode)
