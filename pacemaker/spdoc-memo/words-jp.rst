=========================
用語説明
=========================

Cluster Concepts
==================================

英語部分は、下記サイトから引用
http://linux-ha.org/wiki/Cluster_Concepts

Cluster
---------------------------------------
For our purposes, a cluster is a collection of loosely coupled cooperating computing elements which we refer to as nodes. Failures in clusters are not observed instantaneously or simultaneously by every node. Instead, failures occur asynchronously, and are observed stochastically and independently by the nodes of the cluster. It is not guaranteed that any particular failure will be observed in the same way by every node in the cluster, or even observed at all by every node.

クラスタとは、疎結合している複数のコンピューティングノードの集合です。一般にクラスタ内ノードの動作不良は、確実に把握、共有されることはなく、一部のノードにより確率的に観測されます。（以降のSubcluster, Membership, Quorum, Fencingなどは、この問題を解決するための仕組みと及び概念と位置づけられます。）

Subcluster
------------------------------------------
At any given point in time, a cluster is divided into zero or more subclusters (or partitions) of live nodes. Each live node has a view of subcluster membership, and acknowledges membership in no more than one of these subclusters. The most desirable state is that there is only one subcluster in the cluster, and that all (active) nodes belong to that subcluster. However, communication failures can cause a single cluster to divide into multiple subclusters which are partially or completely unaware of each other.

クラスタが動作しているノードの集合からなる部分集合に分割された場合、それぞれの部分集合をSubclusterと呼びます。

Primary Subcluster
------------------------------------------
One of these subclusters may be designated the primary subcluster. Traditionally, this primary subcluster is said to have quorum (or more specifically have cluster-quorum, or cluster-wide-quorum). How and why the primary subcluster is chosen is implementation-dependent. How and why each node associates itself with a particular subcluster is implementation-dependent.

quorumの対応する日本語は、定足数となります。クラスターが分割され、相互に情報交換ができない状況で誰がPrimary Subclusterとなるかを判断する方法として、quorum（定足数）を定義し、subclusterのメンバー数がquorum以上であればそのsubclusterをprimary subclusterとします。複数のsubclusterが同時に自分達がprimary subclusterだと信じてシステムの整合性が破壊されること（スプリットブレイン問題）を回避するためにこの仕組みが利用されます。

Membership
---------------------------------------
Membership calculation is the process whereby each node associates itself with a particular subcluster, and obtains a (probabalistically current) view of subcluster membership. Quorum calculation is the process whereby the primary subcluster (if any) is selected.
Normally, a membership algorithm will try to make the primary subcluster as large as it can, but it is provable that this is impossible under all circumstances. If a node is in a non-primary subcluster, the membership algorithm is under no obligation to try and make these non-primary subclusters as large as possible.
At any given point in time, each node can view the subcluster of which it is a member as either having stable membership or being in transition. Stable membership is defined to mean that no event has yet been observed by the node which would cause it to recalculate subcluster membership and/or quorum. In transition means that a particular node has observed an event which may cause a membership change, but that the process of recomputing membership and/or quorum has not yet completed. Different nodes in a subcluster may have different views of the stable/transition state at any given point in time.
Because failures occur asynchronously and are observed stochastically, membership provides only probabilistic (and not absolute) assurances of ability to communicate with any particular node. Because it is impossible to know whether a node in a subcluster might observed an event which would make it go into a into transition, in a very real sense, membership is only probabilistically certain, never absolutely certain.

Membershipとは、任意のsubclusterに所属しているノードの集合です。Quorumの計算は、Membership（つまり、いくつのノードがそのsubclusterに所属しているか）に基き実施されます。あるsubclusterに属しているノードたちは、それぞれにどのノードがそのsubclusterに属しているのか（Membership)についての情報を計算し保持していますが、それは確率的に恐らくそうであろうという情報であり、確実な情報では有り得ません。

Determination of Quorum
---------------------------------------
The quorum calculation process is required to select no more than one primary subcluster at a time, but need not select any at all. Under some circumstances, designating more than one primary subcluster at a time can lead to irrecoverable application failures. Nevertheless, no quorum algorithm can provide an absolute guarantee of this property in the presence of arbitrary failures. Different quorum implementations provide different degrees of certainty for any given configuration and set of expected failures.

A common method for defining quorum is to say that a subcluster of size n in a cluster of m total nodes has quorum if it has a plurality of nodes in its partition. That is, it has n members where n > INT(m/2). This simple method works quite well for larger clusters with generally reliable communications. However it breaks down for 2-node clusters, and may perform poorly for geographically dispersed clusters without highly reliable communications between sites.

Quorumの計算目的は、唯一のPrimary subclusterを決定することにあります。
ここでは、例としてquorumをクラスタを構成する全ノードの過半数としているが、Pacemakerでも上記の方式でquorumを定義ています。

Fencing
---------------------------------------
We use the term fencing to refer to the act of separating a cluster node from the resources it manages, without its cooperation. That is, proper fencing techniques will separate a node from its resources without the cooperation of the node being fenced. Given that a cluster node which is subjected to fencing is typically thought to be errant, and the nature of the fault it has experienced is unknown, relying on a third party (involuntary) fencing mechanism increases the probability that an errant node is not longer using its resources. Using fencing and is considered more reliable than simply relying on an errant node to stop using resources on its own.

Pacemakerでは、Fencingと同義語としてしばしはSTONITH(Shoot The Other Node In The Head)が用いられます。問題があるノードをクラスタから切り離すことを指します。上記では、Fencingの信頼性を高めるには、問題のあるノードの助けを借りずに外部からFencingすることが必要であると述べられています。

Type of cluster HA
===========================================
クラスタで高可用性（HA cluster）を実現する方式として、代表的なもの（Active/active, Active/passive, N+1, N+M, N-to-1, N-to-N）が下記リンクに記載されています。
https://en.wikipedia.org/wiki/High-availability_cluster

ここでは、試用手順で取り上げた二つの方式について説明します。

Active/passive
------------------------------
プライマリノードとそれを完全に代替できるスタンバイノードで構成します。スタンバイノードは、プライマリノードに問題が生じた時にだけオンラインになります。

Active/active
-----------------------------
全てのノードが応答可能な状態で稼働します。問題が生じたノードへのトラフィックは、稼働している残りのノードのいずれかにロードバランスされます。


Pacemaker specific
===============================================
以下、Pacemakerで利用される特有の用語についてです。

Resource, Group, Clone
-------------------------
Resourceは、クラスタで提供されるサービスのことを指さします。Resourceの最も基本的なタイプをprimitive resourceと言いますprimitive resourceは対応するresource agent（実体は動作を実装したスクリプト）により動作が規定されます。
より複雑なタイプのresourceとして、group, cloneがあります。複数のresourceの集合をgroupとして扱うことが可能です。また、resourceを複製し、複数のノードで動かすためにcloneが利用されます。

Constraint, Colocation, Order
--------------------------------------------
constraintとは、制約のことです。resourceの配置や関係性に制約条件を課すことができ、その条件の集合をconstraintと言います。
colocationは、resource間の関係を記述します。例えば、
::

   # pcs constraint colocation add webserver with ClusterIP INFINITY

とした場合、webserverリソースは、ClusterIPリソースが起動している場所でしか起動することができなくなります。
また、resource間の起動／停止の順序を指定することも可能で、その場合はorderを利用します。
::

   # pcs constraint order ClusterIP then webserver

例えば上記のように指定すると、起動はClusterIP -> webserverの順になり、停止は逆順になります。

