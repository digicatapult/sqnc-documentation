# Governance

This page describes some of the governance procedures within `sqnc-node`.

## Create a Vote

Assuming you are running a node and connected with [Polkadot JS Apps](https://polkadot.js.org/apps/) - in order to create a vote, select the `Governance` tab, then `Tech. Comm`. You will then be presented with a screen with the current Governance members.

To start a new vote select `Proposals`.

![Governance Tab](../assets/governance/1.png)

On the `Proposals` screen select `Submit proposal` on the right hand side. This will bring up a new proposal which must be made from an initial account, for example, here Alice is creating a proposal. You may notice the use of the doAs pallet, for more information visit [sqnc-node](https://github.com/digicatapult/sqnc-node/blob/main/README.md#doas-pallet).

![Committee Motion](../assets/governance/2.png)

Once the proposal has been agreed and submit has been clicked another window will appear to confirm the transaction.

![Vote on proposal](../assets/governance/3.png)

Click `Sign and submit`. There will then be a voting round.

![Vote on proposal](../assets/governance/4.png)

![Vote on proposal](../assets/governance/5.png)

Once the voting round has been completed the proposal needs to be closed to enter the chain.

![Close vote](../assets/governance/6.png)

You should see these for a successful vote.

![Successful vote](../assets/governance/7.png)

## Set Balance to 0

As we can see in the image below, Bob has nearly the same balance as Alice.

![Similar balances](../assets/governance/8.png)

By following the voting rules above we can chance a user's balance to 0.

![Vote to remove vote](../assets/governance/2.png)

![Balance After](../assets/governance/9.png)

## Can no longer transact

Now Bob has a balance of 0, if that party tries to create a proposal it will fail.

![Bob tries a vote](../assets/governance/10.png)

This error appears, indicating that the user is not able to create a transaction.

![Bob cannot start a transaction](../assets/governance/11.png)
