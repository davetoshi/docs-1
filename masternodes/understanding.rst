.. meta::
   :description: Explanation of how Axe masternodes work in theory and practice to support InstantSend, PrivateSend and governance
   :keywords: axe, masternodes, hosting, linux, payment, instantsend, privatesend, governance, quorum, evolution, bls, 

.. _understanding_masternodes:

=========================
Understanding Masternodes
=========================

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; margin-bottom: 1em; overflow: hidden; max-width: 70%; height: auto;">
        <iframe src="//www.youtube.com/embed/4GRrLiTCq5M" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe>
    </div>

Masternodes, once unique to the Axe network, are now becoming popular
as the technology is forked into other blockchains. This section of the
documentation describes the principles and mechanisms of masternodes and
the services they provide to the Axe network specifically.

Simply put, a masternode is a server with a full copy of the Axe
blockchain, which guarantees a certain minimum level of performance and
functionality to perform certain tasks related to block validation, as
well as PrivateSend and InstantSend, as the anonymity and instant
transaction features in Axe are called. The masternodes are paid for
this service, using a concept known as Proof of Service. This is in
addition to the Proof of Work done by miners to secure the blockchain.
Masternodes are also allowed to vote on :ref:`governance and funding
proposals <governance>`, with each masternode receiving one vote
(yes/no/abstain) on each proposal submitted to the system.

Anyone can run a masternode. The objective is to have enough
decentralization to ensure that no single person controls a significant
fraction of the masternodes. However, to avoid bloating the network with
unnecessary masternodes or encouraging reckless operators, there is one
condition that needs to be fulfilled: proof of ownership of 1000 Axe.
The coins don't need to be in the masternode, but they need to be kept
in a certain way that is transparent to the entire network. If the owner
moves or spends those coins, the masternode stops working and payment
ceases.

Masternodes are paid by the network for the PrivateSend, InstantSend and
governance services they provide. 45% of the block reward is paid out to
the masternodes, 45% to miners and 10% to the budget. In practice, half
of the reward from a normal block goes to the miner and half to the
masternode. Then, every 16,616 blocks (approximately 30.29 days), a
superblock is created that contains the entire 10% payout to the budget
proposal winners. Masternodes are selected for payment in each block
(approximately every 2.6 minutes) from a deterministic masternode list,
and moved to the back of the list after payment. As more masternodes are
created, the duration between payments increases. If the collateral
behind a masternode is spent, or if a masternode stops providing
services to the network for more than one hour, it is removed from the
list until normal service resumes. In this way, masternodes are given
incentive to provide efficient and reliable services to the network.

Having so many servers holding a full copy of the blockchain and working
for the coin can be extremely useful. Thanks to the reward system, there
is no risk of not having enough masternodes, and the developers can rely
on them quickly deploying any new decentralized feature they want to
implement. This is where the true strength of Axe lies - an
incentivized system of thousands of distributed servers working 24x7
means that Axe can scale more efficiently and deploy services more
quickly than a blockchain run entirely by unpaid volunteers. The more
masternodes, the better and safer the Axe network.

As of November 2018, the Axe network has `over 5000 masternodes located
<http://178.254.23.111/~pub/masternode_count.png>`_ in over `45
countries <https://chainz.cryptoid.info/axe/masternodes.dws>`_ and
hosted on `over 140 ISPs
<http://178.254.23.111/~pub/Axe/masternode_ISPs.html>`_. The block
reward is approximately 3.34 Axe, so the selected masternode receives
1.67 Axe per payment or approximately 6 Axe per month. The block
reward decreases by 7.14% approximately once per year, so the annual
earnings for a masternode owner is approximately 7% of the collateral, 
and will decrease over time `as calculated here
<https://docs.google.com/spreadsheets/d/1HqgEkyfZDAA6pIZ3df2PwFE8Z430SVIzQ-mCQ6wGCh4/edit#gid=523620673>`_. 
See `this tool <https://axe- news.de/axetv/#value=1000>`_ to calculate
real-time payment rates, and `this site
<http://178.254.23.111/~pub/Axe/Axe_Info.html>`_ for various real-time
statistics on the masternode network.


.. _dip3-changes:

DIP003 Masternode Changes
=========================

Axe 1.2.0 implements DIP003, which introduces several changes to how a
Axe masternode is set up and operated. A list of available
documentation appears below:

- `DIP003 Deterministic Masternode Lists <https://github.com/axerunners/dips/blob/master/dip-0003.md>`__
- :ref:`dip3-changes` (you are here)
- :ref:`dip3-upgrade`
- :ref:`Full masternode setup guide <masternode-setup>`
- :ref:`Information for users of hosted masternodes <hosted-setup>`
- :ref:`Information for operators of hosted masternodes <operator-transactions>`

