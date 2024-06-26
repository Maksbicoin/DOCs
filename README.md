# DOCs/
services:
  geth: # this is Optimism's geth client
    build: .
    ports:
      - 8545:8545
      - 8546:8546
      - 30303:30303
    command: [ "sh", "./geth-entrypoint.sh" ]
    environment:
      - OP_GETH_GENESIS_FILE_PATH=goerli/genesis-l2.json
      - OP_GETH_SEQUENCER_HTTP=https://goerli.base.org
      - OP_NODE_L2_ENGINE_AUTH=/tmp/engine-auth-jwt
      - OP_NODE_L2_ENGINE_AUTH_RAW=688f5d737bad920bdfb2fc2f488d6b6209eebda1dae949a8de91398d932c517a # for localdev only
      # - OP_GETH_ETH_STATS=nodename:secret@host:port # optional
  node:
    build: .
    depends_on:
      - geth
    ports:
      - 7545:8545
      - 9222:9222
      - 7300:7300 # metrics
      - 6060:6060
    command: [ "sh", "./op-node-entrypoint.sh" ]
    environment:
      - OP_NODE_L1_ETH_RPC=https://ethereum-goerli-rpc.allthatnode.com # [recommended] replace with your preferred L1 (ethereum, not Base) node RPC URL
      - OP_NODE_L2_ENGINE_AUTH=/tmp/engine-auth-jwt
      - OP_NODE_L2_ENGINE_AUTH_RAW=688f5d737bad920bdfb2fc2f488d6b6209eebda1dae949a8de91398d932c517a # for localdev only
      - OP_NODE_L2_ENGINE_RPC=http://geth:8551
      - OP_NODE_LOG_LEVEL=info
      - OP_NODE_METRICS_ADDR=0.0.0.0
      - OP_NODE_METRICS_ENABLED=true
      - OP_NODE_METRICS_PORT=7300
      - OP_NODE_P2P_AGENT=base
      - OP_NODE_P2P_BOOTNODES=enr:-J64QBbwPjPLZ6IOOToOLsSjtFUjjzN66qmBZdUexpO32Klrc458Q24kbty2PdRaLacHM5z-cZQr8mjeQu3pik6jPSOGAYYFIqBfgmlkgnY0gmlwhDaRWFWHb3BzdGFja4SzlAUAiXNlY3AyNTZrMaECmeSnJh7zjKrDSPoNMGXoopeDF4hhpj5I0OsQUUt4u8uDdGNwgiQGg3VkcIIkBg,enr:-J64QAlTCDa188Hl1OGv5_2Kj2nWCsvxMVc_rEnLtw7RPFbOfqUOV6khXT_PH6cC603I2ynY31rSQ8sI9gLeJbfFGaWGAYYFIrpdgmlkgnY0gmlwhANWgzCHb3BzdGFja4SzlAUAiXNlY3AyNTZrMaECkySjcg-2v0uWAsFsZZu43qNHppGr2D5F913Qqs5jDCGDdGNwgiQGg3VkcIIkBg
      - OP_NODE_P2P_LISTEN_IP=0.0.0.0
      - OP_NODE_P2P_LISTEN_TCP_PORT=9222
      - OP_NODE_P2P_LISTEN_UDP_PORT=9222
      - OP_NODE_ROLLUP_CONFIG=goerli/rollup.json
      - OP_NODE_RPC_ADDR=0.0.0.0
      - OP_NODE_RPC_PORT=8545
      - OP_NODE_SNAPSHOT_LOG=/tmp/op-node-snapshot-log
      - OP_NODE_VERIFIER_L1_CONFS=0
#     OP_NODE_L1_TRUST_RPC allows for faster syncing, but should be used *only* if your L1 RPC node
#     is fully trusted.  It also allows op-node to work with clients such as Erigon that do not
#     support storage proofs.
#     - OP_NODE_L1_TRUST_RPC=true
