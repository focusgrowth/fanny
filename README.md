比特币：一种点对点的电子现金系统

​ 作者：中本聪 ​ satoshin@gmx.com ​ www.bitcoin.org ​ 2008.10.31

​ 中文翻译：时汝佳 ​ Rujiashi@gmail.com ​ 2020.02.07

​ Checkout Github Repo for this translation

Abstract. A purely peer-to-peer version of electronic cash would allow online payments to be sent directly from one party to another without going through a financial institution. Digital signatures provide part of the solution, but the main benefits are lost if a trusted third party is still required to prevent double-spending. We propose a solution to the double-spending problem using a peer-to-peer network. The network timestamps transactions by hashing them into an ongoing chain of hash-based proof-of-work, forming a record that cannot be changed without redoing the proof-of-work. The longest chain not only serves as proof of the sequence of events witnessed, but proof that it came from the largest pool of CPU power. As long as a majority of CPU power is controlled by nodes that are not cooperating to attack the network, they'll generate the longest chain and outpace attackers. The network itself requires minimal structure. Messages are broadcast on a best effort basis, and nodes can leave and rejoin the network at will, accepting the longest proof-of-work chain as proof of what happened while they were gone.

    
  概要：不经过第三方金融机构，由一个点在线支付给另外一个点，一个简单的点对点电子现金系统即可做到。数字签名技术也可以做到点对点传输，但要防止“双花”问题，只要第三方存在，就有风险。

我们提供了一套解决方案，借用点对点网络，可以解决双花问题。

将交易加上时间戳，哈希之后发布到网络中，让其加入一条以哈希算力为基础的工作量证明的链，这条链无法篡改，除非算力攻击。最长链是过去一系列事件的证明，而且也表明这是来自最强大的CPU算力，只要算力节点不攻击网络，他们就会制造最长的链并且防御攻击者。网络本身是最小化设计，信息全网广播，节点可自由进出网络，只要在重新加入的时候接受最长链即可。

1. 简介 (Introduction)

Commerce on the Internet has come to rely almost exclusively on financial institutions serving as trusted third parties to process electronic payments. While the system works well enough for most transactions, it still suffers from the inherent weaknesses of the trust based model. Completely non-reversible transactions are not really possible, since financial institutions cannot avoid mediating disputes. The cost of mediation increases transaction costs, limiting the minimum practical transaction size and cutting off the possibility for small casual transactions, and there is a broader cost in the loss of ability to make non-reversible payments for non-reversible services. With the possibility of reversal, the need for trust spreads. Merchants must be wary of their customers, hassling them for more information than they would otherwise need. A certain percentage of fraud is accepted as unavoidable. These costs and payment uncertainties can be avoided in person by using physical currency, but no mechanism exists to make payments over a communications channel without a trusted party.

What is needed is an electronic payment system based on cryptographic proof instead of trust, allowing any two willing parties to transact directly with each other without the need for a trusted third party. Transactions that are computationally impractical to reverse would protect sellers from fraud, and routine escrow mechanisms could easily be implemented to protect buyers. In this paper, we propose a solution to the double-spending problem using a peer-to-peer distributed timestamp server to generate computational proof of the chronological order of transactions. The system is secure as long as honest nodes collectively control more CPU power than any cooperating group of attacker nodes.


互联网商业的电子支付过于依赖第三方金融机构，这个系统大部分时候运转良好。但信任基础是它的内在缺点，完全无法逆转的交易是不可能的，因为金融机构无法避免产生纠纷，纠纷成本大于交易成本时，会限制小额支付，当不可逆服务需要不可逆支付时，成本会更高，此时我们需要建立信任机制，商人会给客户提供更透明的信息，一定比例的纠纷被认为不可避免，使用现金不存在以上问题，电子支付则过于依赖第三方。

电子支付系统需要加密证明，而不是信任，我们不需要第三方。

系统机制会保护买卖双方。论文中我们提供了一套解决方案，解决“双花”问题，打包交易，加上时间戳，工作量证明等，只要大部分节点都是诚实受规则的，系统便安全。


2. 交易 (Transactions)

We define an electronic coin as a chain of digital signatures. Each owner transfers the coin to the next by digitally signing a hash of the previous transaction and the public key of the next owner and adding these to the end of the coin. A payee can verify the signatures to verify the chain of ownership.

