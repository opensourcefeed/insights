---
layout: post
title: "Keyless signing can make verifying open source releases usable"
categories: [open-source, security, tools]
tags: [keyless-signing, sigstore, gpg, pgp, code-signing, transparency-log, open-source-security, provenance]
description: "Keyless signing replaces PGP/GPG key management with authenticated online identities, reducing friction and improving trust in open source release verification."
image: /insights/assets/images/post-images/keyless-signing-open-source-releases.webp
---

When developers collaborate on software whose source code is made public, they often do so with the help of cryptographic signing. This means keeping a special sign-in ID on a machine and using it whenever it's time to interact with services like GitHub.

This has long been an effective means of protecting the software in question and verifying that contributors are who they claim to be. But it's a system that can be arcane and hostile to new users. Fortunately, there's a modern alternative that simplifies things, without compromising on trust.

![Keyless signing for open source release verification](/insights/assets/images/post-images/keyless-signing-open-source-releases.webp)

## Issues with traditional methods

[A PGP/GPG key combination relies on pairs of keys](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key), one public and the other private. This means that users must find the maintainer's public key, establish that it belongs to them, and manage their own private keys. When private keys linger for longer, they become security liabilities, since they are at greater risk of being compromised.

The practical friction is significant. A user who wants to verify a Linux distribution release must locate the project's public key (often on a keyserver or project website), import it, verify its fingerprint through some out-of-band channel, and only then check the signature on the download. Steps get skipped, keys go unverified, and the security guarantee quietly disappears.

All of the hassle associated with this way of doing things has an influence on the way that developers behave. Many are likely to skip verification entirely, or to install binaries without actually checking signatures.

## What keyless signing means

So, what's the alternative? It's that we dispense with long-lived keys entirely, and instead rely on authenticated online identities — typically an OIDC token from a provider like GitHub, Google, or Microsoft. The signer proves who they are through their existing identity provider, a short-lived signing certificate is issued for that session, and the certificate is discarded afterward. There is no private key file sitting on disk for an attacker to steal, and no public key for a verifier to hunt down.

Tools like [cosign](https://github.com/sigstore/cosign), part of the Sigstore project, implement this workflow directly. A maintainer running a GitHub Actions release pipeline can sign an artifact with a single command, and the signing identity is tied to the GitHub Actions OIDC token — meaning the verifier can confirm not just *who* signed, but *which workflow* produced the artifact.

Of course, this isn't to say that other security measures aren't worthwhile. If you're considering a Linux distro, then downloading it with the help of a [free VPN for Mac](https://protonvpn.com/free-vpn/macos) might help you to conceal your IP and ensure that your activity isn't going to be observed by a malicious party. In certain security-critical settings, this becomes extremely worthwhile.

## Role of transparency logs and provenance

[Platforms like Sigstore allow log-signing events to be stored](https://www.sigstore.dev/) in a way that they can be accessed and audited by stakeholders. We can see who signed something, when they did so, and even whether any suspicious activity was detected at the time.

Sigstore's Rekor component is the transparency log that makes this possible. Every signing event is appended to an immutable, append-only ledger. When a user verifies a release, cosign checks not just the signature but also whether it appears in Rekor — confirming the event was recorded publicly at signing time, not backdated later. This is directly analogous to how Certificate Transparency logs work for TLS certificates.

This is fantastic news for organisations that might need to establish the provenance of any given piece of software. Transparency logs are immutable, which means that attempts to insert malicious code can be traced back to the source. Supply chain attacks that tamper with a release after signing will produce a signature that either doesn't appear in the log or doesn't match the logged entry — both detectable failures.

## Why verification is easier at scale

The major advantage of keyless signing is that it reduces friction. There are fewer technical hurdles to surmount on the way to making a contribution, and security measures are, in practice, more easily made.

From a verifier's perspective, the process collapses to a single command:

```bash
cosign verify <image-or-artifact> \
  --certificate-identity=https://github.com/org/repo/.github/workflows/release.yml@refs/heads/main \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com
```

No key import. No keyserver. No trust-on-first-use. The identity claim is baked into the certificate, and the certificate's validity is anchored in the public transparency log. Verification becomes something that CI pipelines and package managers can enforce automatically, rather than a manual step that users skip.

For open source projects, this shift matters. Signing a release should be as automatic as tagging it; verifying it should be as simple as running a linter. Keyless signing, backed by a transparency log, gets both closer to that goal.