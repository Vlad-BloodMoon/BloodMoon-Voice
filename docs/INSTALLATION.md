# BloodMoon Voice — complete English installation guide

**[Guide français pas à pas](INSTALLATION_FR.md) · [Téléchargements compilés](TELECHARGEMENTS_FR.md)**

Precompiled OpenSim 0.9.3.1 DLL/PDB package: [download ZIP](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/download/v1.0.0/WebRTC_Modules_BloodMoon_0.9.3.1-webrtc.3.zip). No compilation is needed for this matching BloodMoon version. The complete AdHoc feature set requires the **BloodMoon-modified WolfVoice server**, the **matching regional modules** and a **separate conference PHP/MySQL registry**, not stock upstream WolfVoice.

For this fork, the current setup.sh on main installs the BloodMoon Voice v1.0.0 server (same original BloodMoon-validated binary) but does NOT create signing keys, install simulator DLLs, or deploy the conference PHP API. Existing servers using the validated `.2/.3` binary need no server replacement for this feature. Historical source tags retain their original installer; use the current script linked below.
Create them manually as described below. Never paste a key into a public issue,
README, screenshot or download archive.

[Overview and features](../README.md) · [French installation](INSTALLATION_FR.md) · [Release v1.0.0](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0)

This guide is for **new OpenSimulator 0.9.3.1 / .NET 8 installations** and for operators who need to understand how to connect all three projects. Do not rerun an installer on a working production voice host merely to update the documentation.

## Overview — the three components