The problem of course is the payee can't verify that one of the owners did not double-spend the coin. A common solution is to introduce a trusted central authority, or mint, that checks every transaction for double spending. After each transaction, the coin must be returned to the mint to issue a new coin, and only coins issued directly from the mint are trusted not to be double-spent. The problem with this solution is that the fate of the entire money system depends on the company running the mint, with every transaction having to go through them, just like a bank.

We need a way for the payee to know that the previous owners did not sign any earlier transactions. For our purposes, the earliest transaction is the one that counts, so we don't care about later attempts to double-spend. The only way to confirm the absence of a transaction is to be aware of all transactions. In the mint based model, the mint was aware of all transactions and decided which arrived first. To accomplish this without a trusted party, transactions must be publicly announced[^1], and we need a system for participants to agree on a single history of the order in which they were received. The payee needs proof that at the time of each transaction, the majority of nodes agreed it was the first received.


我们设计一种数字签名链的电子货币支付时，甲签署一个交易的哈希和乙的工要将其加入到该货币的末尾，接收者可以查询这笔钱是否来自发送者，问题是无法确认是否双花，万一他给你发了一份，又给别人发了一份。常规解决方案，是找来第三方中心权威机构或铸币厂，每一笔交易之后硬币回收到铸币厂，铸币厂再发行一枚新币，只有铸币厂直接发行的硬币是可信的，不用担心双花，这个方案的问题是整个金钱系统依赖于铸币厂，每笔交易都要经过他们，就像一家银行。我们需要一种方法，支付方之前未将这笔钱给过他人，在我们的设计里最早的交易可以知晓，所以我们不担心他以后再试图双花，最好的方法是遍历所有交易，铸币厂模式中铸币厂掌握着所有交易详情，并且知晓每一笔交易的先后顺序，如果不用可信第三方，交易需要全网公布[^1]，系统参与者同意他们接收到的交易顺序，支付方需提供交易的时间，大部分节点认可他们最先收到的交易。


3. 时间戳服务器 (Timestamp Server)

The solution we propose begins with a timestamp server. A timestamp server works by taking a hash of a block of items to be timestamped and widely publishing the hash, such as in a newspaper or Usenet post[^2] [^3] [^4] [^5]. The timestamp proves that the data must have existed at the time, obviously, in order to get into the hash. Each timestamp includes the previous timestamp in its hash, forming a chain, with each additional timestamp reinforcing the ones before it.


我们设计了一种时间戳服务，把每一个交易一块儿打包，盖上时间戳广播哈希，这就像报纸上的邮戳[^2] [^3] [^4] [^5]，这让交易与时间同步，明显的时间也会被打包并哈希，每一个时间戳包含哈希块里的之前的时间戳，彼此成链，每打上一个时间戳他会让之前的时间戳更可信。


4. 工作证明 (Proof-of-Work)

To implement a distributed timestamp server on a peer-to-peer basis, we will need to use a proof-of-work system similar to Adam Back's Hashcash[^6], rather than newspaper or Usenet posts. The proof-of-work involves scanning for a value that when hashed, such as with SHA-256, the hash begins with a number of zero bits. The average work required is exponential in the number of zero bits required and can be verified by executing a single hash.

For our timestamp network, we implement the proof-of-work by incrementing a nonce in the block until a value is found that gives the block's hash the required zero bits. Once the CPU effort has been expended to make it satisfy the proof-of-work, the block cannot be changed without redoing the work. As later blocks are chained after it, the work to change the block would include redoing all the blocks after it.

The proof-of-work also solves the problem of determining representation in majority decision making. If the majority were based on one-IP-address-one-vote, it could be subverted by anyone able to allocate many IPs. Proof-of-work is essentially one-CPU-one-vote. The majority decision is represented by the longest chain, which has the greatest proof-of-work effort invested in it. If a majority of CPU power is controlled by honest nodes, the honest chain will grow the fastest and outpace any competing chains. To modify a past block, an attacker would have to redo the proof-of-work of the block and all blocks after it and then catch up with and surpass the work of the honest nodes. We will show later that the probability of a slower attacker catching up diminishes exponentially as subsequent blocks are added.

