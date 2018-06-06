# Songshop
**Draft 0.0.1 - by Jonathan S Layton**

**PGP:** 5BFF 3935 B019 A7A2 725A 054B 2AA6 11CC 09B3 EDA6

**BTC only, for now.**

#### This should be a simple, 1-click-deployable self-hostable web-service, for Musicians to sell their work.

#### The paradigm ahead is 'lossless, original sound'. This is bit-by-bit. There is no room for fuzzy matching here.

## Incorporates -

### FileBazaar
- https://github.com/ElementsProject/filebazaar

### Lightning Network
- https://github.com/ElementsProject/lightning-charge
- https://github.com/ElementsProject/lightning

### Bitcoin
- https://github.com/bitcoin/bitcoin

### Direct commitments to the BTC chain
- https://github.com/bitpay/i-made-this

### Direct commitments to the BTC chain (w/Memo Protocol)
- https://memo.cash/protocol

### Stampery & OpenTimestamps proofs via the BTC chain
- https://api.stampery.com
- https://github.com/opentimestamps by Peter Todd

### JSON Web Token
- https://github.com/auth0/express-jwt

### Master Music DB - discogs.com
- https://discogs.com

---

**The artist** should be / must be the domain webmaster, and signal this publicly. This allows for a thin-waisted trust model, where an https-enabled domain and a Bitcoin address are the only boundaries.

---

## Artist Upload -

**We begin by uploading a FLAC file -**

The **songhash** is the PCM audio binary, without its MD5 checksum appended, sha256-hashed (`SHA256(pcm)`).

A **tagged** song file version is also created; this version will be distributed. It is a native FLAC with `ARTIST` / `COMPOSER` and `TITLE` set in the Vorbis Tags, based on the musician's input. Note that the exact same PCM audio data exists within this file.


A file is a **unique song** if -

1. If the file is a valid FLAC (`flac -t`), and
2. If there are no `OP_RETURN`s on the BTC chain with this songhash, and
3. If there are no [Stampery](https://stampery.com) or [OpenTimestamps](https://timestamps.org) public attestation servers with proofs of this songhash, and
4. If there are no matching `(Song Name, Artist Name)` entries in the **Master Music DB** ([discogs.com](https://discogs.com)), based on the file's Vorbis Tags.

Then we'll deem it 'unique enough' to create an original song commitment to the BTC blockchain -
1. Validate + upload song file to artist's webserver / domain, then
2. Commit the songhash to BTC chain directly (via a tx from your own address) (optionally as a [memo protocol post](https://memo.protocol/protocol)), and await nth confirmation, **or**
- Commit the songhash via Stampery and/or OpenTimestamps (free!), save + upload proof to artist's webserver/domain, and await confirmations + attestations, then
- Once complete, song is 'ready for distribution'; you may begin sharing it at any price.

## Buyer Download -

**Users can now 'buy original songs', with a fast BTC micropayment, straight from the musician's known domain.**

Possible channels for Song Delivery -
- HTTPS URL over [ElementsProject/FileBazaar](https://github.com/ElementsProject/FileBazaar)
- HTTPS URL over [ElementsProject/paypercall](https://github.com/ElementsProject/paypercall#paying-for-api-calls)
- HTTPS URL over [ElementsProject/ifpaytt](https://github.com/ElementsProject/ifpaytt)
- HTTPS URL over Email
- Other over Email

URLs are useful because they are portable and can allow downloads to easily be retried in case of failure. However, they can be dangerous to a digital goods seller, since they are easily shared.

**By default**, the generated URLs for purchased songs are **'Ephemeral'**, meaning they expire in 30 minutes. They can alternatively be set to 'Persistent', if the artist so chooses.

### Notes -

- This platform does Proof-of-Existence and Timestamping for Song Files, using sha256 and [Bitcoin](https://github.com/bitcoin/bitcoin).

- This platform is **ONLY for NEW, UNRELEASED creations**. Make sure you, the musician, were the one to master the final version of the song file (so you are the **only** person who could upload the original)!

- This protocol / platform, indeed, adheres to a first-come first-serve model. An artist proves ownership of their new creation by committing the sha256 of its PCM Audio in an transaction using `OP_RETURN`, to the Bitcoin blockchain, first.

- If a prior work is found elsewhere, whether as an entry on the blockchain using any known scheme, or on [discogs.com](https://discogs.com) by exact match of `(Song Name, Artist Name)`, you will be warned and alerted of this. You could always try to lie and attest to song hashes of pre-2018 songs that you didn't create, but the platform resists this by 1) politely failing when attempting to double-commit and 2) by querying the Master Music DB first when assessing uniqueness.

- Search implementations **must** return the **earliest** songhash commitment, with precedence order 1) `OP_RETURN songhash` 2) `OP_RETURN 6d02 songhash` ([memo protocol](https://memo.cash/protocol)) 3) Stampery & OpenTimestamps. Any later (second) appearances of the songhash are deemed invalid.

- The songhash serves as a unique permanent identifier, which is easy to calculate from song itself. One can verify whether they have 1) any original file 2) **the** original file, if they know its artist's domain or BTC address.

- FLAC only (should convert from ALAC or MP3 if necessary), so never any need for more than one file to commit, per song.

- File downloads are hosted directly on the artist's server instance (as the default for FileBazaar). This could be externalized if necessary, but should probably remain within the same domain.

- Even if the artist doesn't control the domain in the future, the creator's identity can still be verified via the BTC blockchain or timestamp proofs.

- Both the songhash (sha256 of file audio; could also provide a checksum of the 'tagged' version) and any Stampery/OTS proofs should be viewable by shoppers on the artist's domain.

- Search times over the entire blockchain (at upload/ownership-verification time) are a significant question here; the latest Bitcore code and caching makes this feasible.

- With Stampery & OpenTimestamps, the artist's ownership of the original proofs are evidence of the earliest-ever-uploaded version of a song, to be disputed only against another attestion of existence of an earlier instance of the songhash, from the chain or a public attestation server. An artist gets strongest assurances by interacting with the blockchain directly; the proposed model depends on a mirrored network of attestation (OpenTimestamps) servers, but doesn't have to.

- The **store domain** must also be trusted by the buyer, as always, not just the artist's ownership of their private key - otherwise the delivery step may be a lie!

- One artist (by name) per song, for now.

**Notes from OpenTimestamps -**
- You can create timestamps that don’t depend on calendars by using the `--wait` option. This will make the client wait until the timestamp is completely confirmed by the Bitcoin blockchain. The resulting timestamp will contain all the data needed to prove the timestamp with Bitcoin, allowing verification to be done completely locally.
- OpenTimestamps scales indefinitely, allowing timestamps to be created for free by combining an unlimited number of timestamps into one Bitcoin transaction.

### Future Niceties -
- Artist Admin UI (currently FileBazaar uses YAML + files)
- Buy/Sell multiple songs at once
