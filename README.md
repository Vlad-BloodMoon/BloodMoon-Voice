# BloodMoon Voice 🎙️

**Self-hosted WebRTC voice and multi-avatar conferences for OpenSimulator 0.9.3.1 / .NET 8.**

**[Lire la documentation complète en français](README.fr.md)** · [English installation guide](docs/INSTALLATION.md) · [Downloads](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0)

BloodMoon Voice allows an OpenSimulator grid to operate its **own voice server** instead of relying on Vivox. Based on the original WolfVoice project, it integrates BloodMoon's tested spatial-audio corrections, private calls, regular OpenSimulator group calls, and ad-hoc multi-avatar conferences with optional voice.

## What can you do with it?

| Function | Experience |
| --- | --- |
| **Spatial voice** | Hear other avatars nearby, with 3D positional audio and corrected listening axes. |
| **Private calls** | Start a voice call directly from a two-person instant-message conversation. |
| **OpenSimulator group voice** | Use voice invitations and calls in existing group conversations. |
| **Ad-hoc text conferences** | Start a multi-avatar IM conversation without creating an OpenSimulator group. |
| **Optional conference audio** | Join or start voice within a text conference without forcing everyone to join audio. |
| **Cross-simulator conferences** | Exchange invitations, text and optional audio between avatars on different simulator processes. |
| **Independent leave/rejoin** | Hang up and rejoin audio while keeping the text IM open and without disconnecting others. |
| **Voice controls** | Use compatible viewer speaking indicators and individual participant volume controls. |
| **Your own infrastructure** | Host your server, TLS, access policy, voice keys and WebRTC media ports. |

Spatial/private/group voice requires the first two components below. **Ad-hoc IM conferences additionally require the separate PHP/MySQL API.** Tests were carried out on BloodMoon with compatible Firestorm viewers; other viewer distributions, forks and security models may need additional validation.

## Three repositories, one complete stack

| Order | Project | Install |
| --- | --- | --- |
| **1 — Voice backend** | [BloodMoon Voice v1.0.0](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0) | The WebRTC voice server on your host. |
| **2 — Simulator integration** | [OpenSimulator v0.9.3.1-webrtc.3](https://github.com/Vlad-BloodMoon/opensim/releases/tag/v0.9.3.1-webrtc.3) | **All three matching DLL/PDB pairs** on each participating simulator. |
| **3 — Ad-hoc registry** | [OpenSimulator-AdHocConferences](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences) | Separately hosted PHP/MySQL conference API for ad-hoc conferences. |

**Do not mix DLL versions.** The tested AdHoc regional DLL is **already included** in the complete OpenSimulator `.3` module ZIP. Do **not** replace it with the older standalone DLL from the AdHoc project's historical release. The voice installer does **not** automatically install simulator DLLs or the conference registry.

## Two different URLs — both belong to your installation

In every relevant simulator's `bin/config/wolfvoice.ini`, configure your own voice server and your own conference API:

```ini
[WebRtcVoice]
Enabled = true
WebRtcVoiceServerURI = https://voice.example.org:9443
CredKeyFile = voice_cred_key
ConferenceApiUrl = https://conference.example.org/adhocconf/wolf_api.php
```

**Both domains above are fictional placeholders, not functioning public services.** Replace `voice.example.org` with your voice server's real TLS hostname and `conference.example.org` with the domain serving **your installed** `wolf_api.php`. Retain any other settings required by your voice authentication mode, including `RpcKeyFile` when used. For cross-simulator AdHoc conferences, every participating simulator must configure `ConferenceApiUrl`. BloodMoon's own hosted conference API is restricted to approved trusted simulators; it is not an endpoint others can freely use.

The current AdHoc PHP backend also expects an allowed-server registry (`wolfvoice_admin.servers`) and the shared credential key. **Independent operators must provide or adapt those dependencies.** Never publish voice keys.

## Start here: complete installation

**[English installation from start to finish](docs/INSTALLATION.md)** covers host prerequisites, TLS, firewall, installer, matching DLLs, keys, separate PHP/MySQL registry, URL configuration, verification and troubleshooting.

[Technical three-component guide](docs/COMPLETE_STACK.md) · [French guide](docs/INSTALLATION_FR.md) · [AdHoc backend installation](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences/blob/main/docs/INSTALLATION.md)

## Releases, development and credit

The [BloodMoon Voice v1.0.0 release](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0) retains the production-derived server binary and provides the matching modules plus a new BloodMoon-specific installer archive. Historical release assets are retained for provenance; for a new install, choose **`BloodMoon-Voice-installer-v1.0.0.tar.gz`** instead of the older WolfVoice installer archive.

To create your own derivative, use **Fork**, clone your fork and develop on your own branch. This repository's initial source snapshot matches [WolfVoice BloodMoon v0.2.0-bloodmoon.4](https://github.com/Vlad-BloodMoon/wolfvoice/releases/tag/v0.2.0-bloodmoon.4); it does not include all former GitHub issues or commit history.

**Attribution:** Based on [WolfVoice](https://github.com/intelligentwolf/wolfvoice) and the original WolfVoice contributors, including Lone Wolf / WolfGrid, with BloodMoon integration and testing. Preserve the included [LICENSE](LICENSE) and [NOTICE](NOTICE). The original README is [archived here](docs/ORIGINAL_WOLFVOICE_README.md).
