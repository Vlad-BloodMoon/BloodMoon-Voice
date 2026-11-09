# BloodMoon Voice: complete three-project installation

**Use all three matching projects. Do not replace the tested OpenSimulator regional DLL with an older AdHoc DLL.**

## 1. Voice server

[BloodMoon Voice v1.0.0](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0) is the modified WebRTC voice server, derived from the tested BloodMoon WolfVoice v0.2.0-bloodmoon.4. Install on the voice host using [the French guide](INSTALLATION_FR.md) or [English guide](INSTALLATION.md). Configure TLS, permissions, firewall, private keys and trusted simulator access.

## 2. OpenSimulator modules — EXACT pinned version

**[Vlad-BloodMoon/opensim release v0.9.3.1-webrtc.3](https://github.com/Vlad-BloodMoon/opensim/releases/tag/v0.9.3.1-webrtc.3)** is the canonical compatible release for OpenSimulator 0.9.3.1 / .NET 8.

Stop each simulator and back up its existing files. Install all three matching DLL/PDB pairs from the release ZIP: WebRtcVoice.dll, WebRtcVoiceRegionModule.dll and WebRtcVoiceServiceModule.dll, plus their corresponding PDB files. Keep unrelated OpenSimulator modules and preserve existing settings and key files.

**The regional DLL in this .3 ZIP ALREADY CONTAINS the tested AdHoc conference fixes. Do not overwrite it with the older standalone AdHoc DLL, a .2 release, generic Janus binaries, or another module build.**

## 3. Independent AdHoc conference project — central PHP/MySQL API

**[Vlad-BloodMoon/OpenSimulator-AdHocConferences](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences)** supplies the independent conference backend, schema, source patches and [dedicated installation instructions](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences/blob/main/docs/INSTALLATION.md).

For a **self-hosted** conference service, deploy the current files from that repository:

- web/wolf_api.php — the PHP conference API
- web/wolf_config.php.example — a template for a PRIVATE wolf_config.php
- database/wolf_schema.sql — MySQL conference schema

For new independent deployments, configure the PHP API with `authorization_source = local` and `trusted_simulator_ips` set to your approved simulator egress IP addresses; this avoids any dependency on BloodMoon's admin database. HTTPS and the existing shared private voice credential are still required. BloodMoon's historical `bloodmoon_db` mode remains supported. Do not expose keys or use the obsolete prototype files web/conferences.php and database/schema.sql.

On **EVERY participating simulator**, keep existing voice settings and configure ConferenceApiUrl in the [WebRtcVoice] section of wolfvoice.ini to the HTTPS URL of YOUR conference API. For example:

~~~ini
[WebRtcVoice]
; Retain Enabled, WebRtcVoiceServerURI, CredKeyFile and other existing settings
ConferenceApiUrl = https://YOUR-CONFERENCE-HOST.example/adhocconf/wolf_api.php
~~~

The BloodMoon production endpoint is restricted to explicitly authorized trusted simulators; it is not a public conference API. The example.org address above is a placeholder, not a working endpoint. Independently hosted grids require their **own** PHP/MySQL endpoint and authorization.

Restart each updated simulator, then test proximity audio, private voice, multi-avatar text invitations and optional voice across two different simulators. Each participating simulator must have BOTH the matched .3 DLLs and ConferenceApiUrl.

## Key distinction / précision importante

**There are three projects, but NOT three DLL installations.** The standalone AdHoc project supplies the conference PHP/MySQL service and development sources. Its compatible regional DLL has already been integrated into OpenSimulator v0.9.3.1-webrtc.3.

**En français :** utiliser le serveur BloodMoon Voice v1.0.0, les TROIS paires DLL/PDB de la release OpenSimulator v0.9.3.1-webrtc.3 sur chaque simulateur, puis installer séparément l'API PHP/MySQL du dépôt OpenSimulator-AdHocConferences pour les conférences auto-hébergées. Ne surtout pas remplacer la DLL régionale .3 par une ancienne DLL AdHoc. La configuration ConferenceApiUrl est nécessaire sur chaque simulateur participant.
