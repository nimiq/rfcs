# Nimiq.ID

- Start Date: 2020-01-14
- Affected Repos: Hub
- References: [NimiqID on the Nimiq Forum](https://forum.nimiq.community/t/nimiqid-authenticate-with-your-nimiq-account/96/6)
- Implementation PR: (leave this empty)

## Summary

This RFC describes a proposal to use Nimiq’s public-key cryptography for basic identity proofs and authentication of Nimiq Account holders with online services.

## Basic Example

```js
// 1. Initialize Hub API
// See https://nimiq.github.io/hub/quick-start

// 2. Trigger login flow with Nimiq Hub
// The method name (nimiqID) is not fixed and is used only as an example
const result = await hubApi.nimiqID({
    appName: 'MyApp',
    challenge: '<server-generated-unique-string>',
});
// The result contains a public key and signature

// 3. Post result data to app server

// 4. Verify signature against challenge on app server
// See https://nimiq.github.io/hub/api-reference/sign-message#verification

```

## Background

A secret seed (a form of a private key) used in the Nimiq cryptocurrency ecosystem is meant to only be known to one person and gives control over all funds that are stored in addresses belonging to its private keys. The blockchain ensures that those funds can only be moved by the rightful owner - the owner of the seed - by signing transactions from those addresses with their respective private keys. Ownership of the keys and thus the seed authenticates the owner.

Similar to transactions, any list of bytes can be cryptographically signed by a private key, proving ownership over that key. This so called “message signing” involves an additional cryptographic hash and a prefix, but it essentially uses the same method as the tamper-proof sending of transactions on the blockchain.

Message signing can therefore be used to authenticate the owner of a private key against online services, which is what we call Nimiq.ID. This authentication system would not store user data on Nimiq servers - instead all identity proof generation happens only in the users’ browser during the authentication-attempt.

## Motivation

With most people’s life being increasingly online and using a wide variety of online services, maintaining access to these services often still uses basic username-and-password methods, which are prone to hacks, credential-stealing attacks and phishing. Two-factor-authentication has helped a lot in reducing account-takeovers, and so have password-managers in increasing password security. Additionally, big social networks and software companies such as Facebook, Google, GitHub, LinkedIn, Apple, etc. are offering single-sign-on solutions where people can log into various online services by connecting them with their social account. However, as has become clear in recent years, these social networks and software companies are not so much focused on providing ease-of-use and convenience to their users, but rather on analysing and “data mining” their users’ behaviors to increase their (ad-)revenue.

**Nimiq is in the unique position to offer a combination of**

1. **Ease-of-use**: Nimiq is browser-first and does not require special software downloads.
2. **Privacy**: Setting up a Nimiq Account does not use any personal data, so no personal data can be shared with any third-party.
3. **Safety**: The private keys are protected by the Nimiq Keyguard and never leave the browser. The Keyguard is architected to only output data that is safe to share publicly.
4. **Decentralization**: Authentication happens in the browser, directly on-device. No Nimiq server is required for using Nimiq.ID.
5. **Convenience**: Logins with Nimiq.ID are essentially a unique address on the Nimiq blockchain and could be used to make payments to and from services, such as exchanges, games, service providers and more.

Last but not least, providing the Nimiq.ID service would increase visibility and awareness for the Nimiq project as a whole.

## Detailed Design

### Reduced Scope

We propose here a minimal implementation consisting of a client-side-only identity proof system. To extend this system to become compatible with existing identity standards such as OpenID Connect, WebAuthn, etc., it likely requires some form of centralized user-data storage and server-to-server communication which may not be possible with just the users’ browser, and is thus not considered here.

### Assumed Knowledge

This chapter assumes the reader is familiar with:

- Public key cryptography and how it enables signing data and subsequent verifying of the signature,
- Hierarchical deterministic (HD) key derivation (BIP-44) and how it enables deriving virtually infinite public keys from a single cryptographic seed.

### Authentication Process

This is a chronological overview over the authentication process:

1. To initiate an authentication process, the website or online service (hereafter called “app”) generates a unique and one-time challenge data for the user wanting to authenticate. This can be a random string, or a more meaningful set of data points.
2. The app stores this challenge data with their user-record for later use.
3. The app provides the challenge data to the user in a call to the Nimiq Hub.
4. The user is presented a special Nimiq.ID authentication interface (yet to be designed), containing the URL origin from which the request originated, optionally a custom app icon and a prompt to enter their Nimiq Account password. (The user also needs to select which Account to use for authentication in case multiple Accounts are stored in the Hub.)
5. After correctly entering their Account password and thereby decrypting their Account seed, the Keyguard derives a key pair from the seed. [Key derivation options are described below](#key-derivation).
6. The Keyguard signs the app challenge data with the derived key pair and returns the signature and the public key to the app. The Hub also stores that the user authenticated with the app and which public key was used.
7. The app verifies the signature against the public key and the stored challenge data from step 2 ([Javascript example](https://nimiq.github.io/hub/api-reference/sign-message#verification)). If the signature is valid, the user is authenticated with the public key as the user identifier and gets logged in.

### Key Derivation

Technically, it is possible to sign app challenge data with any key pair derived from an Account seed. Which key pair to use (and thus which public key and Nimiq Address to provide to the app) is therefore a question of the user experience.

We present four possibilities:

1. We could use the first address of an Account stored in the Hub. The app could then also directly use this address to e.g. pay out NIM to the user without having to ask the user again for a payout address. But this method would also reveal a user’s potential main address to all apps they authenticate with, which the user may want to keep somewhat private. Using a user’s main address would also allow the apps to track users activity on the Nimiq blockchain.
2. We could use a new “Address type” in Nimiq’s BIP-44 derivation path. Currently that path is “m/44’/242’/0’/x’”, with the “0’” index denoting the Address type for regular payment addresses. Using a fixed path for authentication (e.g. “m/44’/242’/1’/0’” with a “1’” Address type) is possible and would allow the Hub and Safe to show this address for all user accounts automatically and track balance changes on it.
However, it also means that all apps the user authenticates with with their Account receive the same user identifier (the public key at that derivation path), thus exposing users to be tracked across apps. (Creating new addresses by incrementing the address index of the authentication derivation path for each app is not feasible, as it would lead to non-deterministic user identifiers for the same app on different devices and after Hub logouts or browser data deletion.)
3. Another option is to use a new Address type like in option 2, but to calculate a deterministic derivation path based on the app’s URL origin (the only piece of data that the Hub in a popup or redirect non-falsifiably knows about the app). The method of calculating the path is not specified in this RFC, but could for example be that the byte value of each character of the URL host constitutes an index in the path. The derivation path for authenticating at “nimiq.com” would thus be “m/44’/242’/1’/110’/105’/109’/105’/113’/46’/99’/111’/109’”, with the first part (“m/44’/242’/1’”) denoting the Address type and the second part (“110’/105’/109’/105’/113’/46’/99’/111’/109’”) corresponding to the character byte sequence of n i m i q . c o m. (Derivation path indices can be numbers up to 2’147’483’647, so combining character byte values for shorter derivation paths is also possible.) Deriving key pairs is a comparably cheap computation, so longer domain names wouldn’t have a noticeable impact on user experience.
Using this method ensures that each app receives a unique user identifier even when authenticating with the same Account, preventing users from being tracked across apps. On the other hand, it also prevents preemptively deriving and storing authentication addresses in the Hub upon Account login, since apps the user authenticated with are not persisted outside the users’ browser. (A potential future “Nimiq Sync Service” would solve this problem.) Authentication addresses should thus not be used for currency transfers from/to the app.
4. The user could select any of their stored addresses to use for authentication and can also derive new ones to use. Keeping track of which address was used with a certain service, so that later logins use the same address, would be the responsibility of the user, until a syncinc service is available.

For this RFC to become accepted, one derivation method has to be decided on and the others removed (or moved to [Alternatives](#alternatives) below).

### API Method Name

This section will describe the method name(s) decided for the Nimiq.ID API.

## Drawbacks

- Drawbacks for app developers:
    - No personal data for the app to collect (drawback for the service, but an advantage for the user).
    - Nimiq has much smaller user base than established SSO providers like Facebook, Google, Github, etc., so integrating Nimiq.ID may not pay off (this is a chicken-and-egg problem).
- Spending time and marketing money on Nimiq.ID might not be worth it, considering:
    - [OpenId 2.0 is deprecated and usage decreasing](https://trends.builtwith.com/docinfo/OpenID)
    - [OpenId 2.0 is considered a failure](https://www.wired.com/2011/01/openid-the-webs-most-successful-failure/). Even big supporter StackOverflow abandoned OpenId 2.0 support.
    - Comparable implementations are essentially unused:
        - [DigiID](https://github.com/DigiByte-Core?q=digiid) libraries have only 5 Github stars as of Jan. 2020 and development seems to have stopped.
        - [Bitcoin Authentication Open Protocol](https://github.com/bitid/bitid) ([JS implementation](https://github.com/porkchop/bitid-js)) has ~300 stars.
    - [Many blockchain identity projects already exist](https://github.com/peacekeeper/blockchain-identity)
    - Problem may already be solved by password managers
    - Mozilla Persona was another endeavor for an authentication system for the web, decentralized, that also failed.

## Adoption Strategy

To achieve widespread adoption, server-side implementations to authenticate Nimiq.ID users will have to be developed to let app developers or admins easily integrate Nimiq.ID into systems such as Wordpress, Laravel, ExpressJS, Discourse, and many more.

## Unresolved Questions

The following topics require further research to determine if it is possible, feasible and desirable to integrate with them, either directly or in a future extension of the Nimiq.ID system:

- Decentralized Identifiers (DIDs)
    - [Decentralized Identifiers (DIDs) v1.0](https://w3c.github.io/did-core/) (W3C Standard Draft)
    - [Decentralized identity Foundation: DIF](https://identity.foundation/) (website)
    - [Decentralized Identity Whitepaper by Microsoft](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2DjfY) (Microsoft PDF)
    - [Decentralized identity – Own your digital identity](https://www.microsoft.com/en-us/security/technology/own-your-identity) (Microsoft)
    - [Decentralized Identity Developer Docs](https://didproject.azurewebsites.net/) (Microsoft)
    - [uPort donates code to the Decentralized Identity Foundation](https://medium.com/decentralized-identity/uport-donates-code-to-the-decentralized-identity-foundation-349d4740acbd) (Medium article)
    - [uPort - Tools for Decentralized Identity and Trusted Data](https://www.uport.me/) (website)
    - [Bitcoin DID Method](https://w3c-ccg.github.io/didm-btcr/) (W3C Report)
    - [Create and verify DID verifiable JWT's in Javascript](https://github.com/decentralized-identity/did-jwt) (Github)
- Attempt to follow an established standard?
    - Integrate with WebAuthn? (https://webauthn.guide)
    - OpenID Connect is based on OAuth and on first sight is not compatible with Nimiq’s browser-first approach as a server might be required (but can maybe be simulated by a Hub serviceworker?). https://openid.net/developers/specs/
    - OpenID Connect FAQ: https://openid.net/connect/faq/
    - Also interesting: https://en.wikipedia.org/wiki/IndieAuth
