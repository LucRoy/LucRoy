# Protocol Labs Estuary
 
## Goals
The goal of this tutorial is to deploy an estuary node and seal a filecoin deal.
 
## Repository
- https://github.com/application-research/estuary
 
## Installation
### Estuary
1. For this tutorial, we will choose to deploy a estuary node on Ubuntu 22.04. We can start by installing our dependencies. From a terminal, you can execute the following commands.
 
```
sudo apt-get install -y wget jq hwloc ocl-icd-opencl-dev git libhwloc-dev pkg-config make golang-go cargo curl
```
We will also need to install rust and add the cargo bin to our PATH.
```
curl https://sh.rustup.rs -sSf | bash -s -- -y
PATH="/root/.cargo/bin:${PATH}"
```
 
2. Once we have the dependencies installed, we can go ahead and pull the project locally.
```
git clone https://github.com/application-research/estuary.git
cd estuary
```
 
3. We can now build the project by running.
```
make clean all
```
 
4. Now we can initialize the database and create our authentication token by running.
```
./estuary setup
auth token: EST51e7f8aa-762c-43b7-b5ce-5967ba665131ARY
```
**_NOTE:_**  Make sure to save your auth token, since it will required for interacting with the REST API.
 
5. Set the FULLNODE_API_INFO environment variable to a synced lotus node.
```
export FULLNODE_API_INFO=wss://api.chain.love
```
 
6. You are now ready to start your estuary node.
```
$ ./estuary --logging
```
 
This will also print out your wallet address.
```
2022-07-18T23:16:30.424Z        INFO    estuary estuary/main.go:526     estuary version: v0.1.4 {"app_version": "v0.1.4"}
Wallet address is:  f1mugdi5c3vel2af5gv6kptb7ew67hujzklsemdea
2022/07/18 23:16:30 failed to sufficiently increase receive buffer size (was: 208 kiB, wanted: 2048 kiB, got: 416 kiB). See https://github.com/lucas-clemente/quic-go/wiki/UDP-Receive-Buffer-Size for details.
2022-07-18T23:16:30.933Z        INFO    dt-impl impl/impl.go:145        start data-transfer module
/ip4/192.168.86.67/tcp/6744/p2p/12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9Fys
/ip4/127.0.0.1/tcp/6744/p2p/12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9Fys
 
   ____    __
  / __/___/ /  ___
 / _// __/ _ \/ _ \
/___/\__/_//_/\___/ v4.6.1
High performance, minimalist Go web framework
https://echo.labstack.com
____________________________________O/_______
                                    O\
⇨ http server started on [::]:3004
```
 
7. Go to https://verify.glif.io/ and log in with your github account. Once logged in, request a verified data allowance to your address. An allowance of 32GiB should now be added to your wallet.
 
### Shuttle
1. Now that we have an estuary node running, we will need to get a shuttle node running as well. The shuttle is used for interacting with IPFS and the Filecoin Network. We already built it with our previous `make all` cmd, therefore we can make the below call to initialize it. Don't forget to change the Bearer Token (auth token) to the token saved from your previous step.
 
```
$ curl -H "Authorization: Bearer ESTb43c2f9c-9832-498a-8300-35d9c4b8c16eARY" -X POST localhost:3004/admin/shuttle/init
```
This will return two tokens that will then be used to deploy our shuttle node.
```
{"handle":"SHUTTLEef49824c-f440-45e9-951a-13a5fc2891c9HANDLE","token":"SECRET57733c54-a3b0-40cf-a3d3-cd9274cfdeb9SECRET"}
```
 
2. Deploy a shuttle node by supplying the below command with the output from the previous step.
```
./estuary-shuttle --dev --estuary-api=localhost:3004 --auth-token=SECRET57733c54-a3b0-40cf-a3d3-cd9274cfdeb9SECRET --handle=SHUTTLEef49824c-f440-45e9-951a-13a5fc2891c9HANDLE --logging --host=localhost:3005
```
 
