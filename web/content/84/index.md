
+++
title = "Derivation scheme for P2WPKH based accounts"
date = 2017-12-28
weight = 84

[taxonomies]
authors = ["Pavol Rusnak"]
status = ["Final"]

[extra]
bip = 84
status = ["Final"]
github = "https://github.com/bitcoin/bips/blob/master/bip-0084.mediawiki"
note = "THIS FILE IS AUTOMATICALLY GENERATED - NOT MEANT FOR EDITING"
+++

```
  BIP: 84
  Layer: Applications
  Title: Derivation scheme for P2WPKH based accounts
  Author: Pavol Rusnak <stick@satoshilabs.com>
  Comments-Summary: No comments yet.
  Comments-URI: https://github.com/bitcoin/bips/wiki/Comments:BIP-0084
  Status: Final
  Type: Standards Track
  Created: 2017-12-28
  License: CC0-1.0
```

<h2>Abstract</h2>


This BIP defines the derivation scheme for HD wallets using the P2WPKH (<a href="/173" target="_blank">BIP 173</a>) serialization format for segregated witness transactions.

<h2>Motivation</h2>


With the usage of P2WPKH transactions it is necessary to have a common derivation scheme.
It allows the user to use different HD wallets with the same masterseed and/or a single account seamlessly.

Thus the user needs to create dedicated segregated witness accounts, which ensures that only wallets compatible with this BIP will detect the accounts and handle them appropriately.

<h3>Considerations</h3>


We use the same rationale as described in Considerations section of <a href="/49" target="_blank">BIP 49</a>.

<h2>Specifications</h2>


This BIP defines the two needed steps to derive multiple deterministic addresses based on a <a href="/32" target="_blank">BIP 32</a> root account.

<h3>Public key derivation</h3>


To derive a public key from the root account, this BIP uses the same account-structure as defined in <a href="/44" target="_blank">BIP 44</a> and <a href="/49" target="_blank">BIP 49</a>, but only uses a different purpose value to indicate the different transaction serialization method.

```
m / purpose' / coin_type' / account' / change / address_index
```

For the `purpose`-path level it uses `84'`. The rest of the levels are used as defined in BIP44 or BIP49.


<h3>Address derivation</h3>


To derive the P2WPKH address from the above calculated public key, we use the encapsulation defined in <a href="/141" target="_blank">BIP 141</a>:


```
    witness:      <signature> <pubkey>
    scriptSig:    (empty)
    scriptPubKey: 0 <20-byte-key-hash>
                  (0x0014{20-byte-key-hash})
```



<h3>Extended Key Version</h3>


When serializing extended keys, this scheme uses alternate version bytes. Extended public keys use `0x04b24746` to produce a "zpub" prefix, and private keys use `0x04b2430c` to produce a "zprv" prefix. Testnet uses `0x045f1cf6` "vpub" and `0x045f18bc` "vprv."

Additional registered version bytes are listed in <a href="https://github.com/satoshilabs/slips/blob/master/slip-0132.md" target="_blank">SLIP-0132</a>.


<h2>Backwards Compatibility</h2>


This BIP is not backwards compatible by design as described under <a href="#considerations" target="_blank">considerations</a>. An incompatible wallet will not discover accounts at all and the user will notice that something is wrong.

<h2>Test vectors</h2>


```
  mnemonic = abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about
  rootpriv = zprvAWgYBBk7JR8Gjrh4UJQ2uJdG1r3WNRRfURiABBE3RvMXYSrRJL62XuezvGdPvG6GFBZduosCc1YP5wixPox7zhZLfiUm8aunE96BBa4Kei5
  rootpub  = zpub6jftahH18ngZxLmXaKw3GSZzZsszmt9WqedkyZdezFtWRFBZqsQH5hyUmb4pCEeZGmVfQuP5bedXTB8is6fTv19U1GQRyQUKQGUTzyHACMF

  // Account 0, root = m/84'/0'/0'
  xpriv = zprvAdG4iTXWBoARxkkzNpNh8r6Qag3irQB8PzEMkAFeTRXxHpbF9z4QgEvBRmfvqWvGp42t42nvgGpNgYSJA9iefm1yYNZKEm7z6qUWCroSQnE
  xpub  = zpub6rFR7y4Q2AijBEqTUquhVz398htDFrtymD9xYYfG1m4wAcvPhXNfE3EfH1r1ADqtfSdVCToUG868RvUUkgDKf31mGDtKsAYz2oz2AGutZYs

  // Account 0, first receiving address = m/84'/0'/0'/0/0
  privkey = KyZpNDKnfs94vbrwhJneDi77V6jF64PWPF8x5cdJb8ifgg2DUc9d
  pubkey  = 0330d54fd0dd420a6e5f8d3624f5f3482cae350f79d5f0753bf5beef9c2d91af3c
  address = bc1qcr8te4kr609gcawutmrza0j4xv80jy8z306fyu

  // Account 0, second receiving address = m/84'/0'/0'/0/1
  privkey = Kxpf5b8p3qX56DKEe5NqWbNUP9MnqoRFzZwHRtsFqhzuvUJsYZCy
  pubkey  = 03e775fd51f0dfb8cd865d9ff1cca2a158cf651fe997fdc9fee9c1d3b5e995ea77
  address = bc1qnjg0jd8228aq7egyzacy8cys3knf9xvrerkf9g

  // Account 0, first change address = m/84'/0'/0'/1/0
  privkey = KxuoxufJL5csa1Wieb2kp29VNdn92Us8CoaUG3aGtPtcF3AzeXvF
  pubkey  = 03025324888e429ab8e3dbaf1f7802648b9cd01e9b418485c5fa4c1b9b5700e1a6
  address = bc1q8c6fshw2dlwun7ekn9qwf37cu2rn755upcp6el
```

<h2>Reference</h2>


*  <a href="/32" target="_blank">BIP32 - Hierarchical Deterministic Wallets</a>
*  <a href="/43" target="_blank">BIP43 - Purpose Field for Deterministic Wallets</a>
*  <a href="/44" target="_blank">BIP44 - Multi-Account Hierarchy for Deterministic Wallets</a>
*  <a href="/49" target="_blank">BIP49 - Derivation scheme for P2WPKH-nested-in-P2SH based accounts</a>
*  <a href="/141" target="_blank">BIP141 - Segregated Witness (Consensus layer)</a>
*  <a href="/173" target="_blank">BIP173 - Base32 address format for native v0-16 witness outputs</a>
