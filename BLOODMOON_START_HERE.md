# BloodMoon Voice

**Independent BloodMoon development baseline for the OpenSimulator WebRTC voice stack.**

[Download the original verified release](https://github.com/Vlad-BloodMoon/wolfvoice/releases/tag/v0.2.0-bloodmoon.4) · [BloodMoon Voice releases](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases) · [Source snapshot](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/commit/bdf9d9d325f5c5ab7cbec82a99bcc2f8a0040619)

## What is included

This repository was imported from the **exact source tree** of `Vlad-BloodMoon/wolfvoice` at tag `v0.2.0-bloodmoon.4` (source commit `a16b600be45e34acd61d874183ce2a8c3e1fe4c7`).

Source tree SHA-1: `6da740f1b1d08f4a108f8d9d0fd1a4a95e7645f5`.

The baseline includes the Rust voice server source, client code, example configs, installer scripts, CI workflows, documentation, tests, `LICENSE` and `NOTICE`.

The original release has **9 downloadable assets**, including a Linux x86-64 server binary, the OpenSimulator 0.9.3.1 module ZIP, and checksum files. Their republication is managed by the import workflow at `.github/workflows/import-bloodmoon-release.yml`. **If the new release has not appeared yet, download the original files from the source release page.** Do not assume the ZIP modules are present in the source tree.

## Features and prerequisites

The BloodMoon-modified voice stack supports spatial WebRTC voice, voice calls through private IM, and group/conference voice integrations when paired with compatible OpenSimulator modules. Complete ad-hoc multi-user IM conference functionality depends on the **separate BloodMoon PHP/MySQL conference API**, which is **not contained in this repository or the original source tree**. Do not treat this repository alone as a one-click complete conference deployment.

See [installation (English)](docs/INSTALLATION.md), [installation (French)](docs/INSTALLATION_FR.md), [OpenSimulator integration](docs/OPENSIM.md), and [the release notes](docs/RELEASE-v0.2.0-bloodmoon.4.md).

## Make your own fork

1. Click **Fork** on this repository.
2. Clone your fork:

```bash
git clone https://github.com/YOUR-USERNAME/BloodMoon-Voice.git
cd BloodMoon-Voice
git switch -c my-voice-development
```

3. Develop in your branch, and check `LICENSE` and `NOTICE` before redistributing changes.

## Credit and history

BloodMoon Voice is **based on WolfVoice**, originally authored by the upstream WolfVoice contributors. This repository provides a BloodMoon-modified snapshot with separate branding; it does not replace or claim ownership of upstream authorship.

- Original upstream: https://github.com/intelligentwolf/wolfvoice
- BloodMoon development source: https://github.com/Vlad-BloodMoon/wolfvoice
- Imported release: https://github.com/Vlad-BloodMoon/wolfvoice/releases/tag/v0.2.0-bloodmoon.4

The **files and source tree are preserved exactly** in the import snapshot. The full original commit history, GitHub issues, pull requests, release records, and GitHub Actions run history are **not cloned** into this new repository.