To compensate for increasing hardware speed and varying interest in running nodes over time, the proof-of-work difficulty is determined by a moving average targeting an average number of blocks per hour. If they're generated too fast, the difficulty increases.


分布式时间戳点对点会有偏差，我们设计了pow类似的方法，而不是报纸上的时间，pow方法需要做哈希256运算穷举方法来寻找合适的值，解题难但验证容易。

在时间戳网络中，我们在区块中尝试随机数，当区块加随机数的哈希符合条件时就完成了pow，一旦CPU做出了pow的贡献区块无法更改，以后的区块在它之后链接，如果想改变它，那么要一起改变它之后的所有区块。Pow也解决了少数服从多数的问题，如果一个IP一票，那难免会有人拥有大量IP。Pow保证一个CPU一票，最长的链代表多数人的决定，因为有算力支撑，只要算力掌握在多数诚实节点中，链就会越来越长并且超越其他竞争者，想要修改区块中的信息，需要重做区块的pow，还要修改该区块后的所有区块，并且要比诚实节点更快，稍后我会为您演示，一旦落后便是指数级的落后，硬件会升级，如果算力上升，难度也会上升。


5. 网络 (Network)

The steps to run the network are as follows:

        New transactions are broadcast to all nodes.
        Each node collects new transactions into a block.
        Each node works on finding a difficult proof-of-work for its block.
        When a node finds a proof-of-work, it broadcasts the block to all nodes.
        Nodes accept the block only if all transactions in it are valid and not already spent.
        Nodes express their acceptance of the block by working on creating the next block in the chain, using the hash of the accepted block as the previous hash.

运行网络的步骤如下：

    新交易广播制所有节点

    每个节点将接收到的新交易放到区块。

    每个节点开始寻找区块的pow难度值。

    某个节点解出pow，他将该区块广播至全网。

    如果该区块中所有交易有效并且未被花费，则所有节点验证后接受该区块。

    节点以刚接收到的区块为准，开始下一轮计算。
    
Nodes always consider the longest chain to be the correct one and will keep working on extending it. If two nodes broadcast different versions of the next block simultaneously, some nodes may receive one or the other first. In that case, they work on the first one they received, but save the other branch in case it becomes longer. The tie will be broken when the next proof-of-work is found and one branch becomes longer; the nodes that were working on the other branch will then switch to the longer one.

New transaction broadcasts do not necessarily need to reach all nodes. As long as they reach many nodes, they will get into a block before long. Block broadcasts are also tolerant of dropped messages. If a node does not receive a block, it will request it when it receives the next block and realizes it missed one.


节点认可最长的区块链，并且不断拓展它，如果两个节点同时找到区块，可能一些节点和另一些节点收到的区块不同，这种情况下他们会以各自收到的区块为准来做pow，一旦找到下一个区块节点会全部切换到最长链上来。

广播的新交易不必到达所有节点，只要大部分节点收到即可，交易会被放进正在打包的区块中，区块儿广播也可容忍信息丢失。如果节点未收到区块信息，当他收到下一个区块时会自动补全。


6. 奖励 (Incentive)

By convention, the first transaction in a block is a special transaction that starts a new coin owned by the creator of the block. This adds an incentive for nodes to support the network, and provides a way to initially distribute coins into circulation, since there is no central authority to issue them. The steady addition of a constant of amount of new coins is analogous to gold miners expending resources to add gold to circulation. In our case, it is CPU time and electricity that is expended.

The incentive can also be funded with transaction fees. If the output value of a transaction is less than its input value, the difference is a transaction fee that is added to the incentive value of the block containing the transaction. Once a predetermined number of coins have entered circulation, the incentive can transition entirely to transaction fees and be completely inflation free.

The incentive may help encourage nodes to stay honest. If a greedy attacker is able to assemble more CPU power than all the honest nodes, he would have to choose between using it to defraud people by stealing back his payments, or using it to generate new coins. He ought to find it more profitable to play by the rules, such rules that favour him with more new coins than everyone else combined, than to undermine the system and the validity of his own wealth.


区块中第1个交易特殊在于它将给予区块发现者一些新的币，这让节点有动力去支持网络，也提供了币的初始分发，因为没有中心机构来做这件事。

