# Validator Status API

## Summary

We built a network-wide validator status monitoring tool that will send out alerts in case your node starts losing blocks. Join/follow the [#mainnet-alerts](https://cheqd-community.slack.com/archives/C03R186FKB4) channel to get these updates.

### How it works/how we built it

1. Our [block explorer (BigDipper)](https://explorer.cheqd.io/) exposes a GraphQL endpoint. This GraphQL endpoint is what the block explorer front-end uses when showing [validator conditions on the overall/per-validator page](https://explorer.cheqd.io/validators).
2. To simplify parsing the output from the GraphQL endpoint, we did [a simple wrapper using Cloudflare Workers to parse the GraphQL output](https://github.com/cheqd/validator-status) into simple JSON.

### Alerting

To simplify the task of alerting via various channels (and to keep it extensible to other channels), we take the output of our validator status API and parse it via [Zapier](https://zapier.com/). This is done as a two-stage process via two separate “Zaps”.

1. Collating a list of validators missing blocks
2. [Schedule by Zapier](https://zapier.com/apps/schedule/integrations) to wake up the “Zap” every hour
3. Run custom JavaScript code using [Code by Zapier](https://zapier.com/apps/code/integrations) to parse the JSON output
4. [Filter by Zapier](https://zapier.com/apps/filter/integrations) to check if there are entries generated in the compromised validator list. If not, then terminate Zap execution.
5. If the execution has reached this stage, [Looping by Zapier](https://zapier.com/apps/looping/integrations) with the following sub-steps:
6. [Formatter by Zapier](https://zapier.com/apps/formatter/integrations) to carry out text/number formatting.
7. [Digest by Zapier](https://zapier.com/apps/digest/integrations) to push the formatted bullet-list item with validator details into a compiled digest.
8. Sending an alert to designated alert channel: A separate Zap triggers and sends alerts to designated channels. Right now, our setup sends these details to the [cheqd Community Slack](http://cheqd.link/join-cheqd-slack) and the [cheqd Community Discord](http://cheqd.link/discord-github).
9. Similar to above, Schedule by Zapier to wake the Zap up every hour
10. “Release” any unreleased digests by using [the manual release feature in Digest by Zapier](https://zapier.com/help/create/storage-and-digests/compile-data-in-a-digest-in-zaps#release-the-content-of-the-digest).
11. Filter by Zapier to check if there are any entries populated in the digest. If not, terminate execution of any further steps at this stage.
12. If execution has proceeded to this step, use the [Zapier App for Slack](https://zapier.com/apps/slack/integrations) and [Zapier App for Discord](https://zapier.com/apps/discord/integrations) to send a message (with formatting) to designated alert channels.

You can copy this Zap to configure a similar setup for other alert channels, such as [SMS by Zapier](https://zapier.com/apps/sms/integrations) or [Email by Zapier](https://zapier.com/apps/email/integrations).
