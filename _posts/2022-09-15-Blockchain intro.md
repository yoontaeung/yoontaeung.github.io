---
title: Blockchain - Intro, Accounts (Eng)
categories: [Computer Science, Blockchain, Ethereum]
tags: [blockchain, ethereum, layer2, accounts]     # TAG names should always be lowercase
author: <author_id>
---
# Blockchain - Intro, Accounts

This article is to help understand what blockchain is, EVM or etc. based on the slides designed by [Takenobu T. Ethereum EVM illustrated](https://takenobu-hs.github.io/downloads/ethereum_evm_illustrated.pdf). If you have any questions, please comment below. 

## Blockchain - a transaction-based state machine

According to Wikipedia, a state machine is “an abstract machine that can be in exactly one of a finite number of states at any given time”. The diagram below shows a simple state diagram for a turnstile. There are two possible states(locked and un-locked), and two possible inputs(coin and push). If we put a coin in Locked current state, then the next state would be changed into un-locked state.  

![Untitled](/assets/img/state_machine.png)

Going back to Ethereum, Ethereum can be viewed as a transaction-based state machine. A transaction is an event that changes from world state  $σ_t$ to world state  $σ_t+1$. As the time goes, the possible states are continuously created and every next state are influenced by the previous state and transaction. 

The world state includes so many information. If we make a new state everytime the event happens, there might be too many world states that we need to describe. Therefore, those events(transactions) are collated into a block. The number of transactions that can be collated into a single block would be affected by the size of a block gas limit, congestion of the network, and types of transactions. Since London Hardfork, the maximum gas limit of a single block is about 30M gas. To keep the maximum gas limit, we could maintain the block creation speed minimum so that the decentralization cannot be harmed.

> From the viewpoint of the implementation, Ethereum can also be seen as a chain of blocks, so it is called ‘BLOCKCHAIN’
> 

## Accounts

So what’s inside a world state? Numerous accounts exist in the state with their own account state. Also, each account has their own address. There are two types of accounts. EOA(externally owned account) and contract account.  

EOA is controlled by someone who has the private key. On the other hand, contract account is controlled by the code. Both are the same Ethereum accounts and they can receive, hold, and send ETH. Also, they can interact with other on-chain smart contracts. 

With metamask, the most famous crypto wallet, you can easily make your EOA. However, making an EOA can be done with a few lines of codes. It is just a process of generating a public key with the given random private key using the [Elliptic Curve Digital Signature Algorithm](https://wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm). Also, the digital signature is required when the recipient needs to verify with the sender’s public key. This digital signature can be generated only by the sender’s private key. ECDSA is the way to satisfy both encode and decode. Of course, it is impossible to get private keys from public keys. If you want to learn more about this, take a look at [public-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography).

A contract account is controlled by the code and is excuted by the Ethereum Virtual Machine. This address is given when a contract is deployed to the Ethereum network. The address of a contract is generated using the sender’s address and nonce value(how many transactions sent from that address). 

> The address of the new account is defined as being the rightmost 160 bits of the Keccak hash of the RLP encoding of the structure containing only the sender and the account
nonce. Thus we define the resultant address for the new account a :
> 
> 
> ![Screen Shot 2022-09-15 at 1.57.06 AM.png](/assets/img/Screen_Shot_2022-09-15_at_1.57.06_AM.png)
> 

KEC is the Keccak 256-bit hash function, and RLP is the RLP encoding function. You can find more detailed info on Ethereum Yellow Paper.

<br /><br /><br /><br /><br /><br />
<div id="disqus_thread"></div>
<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
    /*
    var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://yoontaeung-github-io.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>