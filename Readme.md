This repo's purpose is to build the latest images for the Ergo-Chain-Grabber and Ergo-UTX-Tracker components. It is designed to be compatible with Docker-Compose and FluxOS.

The two Dockerfiles in the root directory build the Chain-Grabber and the UTX-Tracker from Ergo Platform's [Explorer-Backend](https://github.com/ergoplatform/explorer-backend).

Install [Docker](https://docs.docker.com/engine/install/) and follow the below instructions to build the image and push it to Docker-Hub.

# Ergo Blockchain Explorer Version
```
# Pull the latest stable version of Explorer-Backend if required
# Latest version can be found at https://github.com/ergoplatform/explorer-backend/releases
git pull
```

```
# Log in to Docker-Hub with your username and password
docker login -u your-username
```

# Build Ergo-Chain-Grabber
```
# Build the image and tag it as "flux-ergo-chain-grabber"
docker build -f chain-grabber.Dockerfile . -t flux-ergo-chain-grabber

# Tag the image with the Docker-Hub repo tag
docker tag flux-ergo-chain-grabber runonflux/flux-ergo-chain-grabber:latest

# Push the image to Docker-Hub (each layer may take a few mins to push)
docker push runonflux/flux-ergo-chain-grabber:latest

# Use the image in FluxOS or in your Docker-Compose file
runonflux/flux-ergo-chain-grabber:latest
```

# Build Ergo-UTX-Tracker
```
# Build the image and tag it as "flux-ergo-utx-tracker"
docker build -f utx-tracker.Dockerfile . -t flux-ergo-utx-tracker

# Tag the image with the Docker-Hub repo tag
docker tag flux-ergo-utx-tracker runonflux/flux-ergo-utx-tracker:latest

# Push the image to Docker-Hub (each layer may take a few mins to push)
docker push runonflux/flux-ergo-utx-tracker:latest

# Use the image in FluxOS or in your Docker-Compose file
runonflux/flux-ergo-utx-tracker:latest
```

# Ergo Blockchain Explorer

This repository contains 4 services:

### 1. Chain Grabber

`modules/chain-grabber`

Scans blockchain, dumps aggregated data to database

Dependencies:
- Postgres (Schema `modules/explorer-core/src/main/resources/db/V9__Schema.sql`)

### 2. Explorer API

`modules/explorer-api`

Provides a set of HTTP API methods for querying chain/off-chain data.

Specification: https://api.ergoplatform.com/docs/openapi

Dependencies:
- Postgres (Schema `modules/explorer-core/src/main/resources/db/V9__Schema.sql`)
- Redis

### 3. UTX Tracker

`modules/utx-tracker`

Dumps unconfirmed transactions from mempool to local database

Dependencies:
- Postgres (Schema `modules/explorer-core/src/main/resources/db/V9__Schema.sql`)

### 4. UTX Broadcaster

`modules/utx-broadcaster`

Broadcasts unconfirmed transactions to a set of known peers

Dependencies:
- Redis

## Assembly

Service JAR can be build with `sbt <service-name>/assembly`, assembly will appear in `modules/<service-name>/target` directory.

## Configuration

Config samples can be found in `modules/<service-name>/src/main/resources/application.conf`

### TestNet Configuration

To establish TestNet Connection modify .conf parameters in modules to:

```application.conf
 network.master-nodes = ["http://195.201.82.115:9052"]
 
 protocol.network-prefix = 16
 protocol.genesis-address = "AfYgQf5PappexKq8Vpig4vwEuZLjrq7gV97BWBVcKymTYqRzCoJLE9cDBpGHvtAAkAgQf8Yyv7NQUjSphKSjYxk3dB3W8VXzHzz5MuCcNbqqKHnMDZAa6dbHH1uyMScq5rXPLFD5P8MWkD5FGE6RbHKrKjANcr6QZHcBpppdjh9r5nra4c7dsCgULFZfWYTaYqHpx646BUHhhp8jDCHzzF33G8XfgKYo93ABqmdqagbYRzrqCgPHv5kxRmFt7Y99z26VQTgXoEmXJ2aRu6LoB59rKN47JxWGos27D79kKzJRiyYNEVzXU8MYCxtAwV"

```

## Run

SBT project:
`sbt <service-name>/run`

Assembly JAR:
`java -jar <<service-name>-assembly-*.jar>`

Tests:
`sbt test`

## Ergo bootstrap

This project is included in [ergo bootstrap](https://github.com/ergoplatform/ergo-bootstrap), which will help you to quickly deploy an Ergo blockchain cluster with a handful of useful tools you might need to start developing your dApps.