Important concepts and changes:

- It is possible to upgrade an existing masternode in-place without 
  starting a new server and without moving your 1000 AXE collateral.
- A masternode was previously "started" using the ``masternode start-alias`` 
  command based on a ``masternode.conf`` file. Under DIP003, this file 
  is no longer used, and masternodes are "registered" instead of 
  "started". Masternodes begin offering services when a `ProRegTx <https://github.com/axerunners/dips/blob/master/dip-0003.md#registering-a-masternode-proregtx>`_ 
  `special transaction <https://github.com/axerunners/dips/blob/master/dip-0002.md>`_ 
  containing a particular key is written to the blockchain.
- As before in ``masternode.conf``, the ProRegTx references the
  transaction id (txid) and index holding the collateral. The IP address
  and port of the masternode are also defined in this transaction.
- The ProRegTx contains 2 Axe addresses (also called public keys) and
  one BLS public key, which represent 3 different roles in the
  masternode and define update and voting rights. The keys are:
  
  1. ``ownerKeyAddr``: This is a Axe address (public key) controlled by
     the masternode owner. It is different from the address used for the
     collateral. Because the owner uses the private key associated with
     this address to issue :ref:`ProUpRegTx <update-dip3-config>`
     transactions, it must be unique for each masternode.
  2. ``operatorPubKey``: This is the BLS public key of the masternode
     operator. Only the operator is allowed to issue :ref:`ProUpServTx
     <update-dip3-config>` transactions. Because the operator key is 
     used during live masternode operation to sign masternode-related 
     P2P messages, quorum-related messages and governance trigger votes,
     the BLS key must be unique for each masternode.
  3. ``votingKeyAddr``: This is a Axe address (public key) used for
     proposal voting. Votes signed with the corresponding private key 
     are valid while the masternode is in the registered set.

- Masternode payments were previously sent to the address holding the
  collateral. Under DIP003, the owner should specify a different address 
  to receive payments in the ProRegTx. The owner may optionally specify 
  a non-zero percentage as payment to a separate masternode operator, if
  applicable.
- The masternode configuration can later be updated using ProUpServTx,
  ProUpRegTx and ProUpRevTx transactions. See `Updating Masternode
  Information <https://github.com/axerunners/dips/blob/master/dip-0003.md#updating-masternode-information>`_ 
  in DIP003 and :ref:`update-dip3-config` in this documentation for more
  details.
- All functions related to DIP003 will only take effect once Spork 15 is
  enabled on the network. Until then, it is necessary to set up the
  masternode following the `old process <https://docs.axerunners.com/en/0.12.3/masternodes/setup.html>`_ 
  and then work through the :ref:`upgrade procedure <dip3-upgrade>`. In
  this state, the masternode will continue to function in compatibility
  mode, and all DIP003 related functions, such as payments to a separate
  address or percentage payments to operators, will not yet have any
  effect. The ``ownerKeyAddr`` and ``votingKeyAddr`` must also be 
  identical until Spork 15 is enabled.

The process of setting up or upgrading a masternode is as follows:

1. Set up your server and operating system
2. Install the Axe software and synchronize the blockchain
3. Generate a BLS key pair and enter the private key on the masternode
4. Prepare a ProRegTx transaction
5. Sign the ProRegTx transaction
6. Submit the signed ProRegTx transaction

Step 1 can be omitted if you have an existing server. Steps 2 and 3
require direct access to the masternode. Steps 3 and 4 require access to
a Axe Wallet (or DMT). Step 5 requires access to the wallet actually
holding the collateral. Step 6 requires a Axe balance to pay the
transaction fee.

Masternodes vs. mining
======================

Axe, like Bitcoin and most other cryptocurrencies, is based on a
decentralized ledger of all transactions, known as a blockchain. This
blockchain is secured through a consensus mechanism; in the case of both
Axe and Bitcoin, the consensus mechanism is Proof of Work (PoW).
:ref:`Miners <mining>` attempt to solve difficult problems with
specialized computers, and when they solve the problem, they receive the
right to add a new block to the blockchain. If all the other people
running the software agree that the problem was solved correctly, the
block is added to the blockchain and the miner is rewarded.

Axe works a little differently from Bitcoin, however, because it has a
two-tier network. The second tier is powered by masternodes (Full
Nodes), which enable financial privacy (PrivateSend), instant
transactions (InstantSend), and the decentralized governance and budget
system. Because this second tier is so important, masternodes are also
rewarded when miners discover new blocks. The breakdown is as follows:
45% of the block reward goes to the miner, 45% goes to masternodes, and
10% is reserved for the budget system (created by superblocks every
month).

