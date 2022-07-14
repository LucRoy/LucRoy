# Protocol Labs Estuary

## Goals
The goal of this tutorial is to deploy an estuary node and seal a filecoin deal.

## Requirements
- Git
- Docker

## Repositories
- https://github.com/application-research/estuary
- https://github.com/application-research/estuary-docker

## Installation
1. For this tutorial, we will choose to deploy the estuary node through docker. Let's start by cloning the estuary-docker project repository locally.

```
git clone https://github.com/application-research/estuary-docker.git
```

2. Once we have the project locally, we should be able to follow the installation instructions provided with the project.

https://github.com/application-research/estuary-docker#run-a-local-estuary-shuttle-and-website-with-docker-compose

3. Once completed, you should now be able to log into the UI with your authentication token.

4. Before trying to upload any files. We need to add some data cap to our wallet address. If you look at the output logs from docker, you should be able to find your wallet address.
```
...
estuary-docker-estuary-main-1     | HOSTNAME: http://estuary-main:3004
estuary-docker-estuary-main-1     | FULLNODE_API_INFO: wss://api.chain.love
estuary-docker-estuary-main-1     | /usr/src/estuary/data/estuary.db exists.
estuary-docker-estuary-main-1     | 2022-07-13T23:06:21.574Z    INFO    estuary estuary/main.go:519     estuary version: v0.1.3-7-g617e08d      {"app_version": "v0.1.3-7-g617e08d"}
estuary-docker-estuary-main-1     | 2022-07-13T23:06:21.574Z    DEBUG   estuary estuary/main.go:166     estuary cli flag node-api-url is set to wss://api.chain.love    {"app_version": "v0.1.3-7-g617e08d"}
estuary-docker-estuary-main-1     | 2022-07-13T23:06:21.574Z    DEBUG   estuary estuary/main.go:166     estuary cli flag database is set to sqlite=/usr/src/estuary/data/estuary.db     {"app_version": "v0.1.3-7-g617e08d"}
estuary-docker-estuary-main-1     | 2022-07-13T23:06:21.574Z    DEBUG   estuary estuary/main.go:166     estuary cli flag apilisten is set to 0.0.0.0:3004       {"app_version": "v0.1.3-7-g617e08d"}
estuary-docker-estuary-main-1     | 2022-07-13T23:06:21.574Z    DEBUG   estuary estuary/main.go:166     estuary cli flag datadir is set to /usr/src/estuary/data/       {"app_version": "v0.1.3-7-g617e08d"}
estuary-docker-estuary-main-1     | 2022-07-13T23:06:21.574Z    DEBUG   estuary estuary/main.go:166     estuary cli flag hostname is set to http://estuary-main:3004    {"app_version": "v0.1.3-7-g617e08d"}
estuary-docker-estuary-main-1     | Wallet address is:  f1tjj3xppmpmvuktifjhza3nelrk22cl4d2p5apxy
...
```

5. Go to https://verify.glif.io/ and log in with your github account. Once logged in, request a verified data allowance to your address. An allowance of 32GiB should now be added to your wallet.

## Uploading File
1. From the UI, go to Home -> Upload. Click on 'ADD A FILE' and select a file to be uploaded. For this tutorial, we will choose a file that is greater than the size of the staging threshold (3.57 GiB). This way we can skip over the batching process and go directly looking for deals.

2. Once the file is uploaded, you can view the status of your deals under Developers -> Deals. This might take a while to process.

3. Congratulations!!! You have now successfully seal a filecoin deal.

## Troubleshooting
1. If no deals are being made and are getting the below error in your logs, this means that you have not successfully added some data allowance to your wallet. Go to https://verify.glif.io/ and request allowance on your wallet address. 
```
...
estuary-docker-estuary-main-1     | 2022-07-13T23:37:16.931Z    ERROR   estuary estuary/replication.go:383      failed to ensure replication of content 1: could not retrieve dataCap from client balance: resolution lookup failed (f1tjj3xppmpmvuktifjhza3nelrk22cl4d2p5apxy): resolve address f1tjj3xppmpmvuktifjhza3nelrk22cl4d2p5apxy: actor not found {"app_version": "v0.1.3-7-g617e08d"}
...
```

## Links
- https://github.com/application-research/estuary
- https://github.com/application-research/estuary-docker
- https://verify.glif.io/
- https://docs.estuary.tech/