```
2022-07-18T23:16:27.211Z        INFO    shuttle estuary-shuttle/main.go:365     shuttle version: v0.1.4 {"app_version": "v0.1.4"}
2022/07/18 23:16:27 failed to sufficiently increase receive buffer size (was: 208 kiB, wanted: 2048 kiB, got: 416 kiB). See https://github.com/lucas-clemente/quic-go/wiki/UDP-Receive-Buffer-Size for details.
Wallet address is:  f1zgpvpukgmvh7izlsypqymn43yqvp7iuosruquwq
2022-07-18T23:16:27.581Z        INFO    dt-impl impl/impl.go:145        start data-transfer module
2022-07-18T23:16:27.583Z        INFO    shuttle estuary-shuttle/main.go:1694    refreshing 0 pins       {"app_version": "v0.1.4"}
 
   ____    __
  / __/___/ /  ___
 / _// __/ _ \/ _ \
/___/\__/_//_/\___/ v4.6.1
High performance, minimalist Go web framework
https://echo.labstack.com
____________________________________O/_______
                                    O\
⇨ http server started on [::]:3005
```
 
3. You should now have an estuary and shuttle node running.
 
## Uploading File
1. For this tutorial, we will choose a file that is greater than the size of the staging threshold (3.57 GiB). This way we can skip over the batching process and go directly looking for deals.
https://docs.estuary.tech/tutorial-uploading-your-first-file
 
```
curl -X POST http://localhost:3004/content/add -H "Authorization: Bearer EST51e7f8aa-762c-43b7-b5ce-5967ba665131ARY" -H "Content-Type: multipart/form-data" -F "data=@./estuary-test-data.rar"
{
   "cid":"bafybeiaq4txuk5ksmmeg2273ftxwnktpj6ut4u372rnlhrl7xt54vmzodm",
   "estuaryId":1,
   "providers":          ["/ip4/192.168.86.67/tcp/6744/p2p/12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9Fys","/ip4/127.0.0.1/tcp/6744/p2p/12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9F  ys","/ip4/192.168.2.65/tcp/14868/p2p/12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9Fys","/ip4/142.162.121.3/tcp/14868/p2p/12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAd LVVfSp9Fys"]
}
```
When successful, the response will give us the estuaryId (1), which can be used to query content data.

2. Once the file is uploaded, we lookup if we have deals. Deals will be made automatically but it might take a while to seal a deal.
https://docs.estuary.tech/api-content-deals
 
```
curl -X GET -H "Authorization: Bearer EST51e7f8aa-762c-43b7-b5ce-5967ba665131ARY" http://localhost:3004/content/deals
[
   {
      "active" : true,
      "aggregate" : false,
      "aggregatedFiles" : 0,
      "aggregatedIn" : 0,
      "cid" : "bafybeiaq4txuk5ksmmeg2273ftxwnktpj6ut4u372rnlhrl7xt54vmzodm",
      "dagSplit" : false,
      "description" : "",
      "failed" : false,
      "id" : 1,
      "location" : "local",
      "name" : "estuary-test-data.rar",
      "offloaded" : false,
      "origins" : "",
      "pinMeta" : "",
      "pinning" : false,
      "replace" : false,
      "replication" : 6,
      "size" : 4412579774,
      "splitFrom" : 0,
      "type" : 0,
      "updatedAt" : "2022-07-18T22:51:15.683961916Z",
      "userId" : 1
   }
]
```
 
We can also query deals for speficic content.
https://docs.estuary.tech/api-content-status-id
 
