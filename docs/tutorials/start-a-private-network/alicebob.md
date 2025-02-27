---
title: Alice and Bob Start Blockchain
---

Before we generate our own keys, and start a truly unique Substrate network, let's learn the
fundamentals by starting with a pre-defined network specification called `local` with two
pre-defined (and definitely not private!) keys known as Alice and Bob.

> This portion of the tutorial should be run on a single workstation with a single Substrate binary.
> If you've followed the tutorial up to this point, you have the correct setup.

## Alice Starts First

Alice (or whomever is playing her) should run these commands from node-template repository root.

> Here we've explicitly shown the `purge-chain` command. In the future we will omit this You should
> purge old chain data any time you're trying to start a new network.

```bash
# Purge any chain data from previous runs
# You will be prompted to type `y`
./target/release/node-template purge-chain --base-path /tmp/alice --chain local
```

```bash
# Start Alice's node
./target/release/node-template \
  --base-path /tmp/alice \
  --chain local \
  --alice \
  --port 30333 \
  --ws-port 9945 \
  --rpc-port 9933 \
  --node-key 0000000000000000000000000000000000000000000000000000000000000001 \
  --telemetry-url 'wss://telemetry.polkadot.io/submit/ 0' \
  --validator
```

Let's look at those flags in detail:

| <div style="min-width:110pt"> Flags </div> | Descriptions                                                                                                                                                                                                                                                                                                                               |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `--base-path`                              | Specifies a directory where Substrate should store all the data related to this chain. If this value is not specified, a default path will be used. If the directory does not exist it will be created for you. If other blockchain data already exists there you will get an error. Either clear the directory or choose a different one. |
| `--chain local`                            | Specifies which chain specification to use. There are a few prepackaged options including `local`, `development`, and `staging` but generally one specifies their own chain spec file. We'll specify our own file in a later step.                                                                                                         |
| `--alice`                                  | Puts the predefined Alice keys (both for block production and finalization) in the node's keystore. Generally one should generate their own keys and insert them with an RPC call. We'll generate our own keys in a later step. This flag also makes Alice a validator.                                                                    |
| `--port 30333`                             | Specifies the port that your node will listen for p2p traffic on. `30333` is the default and this flag can be omitted if you're happy with the default. If Bob's node will run on the same physical system, you will need to explicitly specify a different port for it.                                                                   |
| `--ws-port 9945`                           | Specifies the port that your node will listen for incoming WebSocket traffic on. The default value is `9944`. This example uses a custom web socket port number (`9945`).                                                                                                                                                                  |
| `--rpc-port 9933`                          | Specifies the port that your node will listen for incoming RPC traffic on. `9933` is the default, so this parameter may be omitted.                                                                                                                                                                                                        |
| `--node-key <key>`                         | The Ed25519 secret key to use for `libp2p` networking. The value is parsed as a hex-encoded Ed25519 32 byte secret key, i.e. 64 hex characters. WARNING: Secrets provided as command-line arguments are easily exposed. Use of this option should be limited to development and testing.                                                   |
| `--telemetry-url`                          | Tells the node to send telemetry data to a particular server. The one we've chosen here is hosted by Parity and is available for anyone to use. You may also host your own (beyond the scope of this article) or omit this flag entirely.                                                                                                  |
| `--validator`                              | Means that we want to participate in block production and finalization rather than just sync the network.                                                                                                                                                                                                                                  |

When the node starts you should see output similar to this.

```
2021-03-10 17:34:27  Substrate Node    
2021-03-10 17:34:27  ✌️  version 3.0.0-1c5b984-x86_64-linux-gnu    
2021-03-10 17:34:27  ❤️  by Substrate DevHub <https://github.com/substrate-developer-hub>, 2017-2021    
2021-03-10 17:34:27  📋 Chain specification: Local Testnet    
2021-03-10 17:34:27  🏷 Node name: Alice    
2021-03-10 17:34:27  👤 Role: AUTHORITY    
2021-03-10 17:34:27  💾 Database: RocksDb at /tmp/alice/chains/local_testnet/db    
2021-03-10 17:34:27  ⛓  Native runtime: node-template-100 (node-template-1.tx1.au1)    
2021-03-10 17:34:27  🔨 Initializing Genesis block/state (state: 0xea47…9ba8, header-hash: 0x9d07…7cce)    
2021-03-10 17:34:27  👴 Loading GRANDPA authority set from genesis on what appears to be first startup.    
2021-03-10 17:34:27  ⏱  Loaded block-time = 6000 milliseconds from genesis on first-launch    
2021-03-10 17:34:27  Using default protocol ID "sup" because none is configured in the chain specs    
2021-03-10 17:34:27  🏷 Local node identity is: 12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp    
2021-03-10 17:34:27  📦 Highest known block at #0    
2021-03-10 17:34:27  〽️ Prometheus server started at 127.0.0.1:9615    
2021-03-10 17:34:27  Listening for new connections on 127.0.0.1:9945.    
2021-03-10 17:34:32  💤 Idle (0 peers), best: #0 (0x9d07…7cce), finalized #0 (0x9d07…7cce), ⬇ 0 ⬆ 0    
2021-03-10 17:34:37  💤 Idle (0 peers), best: #0 (0x9d07…7cce), finalized #0 (0x9d07…7cce), ⬇ 0 ⬆ 0
...
```

