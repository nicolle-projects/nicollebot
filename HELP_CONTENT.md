### nicollebot — Help & Command Guide
Last updated: 2026-02-12

This document provides a **public reference** for using nicollebot.

Only **non-admin, non-destructive** commands are listed here.  
Administrative or data-integrity tooling is intentionally **not documented publicly**.



## Overview
nicollebot is a **security-first Discord game engine** that provides:

- Party-style interactive games  
- A **strict WSOP Multi-Table Tournament payout calculator**  
- Minimal **nicollebot game leaderboard** win tracking  

Design principles:
- **Capability denial** — not a moderation bot  
- **Data minimization** — stores only IDs + game win counts  
- **Deterministic behavior** — predictable, auditable outcomes  
- **Strict poker mode** — table-driven WSOP payouts only  




## Public Help & Menu Commands
These commands provide access to menus, rules, and leaderboards.

- `!nicollebothelp` → Opens the main help interface (Public) 
- `/nicollebothelp` → Opens the main help interface (Private to you) 
- `!nicollebotcommands` → Displays available public commands  
- `!nicollebotgamerules` → Shows rules for supported games  
- `!nicollebotleaderboard` → Displays nicollebot game win leaderboards  

Public help **never exposes admin-only commands**.



## GAMES

### Diamond Mine
**Contest-ready multi-round mining elimination game**

- Join a timed expedition lobby  
- Each round, players select **one tile** on a 5×5 board  
- Outcomes:
  - **Coal** → consumes a shield or eliminates  
  - **Rare blue diamond** → grants shields  
  - **No click** → elimination  
- Persistent round summaries show:
  - Unearthed diamonds  
  - Eliminated players  
  - Remaining miners  
- Difficulty scales every 2 rounds:
  - Coal chance increases (capped)  
    - Rare chance decreases (floored)
- Wins are recorded to the leaderboard. View with `!nicollebotleaderboard` → Diamond Mine.
  
**Provably Fair RNG** → outcomes are cryptographically committed before each match and revealed at match end for verification
  - Command: `!diamondmine`
 
### Diamond Mine Tourney
Competitive survival tournament with three victory titles

- Join the high-stakes expedition lobby
- Three Victory Titles:
  - Last 3 Standing (survival title)
  - Top 3 Scores (XP title)
  - Sparkle Standouts (top XP among non–Last 3 finishers)
- Double Winners: Players can claim multiple titles in a single match for ultimate prestige
- Shield System: Everyone starts with 1 shield; Coal strikes consume a shield and apply an XP penalty instead of instant elimination
- Regular Diamonds: Grant XP at random
- Rare Blue Diamond: Finding this rare gem grants a massive XP boost and grants +2 shields
- Rare Flawless Diamond: Extremely rare find that doubles cumulative XP instantly and grants +1 shield
- Progressive Difficulty: Coal danger increases as the match advances into later rounds
- Demo Mode Support: Includes PLAY SOLO and PREVIEW RUN for testing or practice
- **Provably Fair RNG** → outcomes are cryptographically committed before each match and revealed at match end for verification
- Wins are recorded to the leaderboard. View with `!nicollebotleaderboard` → Diamond Mine Tourney.
- Command: `!diamondminetourney` (Use `!diamondminetourney demo` for testing)


## Provably Fair RNG

Both **Diamond Mine** and **Diamond Mine Tourney** use the same cryptographically verifiable RNG 
via a seed commitment + reveal model.

**Shared Model (both builds):**
- Commitment algorithm: SHA256
- Roll algorithm: HMAC-SHA256
- HMAC message format: `{client_seed}|{nonce}|{label}`
- Client Seed: Lobby Message ID (public, non-gameable)
- Server Seed: Secret until match end
- Nonce is label-derived and order-independent (multiplayer safe)

**Diamond Mine (Standard)**
- Nonce: `int(SHA256(label)[:8], 16)` — first 8 hex chars of label hash as 32-bit integer (deterministic)
- Labels are stable per round + tile:
- `diamondmine:r{round}:tile{tileIndex}:rare`
- `diamondmine:r{round}:tile{tileIndex}:coal`
- `diamondmine:r{round}:tile{tileIndex}:gem`
 
**Diamond Mine Tourney**
- Nonce: `int(SHA256(label)[:8], 16)` — first 8 hex chars of label hash as 32-bit integer (deterministic)
- Labels are stable per round + tile:
  - `tourney:r{round}:tile{tileIndex}:rare`
  - `tourney:r{round}:tile{tileIndex}:flawless`
  - `tourney:r{round}:tile{tileIndex}:coal`
  - `tourney:r{round}:tile{tileIndex}:gem`
  - `tourney:r{round}:tile{tileIndex}:xp:rare_blue`
  - `tourney:r{round}:tile{tileIndex}:xp:{gem_key}`