金矿主通过投入资源挖出金子，并将其投放市场，保证稳定供应。我们通过CPU计算和电力支撑网络，激励中也有交易费用，如果交易中输出小于输入差额，就是小费，会作为激励给打包这笔交易的区块者，一旦这些钱流通起来，交易费用可以抗通胀。

这些也可以激励节点保持诚实，如果贪婪的攻击者集结51%算利，它会权衡是攻击网络呢还是挖区块好呢？他会发现遵守游戏规则更划算，因为规则可以让他拥有更多比特币，比其他人加起来都多，而破坏系统会动摇他的财富有效性。


7. 回收硬盘空间 (Reclaiming Disk Space)

Once the latest transaction in a coin is buried under enough blocks, the spent transactions before it can be discarded to save disk space. To facilitate this without breaking the block's hash, transactions are hashed in a Merkle Tree[^2][^5][^7], with only the root included in the block's hash. Old blocks can then be compacted by stubbing off branches of the tree. The interior hashes do not need to be stored.

A block header with no transactions would be about 80 bytes. If we suppose blocks are generated every 10 minutes, 80 bytes * 6 * 24 * 365 = 4.2MB per year. With computer systems typically selling with 2GB of RAM as of 2008, and Moore's Law predicting current growth of 1.2GB per year, storage should not be a problem even if the block headers must be kept in memory.


一旦最新交易形成它所在区块上有足够的区块，该交易之前所有相关的交易可以进行某种操作来节省硬盘空间，为了不破坏区块的哈希值，这些交易被以默克尔数的形式，哈希每个区块只留下默克尔根，之前的区块都可以被压缩，由于砍掉了默克尔树的分支，不需要储存所有的哈希，区块头不包含交易信息，大约80字节。假设平均10分钟产生一个区块，80×6×24×365=4.2MB每年。2008年电脑内存大多是2G，摩尔定律预测每年增长1.2G，所以存储也不是问题，即使把所有区块头放在内存中。


8. 简化版支付确认 (Simplified Payment Verification)

It is possible to verify payments without running a full network node. A user only needs to keep a copy of the block headers of the longest proof-of-work chain, which he can get by querying network nodes until he's convinced he has the longest chain, and obtain the Merkle branch linking the transaction to the block it's timestamped in. He can't check the transaction for himself, but by linking it to a place in the chain, he can see that a network node has accepted it, and blocks added after it further confirm the network has accepted it.
As such, the verification is reliable as long as honest nodes control the network, but is more vulnerable if the network is overpowered by an attacker. While network nodes can verify transactions for themselves, the simplified method can be fooled by an attacker's fabricated transactions for as long as the attacker can continue to overpower the network. One strategy to protect against this would be to accept alerts from network nodes when they detect an invalid block, prompting the user's software to download the full block and alerted transactions to confirm the inconsistency. Businesses that receive frequent payments will probably still want to run their own nodes for more independent security and quicker verification.


即使不是全节点也可以验证交易，用户只需有最长pow链的区块头信息即可，该信息可以从全节点处获得，只要确认这个全节点有最长的链。据此可找到交易所在的默克尔分支，该分支在有时间戳的区块内。用户无法校验交易的有效性，但是通过在链上找到交易的位置，用户如果发现有网络节点已经接收认可了交易，那么以后的区块会增强其有效性，只要诚实节点控制网络，那么验证就是可靠的，但如果攻击者算力过强网络就变得脆弱了，虽然网络节点可以自己确认交易，但如果攻击者过强该方法就会被攻击者的伪交易欺骗，一个策略是如果生意够大，就做全节点吧。


9. 价值的组合与分割 (Combining and Splitting Value)

Although it would be possible to handle coins individually, it would be unwieldy to make a separate transaction for every cent in a transfer. To allow value to be split and combined, transactions contain multiple inputs and outputs. Normally there will be either a single input from a larger previous transaction or multiple inputs combining smaller amounts, and at most two outputs: one for the payment, and one returning the change, if any, back to the sender.

It should be noted that fan-out, where a transaction depends on several transactions, and those transactions depend on many more, is not a problem here. There is never the need to extract a complete standalone copy of a transaction's history.


