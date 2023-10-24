# Deployment


## Contract deploy

### 1. git clone the source from the following command:
```
git clone git@github.com:UptickNetwork/Uptick-DID-Contract.git
```


### 2. install the package
```
yarn install
```

### 3. deploy VerifierV2
```
yarn scripts scripts/0.deployVerifierV2.ts --network mainnet
```

### 4. deploy Poseidon Elements
```
yarn scripts scripts/1.deployPoseidonElements.ts --network mainnet
```

### 5. deploy Poseidon Elements
```
yarn scripts scripts/1.deployPoseidonElements.ts --network mainnet
```

### 6. deploy SmtLib
```
yarn scripts scripts/2.deploySmtLib.ts --network mainnet
```

### 7. deploy StateLib
```
yarn scripts scripts/3.stateLib.ts --network mainnet
```

### 8. deploy stateV2
```
yarn scripts scripts/4.stateV2.ts --network mainnet
```

## Issue deploy

### 1. git clone the source from the following command:
```
git clone git@github.com:UptickNetwork/DID-issuer-node.git
```

### 2. Copy .env-api.sample as .env-api and .env-issuer.sample as .env-issuer. Please see the configuration section for more details:

- .env-api
```
ISSUER_API_UI_SERVER_URL=http://localhost:3002
ISSUER_API_UI_SERVER_PORT=3002
ISSUER_API_UI_AUTH_USER=user-api
ISSUER_API_UI_AUTH_PASSWORD=password-api
ISSUER_API_UI_ISSUER_NAME=my issuer
ISSUER_API_UI_ISSUER_LOGO=uptick
ISSUER_API_UI_SCHEMA_CACHE=false
ISSUER_API_IDENTITY_METHOD=iden3
ISSUER_API_IDENTITY_BLOCKCHAIN=uptick
ISSUER_API_IDENTITY_NETWORK=main
ISSUER_API_ENVIRONMENT=local
ISSUER_API_UI_ISSUER_DID=
```
- .env-issuer
```
ISSUER_SERVER_URL=http://localhost:3003
ISSUER_SERVER_PORT=3003
ISSUER_NATIVE_PROOF_GENERATION_ENABLED=true
ISSUER_PUBLISH_KEY_PATH=pbkey
ISSUER_ONCHAIN_PUBLISH_STATE_FREQUENCY=1m
ISSUER_ONCHAIN_CHECK_STATUS_FREQUENCY=1m
ISSUER_DATABASE_URL=postgres://uptickdid:uptickdid@postgres:5432/platformid?sslmode=disable
ISSUER_IPFS_GATEWAY_URL=https://ipfs.io
ISSUER_LOG_LEVEL=-4
ISSUER_LOG_MODE=2
ISSUER_API_AUTH_USER=user-issuer
ISSUER_API_AUTH_PASSWORD=password-issuer
ISSUER_KEY_STORE_ADDRESS=http://vault:8200
ISSUER_KEY_STORE_PLUGIN_IDEN3_MOUNT_PATH=iden3
ISSUER_REVERSE_HASH_SERVICE_URL=http://localhost:3001
ISSUER_REVERSE_HASH_SERVICE_ENABLED=false
ISSUER_ETHEREUM_URL=https://json-rpc.uptick.network
ISSUER_ETHEREUM_CONTRACT_ADDRESS=0x2F4E9C03e077F923BeEe6102314e6C01228C67EA
ISSUER_ETHEREUM_DEFAULT_GAS_LIMIT=600000
ISSUER_ETHEREUM_CONFIRMATION_TIME_OUT=600s
ISSUER_ETHEREUM_CONFIRMATION_BLOCK_COUNT=5
ISSUER_ETHEREUM_RECEIPT_TIMEOUT=600s
ISSUER_ETHEREUM_MIN_GAS_PRICE=0
ISSUER_ETHEREUM_MAX_GAS_PRICE=1000000
ISSUER_ETHEREUM_RPC_RESPONSE_TIMEOUT=5s
ISSUER_ETHEREUM_WAIT_RECEIPT_CYCLE_TIME=30s
ISSUER_ETHEREUM_WAIT_BLOCK_CYCLE_TIME=30s
ISSUER_ETHEREUM_RESOLVER_PREFIX=uptick:main
ISSUER_PROVER_SERVER_URL=http://localhost:8002
ISSUER_PROVER_TIMEOUT=600s
ISSUER_CIRCUIT_PATH=./pkg/credentials/circuits
ISSUER_REDIS_URL=redis://@redis:6379/1
ISSUER_SCHEMA_CACHE=false
ISSUER_KEY_STORE_TOKEN=
```

- .env-issuer
```
ISSUER_UI_AUTH_USERNAME=user-ui
ISSUER_UI_AUTH_PASSWORD=password-ui
ISSUER_UI_BLOCK_EXPLORER_URL=https://evm-explorer.uptick.network
ISSUER_UI_BUILD_TAG=
ISSUER_UI_WARNING_MESSAGE=
ISSUER_UI_IPFS_GATEWAY_URL=
ISSUER_UI_SCHEMA_EXPLORER_AND_BUILDER_URL=
```

### 3.Run make up 
This launches 3 containers with Postgres, Redis and Vault. Ignore the warnings about variables, since those are set up in the next step.
```
make up
```

### 4.run make run
 This starts up the issuer API, whose frontend can be accessed via the browser (default http://localhost:3001).
```
make run
```

### 5.Import Wallet Private Key To Vault
In order to secure the wallet private key so that the issuer can use it to issue credentials, it must be stored in the Hashicorp Vault.
```
make private_key=<YOUR_WALLET_PRIVATE_KEY> add-private-key;
```

### 6.Add Vault To Configuration File
This will get the vault token from the Hashicorp vault docker instance and add it to our ./env-issuer file.
```
make add-vault-token;
```

### 7.Create Issuer DID
This will create a new issuer DID by creating a new Docker instance of the issuer, generating the DID of the issuer, storing it in the database, then deleting the instance.
```
make generate-issuer-did;
```

## reverse-hash-service

### 1. git clone the source from the following command:
```
git clone git@github.com:uptSmart/reverse-hash-service.git
```

### 2.Run docker-compose
```
docker-compose up -d
```

### 3.Copy schema.sql to container with postgres
```
docker cp schema.sql <db_container_name>:/
```
### 4.Exec to container
```
docker exec -it <db_container_name> /bin/bash
```
### 5.Create rhs db
```
createdb -U iden3 -h localhost rhs 
```
### 6.Upload schema.sql inside on docker container
```
psql -h localhost -U iden3  -d rhs < schema.sql
```

## Run Js sample

Run Js sample to check uptick did deployment

### 1. git clone the source from the following command:
```
git clone git@github.com:UptickNetwork/DID-js-demo.git
```

### 2. install package 
```
yarn install
```

### 3. start the test case 
```
yarn start
```
