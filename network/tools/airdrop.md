---
description: >-
  Use cheqd's Open Source tooling for running an airdrop, including a frontend
  UI and distribution repository.
---

# Cosmos Airdrop Helpers

The airdrop tools, used for our [community airdrop rewards](https://github.com/cheqd/airdrop-ui) site, are split into two repos; one for managing the actual distribution of airdrop rewards to wallets, and another for the frontend itself to handle claims.

## Get started

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Airdrop UI (frontend)</strong></mark></td><td>Check out the repository for our frontend Airdrop UI here.</td><td><a href="https://github.com/cheqd/airdrop-ui">https://github.com/cheqd/airdrop-ui</a></td></tr><tr><td><mark style="color:blue;"><strong>Airdrop Distribution (backend)</strong></mark></td><td>Check out the repository for our backend Airdrop Distribution helper here. </td><td><a href="https://github.com/cheqd/airdrop-distribution">https://github.com/cheqd/airdrop-distribution</a></td></tr></tbody></table>

## Architecture

In terms of the frontend, we learnt that airdrop reward sites need to be more resilient to traffic spikes than most websites because, when announced, community members will tend to flock to the site to claim their rewards generating a large spike in traffic, followed by a period of much lower traffic.

This type of traffic pattern can make prepping the server to host airdrop claim websites particularly difficult. For example, many projects will choose to purchase a large server capacity to prevent server lag, whilst others may simply become overwhelmed with the traffic.

To manage this, the frontend site was developed to work with [Cloudflare Workers](https://developers.cloudflare.com/workers/), a serverless and highly-scalable platform so that the airdrop reward site could handle these spikes in demand.

On the backend we also needed to build something that could manage a surge in demand whilst providing a highly scalable and fast way of completing mass distributions. Initially our implementation struggled with the number of claims resulting in an excessive wait to receive rewards in the claimant’s wallet. To improve this we used 2 separate CosmJS-based [Cloudflare Workers](https://developers.cloudflare.com/workers/) scripts; one which lined up claims in 3 separate queues (or more if we wanted to scale further), and a second distributor script that is instantiated dependent on the number of queues (i.e. 3 queues would require 3 distribution workers).

There is no hiding that we ran into some hiccups, in part due to our Cloudflare Worker approach, during our [Cosmos Community Mission 2 Airdrop](https://www.cheqd.io/blog/cosmos-community-mission-2). We have documented all of the issues we ran into during our airdrop and the lessons learnt in our [airdrop takeaway blog post](https://blog.cheqd.io/what-we-learned-from-the-cheq-community-2f00952095c2).

What is important to explain is that:

1. The reward site using [Cloudflare Workers](https://developers.cloudflare.com/workers/) scaled very well in practice, with no hiccups;
2. We had problems with the way we collated data, but the fundamental Cloudflare Workers infrastructure we ended up with, after having to refactor for our initial mistakes, is battle tested, highly efficient and resilient.

### **Why is this valuable?**

Any project using the CosmosSDK and looking to carry out an airdrop or community rewards program can now use our Open Sourced frontend UI and Distribution repository to ensure a smooth and efficient process for the community, without any hiccups in the server capacity or distribution mechanics.

We would much rather other projects do not make the same mistakes as we did when we initially started our airdrop process. What we have come away with, in terms of infrastructure and lessons learned, should be an example of the do’s and the not-to-do’s when carrying out a Cosmos based airdrop.
