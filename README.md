# idbook-DAG
DAG Consensus
DAG stands for “Directed Acyclic Graph”, and is a finite directed graph with no directed cycles. It consists of finitely many vertices and edges, with each edge directed from one vertex to another, such that there is no way to start at any vertex v and follow a consistently-directed sequence of edges that eventually loops back to v again. DAG has no block concept. It never packages all the data into blocks, or links blocks with other blocks, but every user can submit a data unit with things such as transactions, messages, and so on.
The data units are linked by reference relations, thus forming a Directed Acyclic Graph with a half order relationship. DAG is characterized by the asynchronous operation of writing data, so that large numbers of nodes can write transaction data asynchronously to DAG, which can support great concurrency and high speed. At present, there are many projects such as IOTA and Byteball have used DAG to successfully build a public blockchain which can run stably for a long time.
In IDBook, events such as authentication, voting, verification, authorization, and transfers are all treated as transactions, and multiple events can be grouped together in a block of data, which becomes an accounting unit. The new unit forms a parent-child structure by referencing one or more old units. Then the constantly updated units continue to link to their old units before they are combined into a DAG structure.
In DAG, the smallest data unit is the accounting unit, which contains the following structure.
Head: 
The hash list of all parent units of the current unit, its hash, timestamp, protocol version number, transaction merkleroot, etc.
Transaction: 
The transaction zone records various types of transactions, including identity, login and other authentications, data acquisition, exchanging matching, reviews, vote ranking, etc. They are distinguished by the type field. Transactions can be added according to the ecosystem development. Transaction parts are recorded based on UTXO model, with data content being accounted differently depending on the type of transaction. The transaction fees are paid to the first accounting node, and the fee= input - output - byte cost. Therefore, users broadcasting a transaction to the DAG must pay a certain fee to motivate nodes to process it.


Main Chain:
The optimal path from any top unit to the genesis unit is called the candidate main chain. The optimal path is generated by selecting the optimal parent units. Different candidate main chains will intersect in one unit. The worst case is intersecting in the genesis unit, which is called the stable point. For all candidate main chains, the path from the stable point to the genesis unit is exactly the same. This path is called stable main chain.


Each unit in the DAG is either directly on the main chain or can be reached by a shorter path. Once we have a main chain (MC), we can establish a total order between two conflicting nonserial units. Let’s first index the units that lie directly on the main chain. The genesis unit has index 0, the next MC unit that is a child of genesis has index 1, and so on traveling forward along the MC we assign indexes to 
units that lie on the MC. For units that do not lie on the MC, we can find an MC index where this unit is first included (directly or indirectly). In such a way, we can assign a Main Chain Index (MCI) to every unit.

Optimal Parent Unit Selection Strategy
(1) Level of Unit: the maximum path length from the current unit to the genesis unit.
(2) Level of witness: starting from the current unit tracing back along the main chain and counting the path of different witnesses, with the same witness only counted 1 time. When the number of witnesses is enough-more than most of the known witnesses-stop tracing back and calculate the stop position unit level. This is described as proof of the current unit level.

The optimal parent unit selection strategy consists of the following three parts:
(1) when choosing the optimal parent unit, the witness of the highest-level parent unit is the optimal parent unit.
(2) if they bear the same witness level, unit of the lowest level is the optimal parent unit.
(3) if both are the same, the selected unit with smaller hash value is the optimal parent unit.
Then, starting from the top unit, you only need to recursively select the optimal parent unit in the parent unit to form the main chain.


Double-spend Transaction
Any unordered transaction sent from the same address can be considered a double-spend transaction, even if they do not use the same output. When a user creates a new unit, all units that require the same address to be published should directly or indirectly contain all units prior to that address, that is, all units of the same address are connected. Therefore, in the case that all units of the same address are connected, an earlier transaction on the path is a valid transaction. If there is an attacker that deliberately creates a double-spend transaction, the transaction can be resolved by the main chain order number. The transaction with a smaller main chain order number is the valid transaction.

The image above shows an attack scenario in which an attacker creates a shadow chain on which to process a double-spend transaction. When the shadow chain links into the real DAG, according to the optimal parent unit selection strategy, the shadow chain with fewer witnesses will not become part of the main chain, so as to solve the problem of double spends under this scenario. But if most of the witnesses collude with the attacker and publish units on their shadow chain, the attack may succeed.

Transaction Confirmation
The path from the stable point to the genesis unit of all main chain candidates is exactly the same. That is, the stable main chain becomes the final state. Therefore, those elements that are directly or indirectly contained by the unit on the stable main chain will no longer be tampered with. As long as new units are added, the stable point can be extended backward, increasing the stability of the different user node points. All user nodes on the entire network can achieve consensus, and the transactions the stable units contain on the main chain will be confirmed.