虽然可以独立拥有币，但不可能为每一个cent设立一个交易，为了使价值分合，交易是多输入多输出的，可以是单输入或多输出，但至少两个输出，一个用来支付，一个用来找零，需要指出这样一种交易，该交易建立在很多纷杂的交易上，这也不是问题，不需要遍历交易历史。


10. 隐私 (Privacy)

The traditional banking model achieves a level of privacy by limiting access to information to the parties involved and the trusted third party. The necessity to announce all transactions publicly precludes this method, but privacy can still be maintained by breaking the flow of information in another place: by keeping public keys anonymous. The public can see that someone is sending an amount to someone else, but without information linking the transaction to anyone. This is similar to the level of information released by stock exchanges, where the time and size of individual trades, the "tape", is made public, but without telling who the parties were.

As an additional firewall, a new key pair should be used for each transaction to keep them from being linked to a common owner. Some linking is still unavoidable with multi-input transactions, which necessarily reveal that their inputs were owned by the same owner. The risk is that if the owner of a key is revealed, linking could reveal other transactions that belonged to the same owner.


出于隐私保护，传统银行模型会采用限制他人获取信息的方式，全部公开交易信息是有必要的，隐私也可以得到保护，采用的方法是保证公钥匿名，公众可以看到某人和他人发起了一笔交易，但不能知道人家所有的交易，这类似于股票交易所的信息公布等级，交易时间，交易量及价格是公开的，但无法知晓交易方。

作为防火墙需要配置新的钥匙对，为每一笔交易保证不与无关者相连，一些多输入交易仍然无法避免，显露出这属于同一个人，风险在于一旦他的一个钥匙被发现了，其他的交易也会被追踪发现。


11. 计算 (Calculations)

We consider the scenario of an attacker trying to generate an alternate chain faster than the honest chain. Even if this is accomplished, it does not throw the system open to arbitrary changes, such as creating value out of thin air or taking money that never belonged to the attacker. Nodes are not going to accept an invalid transaction as payment, and honest nodes will never accept a block containing them. An attacker can only try to change one of his own transactions to take back money he recently spent.

假设一个场景，某个攻击者正在试图生成一个比诚实链更快的替代链。就算他成功了，也不能对系统做任意的修改，即，他不可能凭空制造出价值，也无法获取从未属于他的钱。网络节点不会把一笔无效交易当作支付，而诚实节点也永远不会接受一个包含这种支付的区块。攻击者最多只能修改属于他自己的交易，进而试图取回他已经花出去的钱。

The race between the honest chain and an attacker chain can be characterized as a Binomial Random Walk. The success event is the honest chain being extended by one block, increasing its lead by +1, and the failure event is the attacker's chain being extended by one block, reducing the gap by -1.

诚实链和攻击者之间的竞争可以用二项式随机漫步来描述。成功事件是诚实链刚刚被添加了一个新的区块，使得它的优势增加了 $1$；而失败事件是攻击者的链刚刚被增加了一个新的区块，使得诚实链的优势减少了 $1$。

The probability of an attacker catching up from a given deficit is analogous to a Gambler's Ruin problem. Suppose a gambler with unlimited credit starts at a deficit and plays potentially an infinite number of trials to try to reach breakeven. We can calculate the probability he ever reaches breakeven, or that an attacker ever catches up with the honest chain, as follows[^8]:

攻击者能够从落后局面追平的概率类似于赌徒破产问题。假设，一个拿着无限筹码的赌徒，从亏空开始，允许他赌无限次，目标是填补上已有的亏空。我们能算出他最终能填补亏空的概率，也就是攻击者能够赶上诚实链的概率[^8]，如下：

$$ \begin{eqnarray*} \large p &=& \text{ 诚实节点找到下一个区块的概率}\ \large q &=& \text{ 攻击者找到下一个区块的概率}\ \large q_z &=& \text{ 攻击者落后 $z$ 个区块却依然能够赶上的概率} \end{eqnarray*} $$

$$ \large q_z = \begin{Bmatrix} 1 & \textit{if}; p \leq q\ (q/p)^z & \textit{if}; p > q \end{Bmatrix} $$

Given our assumption that $p \gt q$, the probability drops exponentially as the number of blocks the attacker has to catch up with increases. With the odds against him, if he doesn't make a lucky lunge forward early on, his chances become vanishingly small as he falls further behind.