```
curl -X GET -H "Authorization: Bearer EST51e7f8aa-762c-43b7-b5ce-5967ba665131ARY" http://192.168.86.67:3004/content/status/1
...
      {
         "deal" : {
            "CreatedAt" : "2022-07-19T11:49:47.955964797Z",
            "DeletedAt" : null,
            "ID" : 10,
            "UpdatedAt" : "2022-07-19T11:49:47.955964797Z",
            "content" : 1,
            "dealId" : 0,
            "dealUuid" : "9cd42a41-a570-4be9-b183-c3f14175a133",
            "dtChan" : "12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9Fys-12D3KooWKAd5C78zMyqbaMCm7Pt9CMyAy6eoJNzedadUuiDBkfhY-1658230750730020035",
            "failed" : false,
            "failedAt" : "0001-01-01T00:00:00Z",
            "miner" : "f019551",
            "onChainAt" : "0001-01-01T00:00:00Z",
            "propCid" : "bafyreidf32iln6o2jlaw4bpryqglncjdh4lfdbk3x5g36qijjzr4jnytva",
            "sealedAt" : "0001-01-01T00:00:00Z",
            "slashed" : false,
            "transferFinished" : "0001-01-01T00:00:00Z",
            "transferStarted" : "2022-07-19T11:49:48.733949263Z",
            "user_id" : 1,
            "verified" : true
         },
         "onChainState" : null,
         "transfer" : {
            "Stages" : {
               "Stages" : [
                  {
                     "CreatedTime" : "2022-07-19T11:49:48.729901282Z",
                     "Description" : "",
                     "Logs" : null,
                     "Name" : "Requested",
                     "UpdatedTime" : "2022-07-19T11:49:48.946699973Z"
                  },
                  {
                     "CreatedTime" : "2022-07-19T11:49:48.947572634Z",
                     "Description" : "",
                     "Logs" : [
                        {
                           "Log" : "sending data",
                           "UpdatedTime" : "2022-07-19T11:49:48.955677401Z"
                        }
                     ],
                     "Name" : "Ongoing",
                     "UpdatedTime" : "2022-07-19T11:51:23.416604814Z"
                  }
               ]
            },
            "baseCid" : "bafybeiaq4txuk5ksmmeg2273ftxwnktpj6ut4u372rnlhrl7xt54vmzodm",
            "channelId" : {
               "ID" : 1658230750730020035,
               "Initiator" : "12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9Fys",
               "Responder" : "12D3KooWKAd5C78zMyqbaMCm7Pt9CMyAy6eoJNzedadUuiDBkfhY"
            },
            "message" : "",
            "received" : 0,
            "remotePeer" : "12D3KooWKAd5C78zMyqbaMCm7Pt9CMyAy6eoJNzedadUuiDBkfhY",
            "selfPeer" : "12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9Fys",
            "sent" : 154192161,
            "status" : 1,
            "statusMessage" : "Ongoing",
            "transferId" : "12D3KooWLmmKgFs7Jutjnhhic2V7JJhf4t9uTz4NAdLVVfSp9Fys-12D3KooWKAd5C78zMyqbaMCm7Pt9CMyAy6eoJNzedadUuiDBkfhY-1658230750730020035"
         }
      }
   ],
   "failuresCount" : 9
...
```
 
3. Congratulations!!! You have now successfully sealed a filecoin deal.
 
## Troubleshooting
1. If no deals are being made and are getting the below error in your logs, this means that you have not successfully added some data allowance to your wallet. Go to https://verify.glif.io/ and request allowance on your wallet address.
```
...
2022-07-18T23:31:31.238Z        ERROR   estuary estuary/replication.go:383      failed to ensure replication of content 1: could not retrieve dataCap from client balance: resolution lookup failed (f1mugdi5c3vel2af5gv6kptb7ew67hujzklsemdea): resolve address f1mugdi5c3vel2af5gv6kptb7ew67hujzklsemdea: actor not found       {"app_version": "v0.1.4"}
...
```
 
2. If you encounter the error below in your logs, this means your are reaching a limit on resource access.
```
2022-07-18T22:48:10.092Z        ERROR   basichost       basic/basic_host.go:327 failed to resolve local interface addresses     {"error": "route ip+net: netlinkrib: too many open files"}
```
 
You can run the below command in your terminal to resolve this. Keep in mind that the command will only affect the current opened session.
```
ulimit -n 10000
```
 
## Links
- https://github.com/application-research/estuary
- https://verify.glif.io/
- https://docs.estuary.tech/
