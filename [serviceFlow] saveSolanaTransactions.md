# How to save solana transactions on on-chain network in RDB?


- solanan web3 api docs (https://solana-labs.github.io/solana-web3.js/)


## 1. Get wallet
- userId 로 저장된 wallet address 조회

## 2. Get signatures
- wallet address (이하 public key) 로 signature list 를 조회한다.
- https://solana-labs.github.io/solana-web3.js/classes/Connection.html#getSignaturesForAddress
    - before: null
    - limit: n
- response array 가 empty 면 한번도 sol거래가 없었던 지갑임으로 **종료**.

## 3. Get Cached Transactions data
- order by **trnasaction_date** desc;
- 저장된 거래 중에 가장 최신 signautre 조회 (이하 latest signature)
    -  cachedTransaction[0].signature
- ached transaction response array 가 empty 면 한번도 거래내역 저장이 일어나지 않았음으로, 2에서 조회된 모든 데이터 저장 해야함.

## 4. Compare signatures 2 and 3
- 2의 signatures array 에서 3의 latest signature가 몇 번째인지 확인한다.
- index 0 이라면 최신 데이터가 저장된 것 임으로 **종료**.
- cached transaction 에서 조회된 내역 **return**.

## 5. Get Transactions 
https://solana-labs.github.io/solana-web3.js/classes/Connection.html#getTransactions

### case 1. 신규거래 추가 저장 (index of > 0)
- 2의 signatures.slice(0, 4의 index)한 signatures array로 transaction 조회 및 저장

### case 2. n번 이상의 신규 거래 발생 (indes of == -1)
- 2의 signatures array로 transactions 조회 및 저장
- 2의 signatures[n-1] (마지막 signautre)로 2 → 4 → 5 반복
- https://solana-labs.github.io/solana-web3.js/classes/Connection.html#getSignaturesForAddress
    - before: signatures[n-1]
    - limit: n