既然我们已经假定 $p > q$, 既然攻击者需要赶超的区块数量越来越多，那么其成功概率就会指数级下降。于赢面不利时，如果攻击者没有在起初就能幸运地向前猛跨一步，那么他的胜率将在他进一步落后的同时消弭殆尽。

We now consider how long the recipient of a new transaction needs to wait before being sufficiently certain the sender can't change the transaction. We assume the sender is an attacker who wants to make the recipient believe he paid him for a while, then switch it to pay back to himself after some time has passed. The receiver will be alerted when that happens, but the sender hopes it will be too late.

现在考虑一下一笔新交易的收款人需要等多久才能充分确定发款人不能更改这笔交易。我们假定发款人是个攻击者，妄图让收款人在一段时间里相信他已经支付对付款项，随后将这笔钱再转回给自己。发生这种情况时，收款人当然会收到警告，但发款人希望那时木已成舟。

The receiver generates a new key pair and gives the public key to the sender shortly before signing. This prevents the sender from preparing a chain of blocks ahead of time by working on it continuously until he is lucky enough to get far enough ahead, then executing the transaction at that moment. Once the transaction is sent, the dishonest sender starts working in secret on a parallel chain containing an alternate version of his transaction.

收款人生成了一对新的公私钥，而后在签署之前不久将公钥告知发款人。这样可以防止一种情形：发款人提前通过连续运算去准备一条链上的区块，并且只要有足够的运气就会足够领先，直到那时再执行交易。一旦款项已被发出，那个不诚实的发款人开始秘密地在另一条平行链上开工，试图在其中加入一个反向版本的交易。

The recipient waits until the transaction has been added to a block and $z$ blocks have been linked after it. He doesn't know the exact amount of progress the attacker has made, but assuming the honest blocks took the average expected time per block, the attacker's potential progress will be a Poisson distribution with expected value:

收款人等到此笔交易被打包进区块，并已经有 $z$ 个区块随后被加入。他并不知道攻击者的工作进展究竟如何，但是可以假定诚实区块在每个区块生成过程中耗费的平均时间；攻击者的潜在进展符合泊松分布，其期望值为：

$$ \large \lambda = z \frac qp $$

To get the probability the attacker could still catch up now, we multiply the Poisson density for each amount of progress he could have made by the probability he could catch up from that point:

为了算出攻击者依然可以赶上的概率，我们要把每一个攻击者已有的进展的帕松密度乘以他可以从那一点能够追上来的概率：

$$ \large \sum_{k=0}^{\infty} \frac{\lambda^k e^{-\lambda}}{k!} \cdot \begin{Bmatrix} (q/p)^{(z-k)} & \textit{if};k\leq z\ 1 & \textit{if} ; k > z \end{Bmatrix} $$

Rearranging to avoid summing the infinite tail of the distribution...

为了避免对密度分布的无穷级数求和重新整理…

$$ \large 1 - \sum_{k=0}^{z} \frac{\lambda^k e^{-\lambda}}{k!} \left ( 1-(q/p)^{(z-k)} \right ) $$

Converting to C code...

转换为 C 语言程序……

#include <math.h>
double AttackerSuccessProbability(double q, int z)
{
	double p = 1.0 - q;
	double lambda = z * (q / p);
	double sum = 1.0;
	int i, k;
	for (k = 0; k <= z; k++)
	{
		double poisson = exp(-lambda);
		for (i = 1; i <= k; i++)
			poisson *= lambda / i;
		sum -= poisson * (1 - pow(q / p, z - k));
	}
	return sum;
}

Running some results, we can see the probability drop off exponentially with $z$.

获取部分结果，我们可以看到概率随着 $z$ 的增加指数级下降：

   q=0.1
   z=0    P=1.0000000
   z=1    P=0.2045873
   z=2    P=0.0509779
   z=3    P=0.0131722
   z=4    P=0.0034552
   z=5    P=0.0009137
   z=6    P=0.0002428
   z=7    P=0.0000647
   z=8    P=0.0000173
   z=9    P=0.0000046
   z=10   P=0.0000012
   
   q=0.3
   z=0    P=1.0000000
   z=5    P=0.1773523
   z=10   P=0.0416605
   z=15   P=0.0101008
   z=20   P=0.0024804
   z=25   P=0.0006132
   z=30   P=0.0001522
   z=35   P=0.0000379
   z=40   P=0.0000095
   z=45   P=0.0000024
   z=50   P=0.0000006

