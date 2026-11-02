# Deep24 Habit Contracts for Claude Code

Status: current
Last updated: 2026-11-02

This plugin connects Claude Code to the hosted Deep24 Connector so you can set
habit goals, report check-ins and manage optional money stakes from the terminal.
It ships no server code: the `deep24` MCP server is `https://api.deep24.com/api/connector/mcp`,
the same endpoint ChatGPT, claude.ai, Grok and Meta Muse use. A skill teaches
Claude the rules of the tools.

## Connect to Deep24

Anyone can add the hosted server as a custom web connector in Claude. Use
`https://api.deep24.com/api/connector/mcp`, choose OAuth with Claude’s published
identity, then allow the connection on Deep24. Ask “Show my habit goals on
Deep24” to open an interactive view in a host that supports MCP Apps.

## Install in Claude Code

If you have a copy of this plugin folder, start Claude Code from its parent directory.

```sh
claude --plugin-dir ./deep24-habit-contracts
```

Then run `/mcp`, choose `deep24` and sign in. Your browser opens deep24.com,
where you allow the connection as your Deep24 account or as a guest. The plugin
asks for the `habits:read` and `habits:write` permissions. Starting a paid goal
needs the separate `habits:activate` permission. If your host cannot obtain it,
use the secure Deep24 review supplied for that goal.

## What it can do

| Tool | Permission | What it does |
| --- | --- | --- |
| `get_accountability_brief` | read | Today's deadlines, check-ins, pending goals, charges and card status |
| `list_habit_contracts`, `get_habit_contract` | read | Your goals and one goal's exact terms |
| `propose_habit_contract` | write | Propose a one-off or recurring goal; paid goals wait for your acceptance |
| `report_habit_checkin` | write | Record done, not done or unavailable for one occurrence |
| `pause_`, `resume_`, `cancel_habit_contract` | write | Pause or cancel an active goal after today. Resume on the next eligible day |
| `request_charge_refund`, `list_habit_charges` | write / read | Corrections and charge history |
| `activate_habit_contract` | activate | Start a paid goal only after you accept its exact terms in chat |

For new paid goals, Deep24 requires proof before the cutoff, five minutes after
each deadline. A missed occurrence can incur one charge within the agreed cap.
Card entry takes place on Stripe. Deep24 reviews corrections for new-goal
charges. For an older agreement, a refund that would return funds immediately
requires a request on the Deep24 website.

## Privacy Policy

Deep24 stores your goals, check-ins, assessments you ask Claude to record, your
consent records and charge history. Card numbers stay with Stripe. For chat activation, Deep24 records your exact acceptance instruction.
Optional
server media verification uses AWS storage and Google Gemini. The privacy policy
explains retention, payment records and how to request deletion: <https://deep24.com/privacy>. Terms: <https://deep24.com/terms>.
Support: <https://deep24.com/support>. You must be 13 or older to use Habit
Contracts; Claude accounts have their own minimum age.

The MIT license applies to this plugin’s packaging and instructions. Deep24
retains its name and marks. The hosted service has its own terms.