**How to verify:**
1. Copy the Server Seed Hash from the COMMIT message (posted at match start)
2. Copy the Server Seed from the REVEAL message (posted at match end)
3. Compute SHA256(Server Seed) — must exactly match the committed hash
4. Reproduce any tile roll using HMAC-SHA256 with the published label spec

For commitment verification: https://emn178.github.io/online-tools/sha256.html  
For full roll verification (HMAC-SHA256): https://www.toolkitbay.com/tkb/tool/HMAC_SHA256

## Other Games

### Dead or Paid
**Number-survival elimination game**

- Players choose a number from **1–10**  
- Each round reveals an unlucky number
- Difficulty increases at Round 10 (2 Unlucky Numbers)
- Matching players are eliminated  
- Continues until a winner (or all eliminated)
- Command: `!deadorpaid`


### One Remains
**Letter elimination survival**

- Players pick **A–E**  
- Each round removes one letter  
- Winners are players who chose the **final remaining letter**
- Command: `!oneremains`


### The Line
**Over/Under RNG betting**

- Players choose **UNDER** or **OVER** a fixed 7.5 line  
- RNG determines the result  
- Command: `!theline`


### Cupcake Chaos
**Grid-based elimination game**

- Logic exists but availability may be **limited or disabled**  
- May be under construction or restricted  
- Command: `!cupcake`



## WSOP Multi-Table Tournament Payout Calculator

nicollebot includes a **strict WSOP MTT payout calculator** that uses  
**official WSOP payout tables only**.
**Core rules**
- **No estimation**
- **No interpolation**
- **No extrapolation**
- **No fallback math**
- Percentages come directly from **official WSOP MTT tables**

### Entrant limits inside nicollebot
- **Fewer than 2 entrants → rejected**
- **2–3000 entrants → fully supported**
- **More than 3000 entrants → not supported by the bot**

Official WSOP payout structures **do exist** for tournaments larger than 3000 entrants,  
but those extended tables are intentionally **not embedded** in nicollebot.

For 3001+ entrants, reference the official WSOP source:  
https://www.wsoponline.com/nv/how-to-play-poker/mtt-tournament-payouts

### Calculation behavior
- Uses **total prize pool**, not individual buy-ins  
- Requires **exact entrant bracket match**  
- Paid places follow **WSOP rules exactly**  

### Supported payout structures
- **Top 10% — WSOP Standard (default)**  
- **Top 15% — WSOP Extended**  
- **Top 20% — WSOP Extended**

### Commands 
- `!pokerpayout <entrants> <total_pot>`

- `/pokerpayout` - The calculator modal and results embed is visible only to you (Private)

- `!pokerpayouthelp` - View help guide (Public)

- `/pokerpayouthelp` - View help guide (Private)



## Data & Privacy Summary
nicollebot stores **only**:
- Discord platform user IDs  
- nicollebot **game win counts** (integers)

nicollebot does **not** store:

- Message content  
- Usernames, avatars, or personal profile data  
- Analytics, tracking identifiers, or IP addresses  
- Cross-server behavioral history  

Full details: see **PRIVACY_POLICY.md**.




## Data Removal Requests
Users may request removal of their **nicollebot game leaderboard data**  
(the only stored user information).

Process:
1. Contact the **bot owner via Discord DM** to request data removal
2. The bot owner removes the user's leaderboard entries  
3. The bot owner confirms completion privately  

**Important:** Only the **bot owner** has the authority to remove nicollebot game leaderboard data.

Requests involving **other bots or server systems** must be directed to  
the **server owner or administrators**, as those systems are outside  
nicollebot's control.




## Security & Safety
nicollebot is intentionally designed to be:

- **Non-moderating** (cannot kick, ban, or manage servers)  
- **Data-minimal** (IDs + win counts only)  
- **Locally stored** (no external databases or analytics)  
- **Deterministic and auditable**  

See **SECURITY_OVERVIEW.md** for full details.



## Contact & Support

For privacy/data removal requests, contact the **bot owner via Discord DM**.

For help or support requests, contact the **bot owner via Discord DM**.

nicollebot does **not provide email-based support** in order to:
* Reduce spam risk
* Prevent impersonation
* Maintain Discord identity verification