Solving for P less than 0.1%...

若是 P 小于 0.1%……

   P < 0.001
   q=0.10   z=5
   q=0.15   z=8
   q=0.20   z=11
   q=0.25   z=15
   q=0.30   z=24
   q=0.35   z=41
   q=0.40   z=89
   q=0.45   z=340


12. 结论 (Conclusion)

We have proposed a system for electronic transactions without relying on trust. We started with the usual framework of coins made from digital signatures, which provides strong control of ownership, but is incomplete without a way to prevent double-spending. To solve this, we proposed a peer-to-peer network using proof-of-work to record a public history of transactions that quickly becomes computationally impractical for an attacker to change if honest nodes control a majority of CPU power. The network is robust in its unstructured simplicity. Nodes work all at once with little coordination. They do not need to be identified, since messages are not routed to any particular place and only need to be delivered on a best effort basis. Nodes can leave and rejoin the network at will, accepting the proof-of-work chain as proof of what happened while they were gone. They vote with their CPU power, expressing their acceptance of valid blocks by working on extending them and rejecting invalid blocks by refusing to work on them. Any needed rules and incentives can be enforced with this consensus mechanism.


我们设计了一套电子交易系统，该系统不必依赖信任机制。

我们从数字签名这个常规框架出发，这个方案让拥有者强力掌握资产，但无法防止双花问题。

为此我们设计出P2P的网络，用pow方法来记录交易历史，而且攻击者无能为力，只要诚实节点掌控了大部分，CPU算力。

网络是鲁棒性的，节点一连上便开始工作不需要身份识别，因为信息不是传递到一个特别的地方，而是只需要一个好的努力基础，节点来去自由，只要回来时把区块补上即可。节点用CPU算力投票，用扩展来支持有效区块，不在该区块上继续工作，以示拒绝该无效区块。有了这样的同步机制，一切有必要的规则和激励可以增强该系统。


参考文献 (References)

[^1]: b-money Dai Wei (1998-11-01) http://www.weidai.com/bmoney.txt 

[^2]: Design of a secure timestamping service with minimal trust requirements Henri Massias, Xavier Serret-Avila, Jean-Jacques Quisquater 20th Symposium on Information Theory in the Benelux (1999-05) http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.13.6228 

[^3]: How to time-stamp a digital document Stuart Haber, W.Scott Stornetta Journal of Cryptology (1991) https://doi.org/cwwxd4 DOI: 10.1007/bf00196791 

[^4]: Improving the Efficiency and Reliability of Digital Time-Stamping Dave Bayer, Stuart Haber, W. Scott Stornetta Sequences II (1993) https://doi.org/bn4rpx DOI: 10.1007/978-1-4613-9323-8_24 

[^5]: Secure names for bit-strings Stuart Haber, W. Scott Stornetta Proceedings of the 4th ACM conference on Computer and communications security - CCS ’97(1997) https://doi.org/dtnrf6 DOI: 10.1145/266420.266430 

[^6]: Hashcash - A Denial of Service Counter-Measure Adam Back (2002-08-01) http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.15.8 

[^7]: Protocols for Public Key Cryptosystems Ralph C. Merkle 1980 IEEE Symposium on Security and Privacy (1980-04) https://doi.org/bmvbd6 DOI: 10.1109/sp.1980.10006 

[^8]: An Introduction to Probability Theory and its Applications William Feller John Wiley & Sons (1957) https://archive.org/details/AnIntroductionToProbabilityTheoryAndItsApplicationsVolume1


##翻译后记

这是第一次翻译论文，就像捏了一个泥人，怎么看怎么不顺，但这也是对春节假期的不辜负，以后每个假期都要做一件事情，先完成再完美。
翻译原版引用了笑来老师的文本，只是把汉语部分更换成了自己的文字。
第11章因为公式很多，就沿用笑来老师的文章没有改动，待完善时再做修改。
同时感谢李峻老师，他一直在定投践行群讲解编程，这给了我很大的动力，也是我使用Github的原因。
