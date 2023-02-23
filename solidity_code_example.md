# Solidity Safety example

## There is a vulnerability in this code:


```solidity

    /**
    * @notice A method that adds to the rewards for each stakeholder when MINTING payment is done.
    */
    function addPaymentToBnUSDRewards(uint _payment) external {
        uint _totalStakes = totalStakes() / 10 ** 18;

        // calculate cut per staked token * 1 ether to avoid decimals
        uint cutPerStakedToken =  _payment * 1 ether / (_totalStakes * 1 ether);
        
        // add remaining percent to each stakeholders credit:
        for (uint256 s = 0; s < stakeholders.length; s += 1){
            // then the cut per staked token is multiplied by the stake of the stakeholder and added to stakeholder's rewards.
            bnusd_rewards[stakeholders[s]] += (cutPerStakedToken * stakes[stakeholders[s]]) / 1 ether;
        }

        // emit payment event
        emit Payment(_payment);
    }
 

```



## Solution

The issue above is that anyone can call this function! So anyone can add a "fake payment" to the staking contract. Below we added the rule that makes sure only the Blobble Maincontract can call this function.

```solidity

    /**
    * @notice A method that adds to the rewards for each stakeholder when MINTING payment is done.
    */
    function addPaymentToBnUSDRewards(uint _payment) external {
        require(msg.sender == mainContract, 'not mainContract');
        
        uint _totalStakes = totalStakes() / 10 ** 18;

        // calculate cut per staked token * 1 ether to avoid decimals
        uint cutPerStakedToken =  _payment * 1 ether / (_totalStakes * 1 ether);
        
        // add remaining percent to each stakeholders credit:
        for (uint256 s = 0; s < stakeholders.length; s += 1){
            // then the cut per staked token is multiplied by the stake of the stakeholder and added to stakeholder's rewards.
            bnusd_rewards[stakeholders[s]] += (cutPerStakedToken * stakes[stakeholders[s]]) / 1 ether;
        }

        // emit payment event
        emit Payment(_payment);
    }
 

```
