# BloodMoon Voice 🎙️

**Serveur vocal WebRTC indépendant pour OpenSimulator 0.9.3.1 / .NET 8, avec appels privés, voix de groupe et conférences à plusieurs avatars.**

**[Read the complete English documentation](README.md)** · [Guide d'installation français](docs/INSTALLATION_FR.md) · [Téléchargements v1.0.0](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0)

**BloodMoon Voice** permet d'héberger son **propre serveur vocal** pour une grille OpenSimulator, sans dépendre de Vivox. Développé à partir de WolfVoice, il intègre les corrections et évolutions BloodMoon pour la spatialisation, les appels privés et les conférences écrites et vocales entre plusieurs avatars.

## Toutes les fonctionnalités

| Fonctionnalité | Ce qu'elle apporte |
| --- | --- |
| **Voix de proximité en 3D** | Entendre les avatars autour de soi, avec une spatialisation et des axes d'écoute corrigés. |
| **Appels vocaux privés** | Appeler un autre avatar directement depuis une conversation IM privée. |
| **Voix des groupes OpenSimulator** | Participer aux appels vocaux des groupes habituels et recevoir les invitations. |
| **Conférences IM AdHoc** | Créer une conversation écrite entre plusieurs avatars, sans créer de groupe OpenSimulator. |
| **Voix facultative dans une conférence** | Lancer ou rejoindre un appel vocal sans obliger tous les participants à quitter le mode texte. |
| **Conférences entre simulateurs** | Échanger invitations, messages et voix entre avatars situés sur différents simulateurs. |
| **Raccrocher et revenir indépendamment** | Quitter l'audio puis le rejoindre sans fermer la conférence écrite ni déconnecter les autres. |
| **Indicateurs et volumes individuels** | Utiliser les indicateurs de parole et les réglages de volume par participant du viewer compatible. |
| **Hébergement autonome** | Maîtriser le serveur, les certificats TLS, les clés, les accès et les ports multimédias WebRTC. |

La voix de proximité, les appels privés et la voix des groupes nécessitent les deux premiers composants ci-dessous. **Les conférences AdHoc écrites et vocales nécessitent aussi l'API PHP/MySQL indépendante.** Ces fonctions ont été testées sur BloodMoon avec des viewers Firestorm compatibles ; elles ne sont pas garanties sur toutes les versions et tous les forks d'OpenSimulator.

## Les trois dépôts à utiliser, dans cet ordre

| Étape | Dépôt | À installer |
| --- | --- | --- |
| **1 — Serveur vocal** | [BloodMoon Voice v1.0.0](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0) | Le serveur WebRTC et ses paramètres réseau, TLS et clés. |
| **2 — Modules des simulateurs** | [OpenSimulator v0.9.3.1-webrtc.3](https://github.com/Vlad-BloodMoon/opensim/releases/tag/v0.9.3.1-webrtc.3) | **Les trois paires DLL/PDB compatibles**, sur chaque simulateur concerné. |
| **3 — Service central de conférences** | [OpenSimulator-AdHocConferences](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences) | L'**API PHP/MySQL**, sur ton serveur web, pour les conférences AdHoc. |

**Attention aux versions !** La DLL régionale AdHoc correcte est **déjà intégrée** dans le ZIP OpenSimulator `.3`. **Ne la remplace pas par l'ancienne DLL autonome** proposée dans la release historique du dépôt AdHoc. L'installateur du serveur vocal ne déploie pas automatiquement les DLL ni l'API des conférences.

## Quelle adresse mettre dans la configuration ?

Chaque administrateur doit utiliser **ses propres adresses** dans `bin/config/wolfvoice.ini` :

```ini
[WebRtcVoice]
Enabled = true
WebRtcVoiceServerURI = https://voice.example.org:9443
RpcKeyFile = voice_rpc_key
CredKeyFile = voice_cred_key
ConferenceApiUrl = https://conference.example.org/adhocconf/wolf_api.php
```

**Ces deux adresses sont fictives et ne fonctionnent pas telles quelles.** Remplace `voice.example.org` par le vrai nom DNS de **ton serveur vocal** et `conference.example.org` par le domaine HTTPS où **tu as installé `wolf_api.php`**. **Cet exemple correspond au mode à deux clés du guide de nouvelle installation :** `RpcKeyFile` pour authentifier les requêtes des simulateurs et `CredKeyFile` pour les identifiants vocaux. Il faut réellement créer et distribuer les fichiers de clés selon le [guide français](docs/INSTALLATION_FR.md). **Les installations existantes fonctionnant avec une seule clé et une liste d’IP autorisées, comme BloodMoon, doivent conserver leur configuration actuelle sans ajouter ni régénérer de clé RPC.** L'URL `ConferenceApiUrl` doit être configurée sur **chaque simulateur** participant aux conférences inter-simulateurs.

L'API de production BloodMoon n'est **pas un service public accessible sans autorisation**. Pour une installation indépendante, déployer sa propre API PHP/MySQL, définir `authorization_source = local` et renseigner `trusted_simulator_ips` avec les IP publiques sortantes autorisées. **Aucune base `wolfvoice_admin.servers` n'est nécessaire dans ce mode.** Le mode `bloodmoon_db` reste disponible pour BloodMoon. Conserver ses propres clés privées.

## Installation complète : commencer ici

**[Guide français pas à pas, de zéro jusqu'aux tests](docs/INSTALLATION_FR.md)** : préparation réseau, installation du serveur, TLS, clés, modules, configuration, déploiement de l'API PHP/MySQL, tests et dépannage.

[Vue d'ensemble des trois composants](docs/COMPLETE_STACK.md) · [Installation détaillée de l'API de conférences](https://github.com/Vlad-BloodMoon/OpenSimulator-AdHocConferences/blob/main/docs/INSTALLATION.fr.md)

## Téléchargements, développement et remerciements

La [release BloodMoon Voice v1.0.0](https://github.com/Vlad-BloodMoon/BloodMoon-Voice/releases/tag/v1.0.0) contient le binaire du serveur issu de la version utilisée sur BloodMoon et les modules compatibles. Pour une nouvelle installation, choisir **`BloodMoon-Voice-installer-v1.0.0.tar.gz`**, et non l'ancienne archive d'installation WolfVoice conservée à titre historique.

Les développeurs peuvent utiliser le bouton **Fork** du dépôt pour créer et faire évoluer leur propre version. Les sources initiales proviennent de [WolfVoice BloodMoon v0.2.0-bloodmoon.4](https://github.com/Vlad-BloodMoon/wolfvoice/releases/tag/v0.2.0-bloodmoon.4).

**Remerciements :** BloodMoon Voice s'appuie sur le projet [WolfVoice](https://github.com/intelligentwolf/wolfvoice), le travail de ses auteurs et contributeurs, notamment Lone Wolf de WolfGrid, ainsi que l'intégration et les tests réalisés sur BloodMoon. Conserver les fichiers [LICENSE](LICENSE) et [NOTICE](NOTICE). [README original de WolfVoice](docs/ORIGINAL_WOLFVOICE_README.md).
