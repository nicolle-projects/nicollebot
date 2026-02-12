## nicollebot

nicollebot is a **security-first** Discord bot that provides:
- Party-style interactive games
- A strict WSOP tournament payout calculator (**table-only; no estimation**)
- Minimal win-count leaderboards (**data-minimized**)

## Core principles (non-negotiable)
- **Capability denial:** nicollebot is **not** a moderation bot and does not provide moderation features.
- **Data minimization:** Stores only what is required for leaderboards (see Privacy Policy).
- **Deterministic behavior:** Explicit state transitions and predictable outputs.
- **Strict poker mode:** No estimation, interpolation, extrapolation, or fallback math.

## Features
### Games
- Diamond Mine — timed elimination game with difficulty scaling
- Dead or Paid — survival number-pick elimination
- One Remains — letter elimination game
- The Line — over/under RNG betting game
- Cupcake Chaos — planned/limited availability (may be under construction)

### Tournament Games
- Diamond Mine Tourney — A high-stakes survival tournament. Miners compete through multiple rounds to find gems while avoiding coal. Features a shield system, progressive difficulty scaling, and two ways to win: Last Survivor Standing or Top XP Score.

### WSOP Multi-Table Tournament Payout Calculator

nicollebot includes a **strict WSOP MTT payout calculator** that uses only
official World Series of Poker payout tables.

Entrant limits inside nicollebot:
- Entrants < 2 → rejected
- Entrants 2–3000 → calculated using locked WSOP payout tables
- Entrants > 3000 → not supported by the bot

Official WSOP payout structures **do exist** for tournaments larger than
3000 entrants, but those extended tables are intentionally **not embedded**
inside nicollebot.

For tournaments with 3001+ entrants, reference the official WSOP source:
https://www.wsoponline.com/nv/how-to-play-poker/mtt-tournament-payouts

Key rules:
- No estimation, interpolation, or extrapolation
- Percentages pulled directly from WSOP tables
- Calculations based on the **total prize pool**, not individual buy-ins
- Exact entrant bracket matching required

Supported payout structures:
- Top 10% (WSOP Standard)
- Top 15% (WSOP Extended)
- Top 20% (WSOP Extended)

Commands:
- `!pokerpayout <entrants> <total_pot>`
- `/pokerpayout`

### Data Source (Single Source of Truth)
All payout data comes from:
```
data/wsop_mtt_payouts.json
```
Properties:
* Human-auditable
* Locked reference data
* Used **exclusively** by the calculator
* `poker.py` contains **ZERO embedded payout values**

**WSOP MULTI-TABLE TOURNAMENT PAYOUT CALCULATOR**
**Data Source:** [WSOP Standard MTT Payout Tables](https://www.wsoponline.com/nv/how-to-play-poker/mtt-tournament-payouts)

## Privacy & safety
nicollebot is designed to minimize retained data and reduce exposure.

- Privacy Policy: `PRIVACY_POLICY.md`
- Security Overview: `SECURITY_OVERVIEW.md`
- Terms of Service: `TERMS_OF_SERVICE.md`

## Data removal requests
Users may request removal of their leaderboard entries.  
See `PRIVACY_POLICY.md` for the process.

## Contact
For support and data removal requests, contact the server owner, server admin or bot owner, through DM via Discord.