The masternode system is referred to as Proof of Service (PoSe), since
the masternodes provide crucial services to the network. In fact, the
entire network is overseen by the masternodes, which have the power to
reject improperly formed blocks from miners. If a miner tried to take
the entire block reward for themselves or tried to run an old version of
the Axe software, the masternode network would orphan that block, and
it would not be added to the blockchain.

In short, miners power the first tier, which is the basic sending and
receiving of funds and prevention of doublespending. Masternodes power
the second tier, which provide the added features that make Axe
different from other cryptocurrencies. Masternodes do not mine, and
mining computers cannot serve as masternodes. Additionally, each
masternode is “secured” by 1000 AXE. Those AXE remain under the sole
control of their owner at all times, and can still be freely spent. The
funds are not locked in any way. However, if the funds are moved or
spent, the associated masternode will go offline and stop receiving
rewards.


.. _payment-logic:

Payment logic
=============

Masternode paymentss in Axe version 1.2.0 are entirely deterministic
and based on a simple list sort algorithm. For documentation of version
0.12.0 payment logic, see the `legacy masternode payment documentation
<https://docs.axerunners.com/en/0.12.3/masternodes/understanding.html#payment-logic>`_. 
Axe version 1.2.0 implements `DIP003
<https://github.com/axerunners/dips/blob/master/dip-0003.md>`_ and defines
two sets of masternodes.

1. The full set, which contains all registered masternodes that have not
   spent their collateral funding transactions.
2. The valid set, a subset of the full set which contains all 
   masternodes which are not marked as Proof of Service (PoSe) banned.

Each masternode in the set of valid masternodes is identified by the
block at which it was last paid. If it has never received payment or was
banned for failing to meet the PoSe requirements, then the block at
which it was first registered or at which PoSe was restored is used
instead. The list is sorted in ascending order, and the first entry is
paid. If this results in more than one masternode, then the hash of the
masternode ProRegTx is sorted to break the tie.


Quorum selection
================

InstantSend transactions in Axe version 1.2.0 are secured using a
consensus of deterministically selected masternodes. This set of
masternodes is informally termed a quorum and must be in a majority
agreement, at least six out of ten, for a successful lock of the
transaction inputs. Multiple quorums are self-selected for each input in
an InstantSend transaction using the mathematical distance between the
hash of each input and of the set of masternode funding transactions.

Each masternode receiving the InstantSend transaction lock request
compares the hash of the masternode's funding transaction to the hash of
the input requesting the lock. After validating the inputs are not
spent, the ten masternodes furthest from this hash broadcast their
acceptance of the lock.

All InstantSend inputs must be at least six blocks old or the
transaction will be rejected.


Masternode requirements
=======================

- 1000 Axe: Arguably the hardest part. Axe can be obtained from
  exchanges such as Poloniex, Bittrex, Kraken and LiveCoin. Shapeshift's
  service is also an excellent way.
- A server or VPS running Linux: Most recent guides use Ubuntu 16.04
  LTS. We recommend VPS services such as Vultr and DigitalOcean,
  although any decent provider will do. Generally an instance with low
  to average specifications will do, although performance requirements
  will increase according to this roadmap.
- A dedicated IP address: These usually come with the VPS/server.
- A little time and (heart): Masternodes used to require complex setup,
  but tools such as axerunner now greatly simplify the process.

In addition to the 1000 Axe held in collateral, masternodes also have
minimum hardware requirements. As of version 12.1, these requirements
are as follows:

+---------+------------+-------------+
|         | Minimum    | Recommended |
+=========+============+=============+
| CPU     | 1x 1 GHz   | 1x 2 GHz    |
+---------+------------+-------------+
| RAM     | 2 GB       | 4 GB        |
+---------+------------+-------------+
| Disk    | 20 GB      | 40 GB       |
+---------+------------+-------------+
| Network | 400 GB/mth | 1 TB/mth    |
+---------+------------+-------------+

Masternode bandwidth use ranges between 300-500 GB per month and will
grow as the network does.

Axe Evolution
--------------

The exact hardware requirements for Axe Evolution masternodes have yet
to be determined, although some pointers can be taken from the `roadmap
<https://github.com/axerunners/axe-roadmap>`_ and this `blog post
<https://medium.com/@eduffield222/how-to-enabling-on-chain-scaling-2ffab5997f8b>`_. 
It should be possible to run Axe masternodes on normal VPS servers
until the block size reaches approximately 20 MB, after which custom
hardware such as GPUs and eventually ASICs may be required.
