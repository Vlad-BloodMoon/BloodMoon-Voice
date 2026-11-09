# BloodMoon Voice

**Independent BloodMoon development baseline for the OpenSimulator WebRTC voice stack.**

[Download the original verified release](https://github.com/Vlad-BloodMoon/wolfvoice/releases/tag/v0.2.0-bloodmoon.4) · [BloodMoon Voice releases](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases) · [Source snapshot](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/commit/bdf9d9d325f5c5ab7cbec82a99bcc2f8a0040619)

## Start here — installation en français

Pour une grille indépendante, installer dans cet ordre : **1. [serveur vocal BloodMoon Voice v1.0.0](docs/INSTALLATION_FR.md)**, **2. [les six fichiers DLL/PDB de OpenSimulator v0.9.3.1-webrtc.3](https://github.com/Vlad-BloodMoon/opensim/releases/tag/v0.9.3.1-webrtc.3)** et **3. [l'API PHP/MySQL AdHoc indépendante](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences/blob/main/docs/INSTALLATION.fr.md)**. Les DLL AdHoc sont **déjà dans le paquet .3**, ne pas en ajouter d'autres. **L'URL `conference.example.org` dans les exemples est fictive** : utiliser votre propre URL HTTPS, et configurer `ConferenceApiUrl` sur chaque simulateur. [Guide complet des trois composants](docs/COMPLETE_STACK.md).

## What is included

This repository was imported from the **exact source tree** of `Vlad-BloodMoon/wolfvoice` at tag `v0.2.0-bloodmoon.4` (source commit `a16b600be45e34acd61d874183ce2a8c3e1fe4c7`).

Source tree SHA-1: `6da740f1b1d08f4a108f8d9d0fd1a4a95e7645f5`.

The baseline includes the Rust voice server source, client code, example configs, installer scripts, CI workflows, documentation, tests, `LICENSE` and `NOTICE`.

The [BloodMoon Voice v1.0.0 release](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0) includes the **9 original verified release assets** (Linux x86-64 server binary, OpenSimulator 0.9.3.1 modules and checksums) **plus a new BloodMoon Voice installer archive and its SHA-256**. The original 9 files remain unchanged.

**For new installations:** [download the BloodMoon Voice installer archive](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/download/v1.0.0/BloodMoon-Voice-installer-v1.0.0.tar.gz) and follow the [French guide](docs/INSTALLATION_FR.md) or [English guide](docs/INSTALLATION.md). The older `wolfvoice-installation-bloodmoon.4.tar.gz` is preserved as a historical archive and still contains original WolfVoice repository links. Do not assume the ZIP modules are present in the Git source tree.

## Required matching repositories

**[Complete three-project installation guide](docs/COMPLETE_STACK.md)** — The compatible OpenSimulator DLLs come **only** from [v0.9.3.1-webrtc.3](https://github.com/Vlad-BloodMoon/opensim/releases/tag/v0.9.3.1-webrtc.3). The independent [OpenSimulator-AdHocConferences](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences) repository supplies the separate PHP/MySQL conference service. **Do not replace the matching regional DLL from the .3 ZIP with an older standalone AdHoc DLL.**

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


---

[View preserved original WolfVoice README](docs/ORIGINAL_WOLFVOICE_README.md).