1. **Voice server:** [BloodMoon Voice v1.0.0](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0), a self-hosted modified WebRTC voice server.
2. **Every simulator:** the **three DLL/PDB pairs** from **[OpenSimulator v0.9.3.1-webrtc.3](https://github.com/Vlad-BloodMoon/opensim/releases/tag/v0.9.3.1-webrtc.3)**. The correct AdHoc regional DLL is already included. Do **not** replace it with an older standalone DLL.
3. **AdHoc conferences (separately hosted):** the PHP/MySQL service from [OpenSimulator-AdHocConferences](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences). Basic spatial/private/group voice does not require this API; multi-avatar AdHoc text/voice conferences do.

**Quick start for a new Ubuntu 24.04 Linux x86-64 voice host:** prepare your public IP, a real voice DNS name, TCP 9443 for authorized simulator hosts, UDP 40000–40999 for viewers and TCP 80 for certificate issuance if using Certbot standalone. Back up any existing voice files. Download the [BloodMoon Voice installer archive](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/download/v1.0.0/BloodMoon-Voice-installer-v1.0.0.tar.gz), extract it, review `setup.sh` and run it as root. It installs **only the voice host**, downloads and checks the pinned server binary, configures its service and TLS, but does not install simulator modules, keys or the AdHoc API.

```bash
mkdir -p "$HOME/bloodmoon-voice-install" && cd "$HOME/bloodmoon-voice-install"
curl -fL https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/download/v1.0.0/BloodMoon-Voice-installer-v1.0.0.tar.gz -o installer.tar.gz
mkdir source && tar -xzf installer.tar.gz -C source
cd source
less setup.sh
sudo bash setup.sh
```

The installer asks for your **own** public IP and DNS hostname. Ensure you have reviewed firewall/TLS prerequisites before running it. Then perform the key configuration below, install the matching module ZIP and, only for AdHoc conferences, deploy your own API.

## 1. Fresh installation or existing installation?

Fresh Linux x86_64 server with systemd: download and review setup.sh from this
fork, then run it. It installs the pinned fork release and prepares the service
and TLS. It needs a hostname pointing at the server; standalone Certbot needs
port 80 available. On a machine already hosting Apache or another TLS setup,
prepare the certificates using your existing method rather than blindly running
the standalone certificate step.

```bash
curl -fsSLO https://raw.githubusercontent.com/Vlad-BloodMoon/BloodMoon-Voice/main/setup.sh
less setup.sh
sudo bash setup.sh
```

Existing working installation: do not rerun the installer. Verify the release
checksum, back up and replace only the binary, retaining your service unit, TLS
certificates, firewall/IP allowlist and resource limits. A service restart briefly
disconnects voice users.

## 2. Choose the key mode before creating files

- Fresh deployment with matching modules on ALL region hosts: create BOTH
  cred_key (room tickets) and rpc_key (relay authentication).
- Rolling upgrade with old region modules still using the shared server: create
  ONLY cred_key for now and retain the IP allowlist. This is the mode validated
  during this grid's rollout. Do not create rpc_key or set WOLFVOICE_RPC_KEY until
  every region using the service can send the relay key.
- If rpc_key already exists, do not remove or replace it as part of this guide.
  Distribute its existing contents to authorized, matching region modules.

The relay key is optional in the software. When configured on the server, it is
mandatory on every region request; old modules without it lose voice access.
Private and group voice need at least a credential/signing key. Firewall
restrictions remain necessary in either mode.

## 3. Create the server key files without overwriting existing keys

Run on the VOICE SERVER after the wolfvoice user/group exists.
This command creates only cred_key. For a fresh, fully updated deployment,
change the for line to: for key in cred_key rpc_key; do

```bash
sudo bash <<'KEYS'
set -euo pipefail
install -d -o root -g wolfvoice -m 750 /etc/wolfvoice
for key in cred_key; do
    path="/etc/wolfvoice/$key"
    if [ -e "$path" ]; then
        test -s "$path" || { echo "Existing empty key file: $path"; exit 1; }
        echo "Keeping existing key: $path"
    else
        umask 077
        openssl rand -hex 32 > "$path"
        echo "Created key file: $path"
    fi
    chown root:wolfvoice "$path"
    chmod 640 "$path"
done
KEYS
```

Do not rerun a raw openssl command with > on an existing key: that rotates it and
breaks signatures with the old secret. The command above preserves existing
nonempty files and prints only file paths, never their contents.
If WOLFVOICE_CRED_KEY or WOLFVOICE_RPC_KEY is set in your service environment,
it takes precedence over the corresponding file. Keep one intentional source.

## 4. Transfer the SAME key to each authorized region host

Transfer cred_key privately (SFTP/SCP or an equivalent secure channel). Do NOT
generate a different key on each region. If relay authentication is enabled,
transfer the existing rpc_key too.

On the REGION HOST, in the directory containing the transferred cred_key:
replace opensim below with the actual user and group that run your simulator.

```bash
sudo install -d -o opensim -g opensim -m 750 /etc/opensim
sudo install -o opensim -g opensim -m 600 cred_key /etc/opensim/voice_cred_key
# Only when the server uses a relay key:
# sudo install -o opensim -g opensim -m 600 rpc_key /etc/opensim/voice_rpc_key
```

After confirming the transfer, remove temporary copies of the secrets.

Alternative for an external host: put the administrator-supplied credential key
in the simulator's bin/voice_cred_key and use CredKeyFile = voice_cred_key.
The simulator must start with bin as its working directory, and its service
account must be able to read the file. No .txt extension.

## 5. Configure the existing region INI

Install the three matching WebRTC DLL/PDB pairs first. In the existing dedicated
bin/config/wolfvoice.ini, adapt this example:

```ini
[WebRtcVoice]
    Enabled = true
    SpatialVoiceService = WebRtcVoice.dll:WebRtcVoiceServiceConnector
    NonSpatialVoiceService = WebRtcVoice.dll:WebRtcVoiceServiceConnector
    WebRtcVoiceServerURI = https://voice.example.org:9443
    ; New independent installs with relay authentication: use BOTH keys.
    RpcKeyFile = /etc/opensim/voice_rpc_key
    CredKeyFile = /etc/opensim/voice_cred_key
    MessageDetails = false

[VivoxVoice]
    enabled = false
```

**The configuration above illustrates the two-key relay-authentication mode recommended in the French fresh-install guide.** Create and securely distribute both keys before using it. If instead you already run a validated **single credential key + trusted-IP allowlist** setup, keep that configuration and omit RpcKeyFile if relay authentication is not enabled. The default RpcKeyFile path may be read when the file exists, even if not explicitly set; check for an existing file before switching modes. Do not change live keys without a migration plan.

## 6. Start and test

After the files are in place, restart the voice server to load its key:
```bash
sudo systemctl restart wolfvoice.service
sudo systemctl status wolfvoice.service --no-pager
sudo journalctl -u wolfvoice.service -n 40 --no-pager
```

Then restart the updated simulator. The server reads keys at startup; region
modules re-read files within about one minute, but DLL changes need a restart.
Test local voice, participant dots, per-user volume, a private invitation and
audio in both directions, then hangup and return to local voice.


## 6A. BloodMoon multi-avatar IM conferences and optional group voice

**Read the [complete three-project installation guide](COMPLETE_STACK.md).** Required: [BloodMoon Voice server v1.0.0](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0), **the exact [OpenSimulator module release v0.9.3.1-webrtc.3](https://github.com/Vlad-BloodMoon/opensim/releases/tag/v0.9.3.1-webrtc.3)** (including the already-integrated AdHoc regional DLL), and the separately installed PHP/MySQL conference registry from [OpenSimulator-AdHocConferences](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences). **Do not replace the .3 regional DLL with an older standalone AdHoc DLL.**

**Compatibility warning:** the **stock upstream WolfVoice server has not yet integrated the complete BloodMoon spatial-axis/group-voice work**. The tested complete package is for the [BloodMoon WolfVoice server](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0) with the [OpenSim WebRTC module `v0.9.3.1-webrtc.3`](https://github.com/Vlad-BloodMoon/opensim/releases/tag/v0.9.3.1-webrtc.3). Ad-hoc conferences **additionally depend on a separate central conference registry** ([PHP/MySQL source, installation and trust model](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences)). A standalone server and three DLLs **do not** create a conference registry.

**For independent self-hosted conferences:** add your **own** conference API address to the existing `[WebRtcVoice]` section without disturbing server URI/key files:

```ini
ConferenceApiUrl = https://conference.example.org/adhocconf/wolf_api.php
```

The `example.org` address above is a fictional placeholder; it is not hosted by BloodMoon and will not work until replaced. Your deployed API must restrict access to authorized trusted simulator hosts sharing the correct voice credential key. For your **own independent grid**, deploy and configure your **own** conference registry and use **your own** URL and key material instead. Do not copy BloodMoon's key onto unknown or untrusted machines. A grid operator holding the shared secret can sign actions for other avatar UUIDs: consider independent per-simulator authorization before federation.

**Both simulators** need the `.3` compatible regional DLL **and** their own `ConferenceApiUrl` line. If the second simulator is missing the line, its residents will not receive multi-avatar IM conference invitations. Restart each updated simulator.

The features validated on BloodMoon (7 Nov 2026): private IM voice, standard OpenSim group calls, multi-avatar **text** IM conferences (without echo), optional conference **voice** across separate simulators, independent audio hang-up and rejoin with other members remaining unaffected.

## 6B. Deploy your own AdHoc conference API (required for ad-hoc conferences)

**Follow this section for an independent self-hosted grid.** Multi-avatar text conferences and optional conference audio need the independent [OpenSimulator-AdHocConferences](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences) backend. This step is not performed by the voice server installer.

1. Prepare an HTTPS web host running PHP 8.3+ with PDO MySQL and a compatible MySQL 8 database.
2. From the AdHoc repository deploy **`web/wolf_api.php`**, create a private `wolf_config.php` using **`web/wolf_config.php.example`**, and import **`database/wolf_schema.sql`** into a separate database. Do **not** use the older prototype `web/conferences.php` or `database/schema.sql`.
3. Create a restricted MySQL account. Set the DSN, credentials, existing voice credential key path and session limits in the private PHP configuration. Restrict file access and HTTP access to configuration files.
4. **Important independent-install prerequisite:** the PHP currently checks `wolfvoice_admin.servers` with `status='allowed'` for permitted simulator IPs. Supply a compatible trusted-server registry and narrowly scoped read permissions, **or adapt the PHP allowlist logic for your own infrastructure**. Without this dependency, the current backend cannot be deployed as-is.
5. Publish `wolf_api.php` over HTTPS. The API expects authenticated signed POST requests; merely loading it in a browser is not a successful functional test.
6. In `[WebRtcVoice]` on **every participating simulator**, configure your **own** API hostname:

```ini
ConferenceApiUrl = https://conference.example.org/adhocconf/wolf_api.php
```

**This address is an intentionally non-working example.** Replace the host and path with the actual address of your installed conference API. BloodMoon's production API is private and not available as a public service. Do not reuse its keys.

7. Restart all updated simulators. Verify ad-hoc text and optional voice with participants on the same simulator, then on **different simulators**. The `.3` modules and the API URL must be configured on both ends.

For database, PHP and trust-model details, see the dedicated [AdHoc English deployment guide](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences/blob/main/docs/INSTALLATION.md). The shared-secret model is appropriate only for **trusted simulator operators**; it is not authenticated open federation.

## 7. Network and common errors

Keep TCP 9443 restricted to the public egress IPs of authorized region hosts.
Allow the configured UDP media range (default 40000-40999) for viewers.
Do not change existing grid firewall rules just to install this update.

- Local voice works but calls do not: check the matching DLLs, credential file
  path, file permissions and identical key contents. For cross-simulator calls,
  both simulators need matching updated modules.
- "wrong or missing relay key": the server requires rpc_key, but the region is
  sending none or a different one. Do not regenerate keys to fix this.
- TLS certificate refused: use the certificate's hostname in the voice URI,
  not the server's numerical IP.
- A warning that no relay key is configured is expected in credential-only mode;
  the IP allowlist is then the access restriction.
- No key value should appear in support messages.

Existing OpenSim group voice calls and cross-region invitations were validated with module release v0.9.3.1-webrtc.3. For an existing compatible installation, replace only WebRtcVoiceRegionModule.dll and its matching PDB; keep the other DLLs, configuration and keys, then restart. Ad-hoc conferences need a separate
external service.


### Known Firestorm UI limitations for group calls

The French “No Answer” message after a normal group hang-up is an incorrect translation of Firestorm's `nearby` string; it does not mean that all members must answer. It has been reported by the operator. No viewer translation fix is included in the module package.
Some invited participants may have a disabled hang-up button in their group IM window; use the red hang-up button in the headset/voice participants panel. The cause of this separate issue is not confirmed.
This repackaged ZIP contains the unchanged validated DLL/PDB files and updated documentation; no recompilation or credential is included.
