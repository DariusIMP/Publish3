

![logo](logo.png)

Publish3 is an x402-powered decentralized publishing and micro-royalty protocol that lets researchers mint their papers as on-chain assets and receive instant payments for both downloads and citations.

Using Movement's x402 payment rails, every purchase or citation of a paper triggers automated, real-time micropayments to the authors and to the researchers whose work was cited—creating the first incentive-aligned economic system for scientific knowledge.


# Motivation

Publishing in academia is generally hard and little rewarding for the scientists and researchers doing the actual job. On the other side, for readers, students, and other fellow researchers around the globe, it's in many times cost prohibitive to access the research publications and getting access to that knowledge. Publications are generally locked behind a huge paywall setup by publication companies that make a business from research.

Pages like SciHub aim to make more accessible the knowledge to the general people by sharing papers without the consent of those publisher companies that have the commercial rights over them. Although SciHub is widely used nowadays, the site is regularly targeted by lawsuits and taken down. It is an activist site that does not solve the underlying problem, it is more of a palliative for a flawed system. Moreover, it aims to tackle the problem of access to the knowledge, but it doesn't help rewarding the scientists and researchers.

The idea behind Publish3 is to setup a publication system in which access to the research is cheaper, while directly rewarding the authors and authors of cited publications.

# How Publish3 Works

Publish3 is a decentralized academic publishing protocol designed to fairly compensate researchers for both their work and their influence, while keeping access to scientific knowledge simple, transparent, and incentive-aligned. A small fee would be charged for each transaction in order to cover the costs of maintenance and development of the platform.

At its core, Publish3 combines **on-chain payments and ownership** with **off-chain storage and access control**, using the right tool for each job.


## 1. Identity and Access

Publish3 uses **Privy** as its identity and wallet abstraction layer.

Privy enables:
- Email and social login for Web2 users
- Automatic creation and management of a non-custodial Movement wallet
- A unified identity usable both on-chain and off-chain

This allows users to interact with blockchain-powered features without directly handling private keys, while still retaining full ownership of their assets.


## 2. Publishing a Paper

When a researcher publishes a paper:

1. The researcher signs in using Privy
2. The paper (PDF or similar) is uploaded through the web interface
3. The backend:
   - Encrypts the file
   - Stores it in an S3-compatible object store
   - Computes a cryptographic hash of the content
4. The researcher defines:
   - The price of the paper
   - The list of authors
   - A citation royalty percentage
   - Optional references to other papers already published in the system

A smart contract on **Movement** is then called to mint the paper as an on-chain asset.

Only metadata is stored on-chain, including:
- Author addresses
- Pricing and royalty rules
- Citation references
- Content hash

The paper itself is never stored on-chain.

Once minted, the paper becomes discoverable in the Publish3 marketplace.

## 3. Discovering Research

Anyone can browse published papers without payment.

Users can:
- Search by topic, author, or keywords
- View abstracts and previews
- Inspect authorship and citation relationships
- See pricing and royalty rules transparently on-chain

This allows open discovery while keeping full content protected.

## 4. Purchasing a Paper

When a user purchases a paper:

1. The frontend initiates a payment using **x402 payment rails** on Movement
2. The payment is sent directly to the publishing smart contract
3. The smart contract:
   - Verifies the payment
   - Instantly splits the funds according to predefined rules:
     - Primary authors receive the main share
     - Authors of cited papers receive micro-royalties
4. Settlement occurs immediately and transparently on-chain

This ensures that:
- Authors are paid instantly
- Citation rewards are automatic
- No intermediaries take a cut

## 5. Granting Access to the Paper

After a successful purchase:

1. The smart contract emits an on-chain purchase event
2. The backend listens for this event and verifies:
   - The buyer’s wallet address
   - The purchased paper ID
3. The backend generates a **time-limited signed URL** to the encrypted paper stored in S3
4. The signed URL is returned to the authenticated user

Access to the paper is tied to **on-chain ownership**, not to the URL itself.

If a signed URL expires, a new one can be generated at any time after verifying ownership on-chain.

## 6. Architecture Rationale

Publish3 intentionally separates responsibilities:

### On-chain (Movement)
- Ownership and authorship
- Pricing and royalty logic
- Payment settlement via x402
- Citation-based reward distribution

### Off-chain (Backend + S3)
- File storage and encryption
- Search and indexing
- Access control via signed URLs
- Performance and user experience

This hybrid architecture combines blockchain trust guarantees with Web2 scalability and usability.

## 7. Why x402 Is Essential

x402 enables:
- Instant micropayments
- Automated revenue splitting
- Machine-to-machine payments

This makes it possible for:
- Researchers to earn per download
- Citations to become economically meaningful
- AI agents or institutions to programmatically purchase research

Publish3 is not just a paywall — it is a programmable economic layer for knowledge.

## 8. Outcome

With Publish3:
- Researchers are paid immediately for their work
- Citations become a direct economic signal
- Readers know exactly where their money goes
- Scientific knowledge is distributed fairly and transparently

Publish3 replaces opaque publishing monopolies with open, incentive-aligned infrastructure built on Movement.


---

# Architecture

Publish3 is a web application based on React with a backend developed in Rust with Actix-Web, connected to an SQL database and an S3 database as well.

The login is achieved using Privy.

# How to build

## Links:

* https://github.com/DariusIMP/publish3-backend
* https://github.com/DariusIMP/publish3-frontend

## Front end

Requirements:

* node-js
* yarn

Download the `publish3-front` repository and run

```
$ yarn install
```

followed by

```
$ yarn dev
```

Then the site should be up at `http://localhost:3033`.

## Backend

Requirements:

* Rust
* Docker

Follow the instructions from the official Rust page in order to install the language at https://rust-lang.org/tools/install/ .
Same for Docker: https://docs.docker.com/engine/install/

First, set up docker:

```
docker compose up
```

This is going to start the SQL database as well as an instance of MinIO (an S3 storage implementation running locally). Also a Redis instance is launched for caching storage data.

Then build the server with

```
cargo build --release
```

and run it with

```
cargo run --release
```

