# Raiden Services

## What is this repository

This repository contains the documentation and configuration necessary to run the Raiden Services. The services consist of the Raiden Monitoring service (MS) and the Raiden Pathfinding service (PFS).

**Current release:** 2019.7.1

## Table of Contents

- [Overview](#overview)
- [Requirements](#requirements)
  - [Hardware](#hardware)
  - [Software](#software)
- [Installation](#installation)
- [Upgrades](#upgrades)
- [Known issues](#known-issues)
- [Changelog](#changelog)

## Overview

The Raiden Network uses pathfinding and monitoring services to increase usability.
To ensure reliability, availability and neutrality it is desirable that those servers are
being operated by multiple independent entities.

Therefore we provide this repository which allows easy setup of these services.
It uses docker and docker-compose for easy installation and upgrades.

### Structure


```
+-------------------+
|                   |
|   Raiden clients  |
|                   |
+---------+---------+
          |
==========|==========
          |
+---------v---------+
|                   |
|      Traefik      |
|                   |
+---------+---------+
          |
          +------------------------+
          |                        |
+---------v---------+    +---------v---------+
|                   |    |                   |
|     Pathfinding   |    |    Monitoring     |
|                   |    |                   |
+---------+---------+    +---------+---------+
```


We use Traefik as a reverse proxy and also utilize its capability of automatically provisioning `Let's Encrypt TLS` certificates.

### Network

After a successful deployment the following ports will be in use:

- 80 - HTTP
  - Redirects to HTTPS
  - Let's Encrypt HTTP challenge for certificate provisioning
- 443 - HTTPS
  - PFS REST API


## Requirements

### Hardware

Minimum recommended for a production setup:

- 2 GiB RAM
- 2 Cores
- 10 GB SSD

### Software

- Docker >= 17.12
- docker-compose >= 1.21.0

### Other

- A domain (or subdomain) for exclusive use by the services.
- A synced Ethereum node, that is reachable from the server where the services are installed,
is required. Setting one up is outside of the scope of this document, please refer to [eth node setup guide](https://ethereum.gitbooks.io/frontier-guide/getting_a_client.html).
- A fresh Ethereum account that will be used only by the services. The accounts needs a small
amount of funding (0.1 ETH) should be enough.

## Installation

### Preparation

1. Provision a server that meets the [hardware](#hardware) and [software](#software) requirements listed above.
2. Ensure a domain (or subdomain) is available

    Examples:
    - raidenservices.mydomain.tld
    - raidenservices-mydomain.tld
    - mydomain-raidenservices.tld

3. Configure `A` (and optionally `AAAA`) DNS records for the domain pointing to the servers IP address(es)
4. Configure a `CNAME` DNS record for `*.<domain>` pointing back to `<domain>`


### Installing

1. Clone the [current release version of this repository](https://github.com/raiden-network/raiden-services)
   to a suitable location on the server:

   ```shell
   git clone https://github.com/raiden-network/raiden-services.git
   ```
2. Go to `./package/` to conduct the next steps - whereas `.` refers to the location you cloned into in step 1
3. Copy the keystore file to be used by the services into the directory `./package/data/keystore/`
4. Copy `.env.template` to `.env` and modify the values to fit your setup (see inline comments for details)
    - Set `DATA_DIR`, if you used a different data directory (the keystore file is expected in `<DATA_DIR>/keystore/`)
    - Set `KEYSTORE_FILE` to the filename of the keystore file
    - Set `PASSWORD` to the password of the keystore file
    - Set `ETH_RPC` to the rpc endpoint of your Ethereum node (example: `http://111.222.33.444:8545`)
5. Run `docker-compose build` to build the containers
6. Run `docker-compose up -d` to start all services
    - The services are configured to automatically restart in case of a crash or reboot.
7. The registration of the service with the service registry is done automatically. This may take a while.
    - Please check that the registration was successful with `docker-compose logs -f registration`.
8. The states of the services are stored in `./data/state/` (or `<DATA_DIR>/state/`). This directory should be backed up regularly, otherwise funds could be lost.

We recommend that you provide your own monitoring for the services. The setup of which is currently out of scope of this document.

In case you want to change your configuration afterwards, just change `.env` and run `docker-compose down` followed by `docker-compose up -d`.
## Upgrades

To upgrade to a new release please check the [changelog](#changelog) for any necessary
configuration changes and then run the following commands:

```shell
git fetch origin --tags
git reset --hard master
docker-compose build
docker-compose down
docker-compose up -d
```


## Known issues

None right now.


## Contact / Troubleshooting

To report issues or request help with the setup please [open an issue](https://github.com/raiden-network/raiden-services/issues/new)
or contact us via email at contact@raiden.nework.


## Changelog

- 2019-07-31 - `2019.7.1`
  - Add options to set *operator* and and the *info message*, please update your `.env` accordingly
  - Update the registration script to the new `ServiceRegistry`.
- 2019-07-10 - `2019.7.0` - **Initial version**


## Licenses

The code and documentation in this repository are released under the [MIT license](LICENSE).

This repository contains instructions to install third party software. Those are licensed as follows:

- [Traefik](https://github.com/containous/traefik): [MIT](https://github.com/containous/traefik/blob/6a55772cda1684546a6a5456b6847e0f9b3df44d/LICENSE.md)
