# nicollebot — Security Overview
Last updated: 2026-02-08

## Purpose and scope

nicollebot is a **security-first Discord game engine** and **strict WSOP MTT payout calculator**.  
It is **not** a moderation bot and is **not designed to administer servers**.

This document defines the bot’s:

- Security posture  
- Capability boundaries  
- Privacy guarantees  
- Installation and permission model  
- Responsible disclosure process  

All claims in this document are intended to be **verifiable from the codebase and runtime behavior**.

## Capability denial (safety by design)

nicollebot intentionally **does not implement moderation or server-management behavior**.

There are **no code paths** for:

- Kicking, banning, or timing out users  
- Deleting messages  
- Managing roles, channels, or server settings  
- Message scanning, filtering, or analytics  
- Executing user-supplied code  

Even if elevated permissions were granted accidentally, the bot’s feature set  
**cannot perform moderation actions**.

nicollebot is a **game engine, not an authority**.

## Installation & access control

nicollebot is distributed as a **private Discord application**.

Security characteristics:

- **Public Bot toggle:** OFF  
- **User installs:** Disabled  
- **Guild install:** Required  
- **Install link:** Owner-generated OAuth2 invite only  
- **Unauthorized self-installation:** Not possible  

This creates a **closed-distribution security model**, preventing uncontrolled spread.

## Required bot permissions

nicollebot requests **minimal gameplay-only permissions** necessary for:

- Interactive games  
- Embedded UI rendering  
- Custom emoji display  
- Multi-round state continuity  

### Permission usage

Permission | Purpose inside nicollebot
-----------|------------------------------------------------
View Channels | Allows the bot to detect where commands are issued.
Send Messages | Allows posting of game boards, results, and help menus.
Embed Links | Required for structured pink-themed UI and rule displays.
Attach Files | Used only when a feature requires image/file output.
Add Reactions | Enables player interaction and voting mechanics.
Read Message History | Required for multi-round gameplay continuity.
Use External Emojis | Required for all custom emoji-based UI elements.
Use Slash Commands | Enables the public command system and private admin UI.

### Explicit non-requirements

- **Administrator permission is NOT required**
- **No moderation permissions are requested**
- Permissions are intentionally minimized for **gameplay only**

## Data minimization (privacy by design)
nicollebot stores **only the minimum data required** to operate leaderboards.

### Stored data
- Discord User IDs  
- nicollebot **game win counts** (integers)

### Data explicitly NOT stored
- Message content  
- Usernames or nicknames  
- Avatars  
- Timestamps of user choices  
- Analytics or tracking identifiers  
- IP addresses, emails, phone numbers, or payment data  
- Cross-server behavioral history or profiling  

Leaderboard data is stored **locally on the bot host machine**  
and is treated as **private operational data**.

## External attack-surface reduction
nicollebot is intentionally designed to avoid common exploit vectors:
- **No databases**
- **No webhooks**
- **No external analytics or telemetry**
- **No external APIs required for gameplay or persistence**
- **No user-supplied file processing**
- **No remote code execution of any kind**

Runtime network activity is limited to:

**Discord’s official API only**

## Hosting & credential security

- Bot token is stored via **environment configuration (`.env`)**
- Tokens are **never hard-coded or committed**
- Stored leaderboard data is **not intentionally transmitted externally**
- Core functionality requires **no third-party infrastructure**

## Data removal (user privacy rights)
Users may request removal of their **nicollebot game leaderboard data**  
(the only retained user information).

### Removal process
1. The user contacts the **bot owner via Discord DM**.  
2. The owner removes the user’s ID from all nicollebot leaderboard records.  
3. The owner confirms completion privately.

### Important scope clarification
- Removal applies **only to data stored by nicollebot**.  
- Data stored by **other bots or server systems** must be handled by the  
  **server owner or administrators under their own policies**.

### Additional notes
- Removal is **irreversible**.  
- Only **win-count leaderboard entries** exist to remove.
  
## Secure interaction behavior

- Unauthorized access attempts fail **safely and predictably**.
- Public help surfaces **never expose admin-only or destructive commands**.
- Admin tooling is designed for **data integrity only**, not moderation.

## Verification checklist for server owners
Server owners can independently confirm:

- Administrator permission is **not required**.
- The bot role lacks **moderation capabilities**.
- Public help does **not reveal restricted commands**.
- Stored data equals **IDs + integer win counts only**.
- No outbound traffic exists beyond **Discord’s API**.

## Responsible disclosure
If a security issue is discovered:

- Contact the **bot owner privately via Discord DM**.
- Do **not** publicly share exploit details or reproduction steps.
- The owner will investigate and remediate as appropriate.

## Security model summary
nicollebot’s security posture is built on four principles:

1. **Capability denial** — cannot moderate or administer servers  
2. **Minimal data retention** — stores only IDs and game win counts  
3. **Closed distribution** — private installation via owner invite only  
4. **Deterministic, auditable behavior** — predictable and verifiable runtime  

nicollebot is intentionally designed to be:
- **Simple**  
- **Private**  
- **Predictable**  
- **Secure by architecture**