> **Notes**
>
> - `🔨 Initializing Genesis block/state (state: 0xea47…9ba8, header-hash: 0x9d07…7cce)` tells which
>   genesis block the node is using. When you start the next node, verify that these values are
>   equal.
> - `🏷 Local node identity is: 12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp` shows the
>   Peer ID that Bob will need when booting from Alice's node. This value was determined by the
>   `--node-key` that was used to start Alice's node.

You'll notice that no blocks are being produced yet. Blocks will start being produced once another
node joins the network.

More details about all of these flags and others that I haven't mentioned are available by running
`./target/release/node-template --help`.

## Attach a UI

You can tell a lot about your node by watching the output it produces in your terminal. There is
also a nice graphical user interface called Polkadot-JS Apps, or just "Apps" for short.

In your web browser, navigate to
[https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9945#/explorer](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9945#/explorer).

> Some browsers, notably Firefox, will not connect to a local node from a https website. An easy
> work around is to try another browser, like Chromium. Alternatively
> [host this interface locally](https://github.com/polkadot-js/apps#development).

The link provided above includes the `rpc` URL parameter, which instructs the Apps UI to connect to
the URL that was provided as its value (in this case, your local node). To manually configure Apps
UI to connect to another node:

- Click on the top left network icon

  ![Top Left Network Icon](assets/tutorials/private-network/private-network-top-left-network-icon.png)

- A popup dialog appears. Expand **DEVELOPMENT** and ensure the custom endpoint is set to
  `ws://127.0.0.1:9945`.

  ![Select Network](assets/tutorials/private-network/private-network-select-network.png)

- To connect to a custom node and port, you just need to specify the endpoint by choosing
  `custom endpoint` and type in your own endpoint. In this way you can use a single instance of Apps
  UI to connect to various nodes. Click **Switch** icon to actually switch to the new endpoint when
  necessary.

  ![Custom Endpoint](assets/tutorials/private-network/private-network-custom-endpoint.png)

You should now see something like this example from the **Network** and **Explorer** page.

![No blocks in polkadot-js-apps](assets/tutorials/private-network/private-network-no-blocks.png)

> **Notes**
>
> If you do not want to run your hosted version of Polkadot-JS Apps UI while connecting to Substrate
> node you have deployed remotely, you can configure ssh local port forwarding to forward local
> request to the `ws-port` listened by the remote host. Instructions are in the [references](#references).

## Bob Joins

Now that Alice's node is up and running, Bob can join the network by bootstrapping from her node.
His command will look very similar.

```bash
./target/release/node-template purge-chain --base-path /tmp/bob --chain local
```

```bash
./target/release/node-template \
  --base-path /tmp/bob \
  --chain local \
  --bob \
  --port 30334 \
  --ws-port 9946 \
  --rpc-port 9934 \
  --telemetry-url 'wss://telemetry.polkadot.io/submit/ 0' \
  --validator \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp
```

Most of these options are already explained above, but there are a few points worth mentioning.

- Because these two nodes are running on the same physical machine, Bob must specify different
  `--base-path`, `--port`, `--ws-port`, and `--rpc-port` values.
- Bob has added the `--bootnodes` flag and specified a single boot node, namely Alice's. He must
  correctly specify these three pieces of information which Alice can supply for him.
  - Alice's IP Address, probably `127.0.0.1`
  - Alice's Port, she specified `30333`
  - Alice's Peer ID, copied from her log output.

If all is going well, after a few seconds, the nodes should peer together and start producing
blocks. You should see some lines like the following in the console that started Alice node.

```
...
2021-03-10 17:47:32  🔍 Discovered new external address for our node: /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp    
2021-03-10 17:47:32  🔍 Discovered new external address for our node: /ip4/<your computer's LAN IP>/tcp/30333/p2p/12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp    
2021-03-10 17:47:33  💤 Idle (1 peers), best: #0 (0x9d07…7cce), finalized #0 (0x9d07…7cce), ⬇ 1.0kiB/s ⬆ 1.0kiB/s    
2021-03-10 17:47:36  🙌 Starting consensus session on top of parent 0x9d07d1757a9ca248e58141ce52a11fca37f71007dec16650b87a853f0d4c7cce    
2021-03-10 17:47:36  🎁 Prepared block for proposing at 1 [hash: 0x727826a5e6fba9a13af11422d4677b5f0743cc733c382232328e69fd307d1d2f; parent_hash: 0x9d07…7cce; extrinsics (1): [0x768a…a9e2]]    
2021-03-10 17:47:36  🔖 Pre-sealed block for proposal at 1. Hash now 0x4841d8b2e62483fa4702b3ddcd1b603803842374dcdc1e9533ad407708b33dd8, previously 0x727826a5e6fba9a13af11422d4677b5f0743cc733c382232328e69fd307d1d2f.    
2021-03-10 17:47:36  ✨ Imported #1 (0x4841…3dd8)    
2021-03-10 17:47:36  ✨ Imported #1 (0xb241…2ae8)    
2021-03-10 17:47:38  💤 Idle (1 peers), best: #1 (0x4841…3dd8), finalized #0 (0x9d07…7cce), ⬇ 0.8kiB/s ⬆ 0.8kiB/s    
2021-03-10 17:47:42  ♻️  Reorg on #1,0x4841…3dd8 to #2,0x8b6a…dce6, common ancestor #0,0x9d07…7cce    
2021-03-10 17:47:42  ✨ Imported #2 (0x8b6a…dce6)    
2021-03-10 17:47:43  💤 Idle (1 peers), best: #2 (0x8b6a…dce6), finalized #0 (0x9d07…7cce), ⬇ 0.8kiB/s ⬆ 0.7kiB/s    
2021-03-10 17:47:48  🙌 Starting consensus session on top of parent 0x8b6a3ab2fe9891b1af008ea0d92dae9bc84cfa5578231e81066d47928822dce6    
2021-03-10 17:47:48  🎁 Prepared block for proposing at 3 [hash: 0xb887aef2097eff5869e38ccec0302bce372ad05ac2cdf9cc4725c38ec071fb7a; parent_hash: 0x8b6a…dce6; extrinsics (1): [0x82ac…2f20]]    
2021-03-10 17:47:48  🔖 Pre-sealed block for proposal at 3. Hash now 0x34d608dd8be6b82bef4a7aaae1ec80930a5c4b8cf9bdc99013410e91544f3a2a, previously 0xb887aef2097eff5869e38ccec0302bce372ad05ac2cdf9cc4725c38ec071fb7a.    
2021-03-10 17:47:48  ✨ Imported #3 (0x34d6…3a2a)    
2021-03-10 17:47:48  💤 Idle (1 peers), best: #3 (0x34d6…3a2a), finalized #0 (0x9d07…7cce), ⬇ 0.7kiB/s ⬆ 0.8kiB/s    
2021-03-10 17:47:53  💤 Idle (1 peers), best: #3 (0x34d6…3a2a), finalized #1 (0xb241…2ae8), ⬇ 0.6kiB/s ⬆ 0.7kiB/s    
2021-03-10 17:47:54  ✨ Imported #4 (0x2b8a…fdc4)    
2021-03-10 17:47:58  💤 Idle (1 peers), best: #4 (0x2b8a…fdc4), finalized #2 (0x8b6a…dce6), ⬇ 0.7kiB/s ⬆ 0.6kiB/s
...
```

These lines shows that Bob has peered with Alice (**`1 peers`**), they have produced some blocks
(**`best: #4 (0x2b8a…fdc4)`**), and blocks are being finalized (**`finalized #2 (0x8b6a…dce6)`**).

Looking at the console that started Bob's node, you should see something similar.

> Once you've verified that both nodes are running as expected, you should shut them down. Although 
> this is not strictly required, so long as you don't have conflicting ports and the same chainspec.
> The next section of this tutorial will include commands to restart new nodes when necessary.

## References

- [Configure ssh local port forwarding](https://www.booleanworld.com/guide-ssh-port-forwarding-tunnelling/)
