---
name: habit-contracts
description: Use when the person wants to set a habit or one-off goal with Deep24, report that they did or did not do it, check what is due today, pause, resume or cancel a goal, or ask about habit charges and refunds. Covers the deep24 MCP tools (propose_habit_contract, report_habit_checkin, list_habit_contracts, get_accountability_brief and the rest).
---

# Deep24 Habit Contracts

Deep24 keeps a person's habit goals. A goal can have no money at stake, or a stake
that Deep24 charges to the person's own saved card when they miss an occurrence
under the exact terms they accepted. Deep24 checks deadlines and handles charges.

## First use

1. The `deep24` MCP server needs a sign-in. If a tool answers that sign-in is
   needed, tell the person to run `/mcp` and pick `deep24`. The browser opens
   deep24.com, where they allow the connection as their account or as a guest.
2. Call `get_habit_account_status` once. If the goals are guest goals, say that
   linking them to a Deep24 account is optional and protects them if this
   browser is lost. Never present linking as approval of any payment.

## Propose a goal

Before `propose_habit_contract`, agree with the person:

- The habit in their words (keep their wording as the title).
- One-off or recurring. For recurring: which days or every N days, and each
  deadline time. Every scheduled time is its own occurrence.
- The IANA time zone.
- Stake in whole dollars, or $0. Say plainly: a stake is charged per missed
  occurrence; a recurring goal has a weekly cap that defaults to three times the
  stake; a one-off goal is charged at most once. Do not invent a custom cap;
  pass `weekly_cap_usd` only when the person names an amount.
- How success is proven: self-report, or a photo you assess against an agreed
  observable rule. Write the agreed proof deadline into
  `verification_rules.success_criteria`. Do not promise proof after the server's
  cutoff, which is exactly five minutes after each deadline.

Pass a stable `client_request_id` so a retry does not duplicate the goal.

A $0 goal with self-report or a photo you assess starts at once unless another
consequence requires consent. Money, server media verification, email consequences
and device blocking need exact terms acceptance. Use the returned status and
next action. For an interactive view, call `show_habit_contracts`.

## Activation of a paid goal

Only after the person explicitly says to start that exact goal and accepts the
terms from `get_habit_contract` may you call `activate_habit_contract`. Show the
stake, cap if applicable, schedule, time zone, evidence rule, agreed proof time
and server cutoff. Check for an eligible goal and `payment_authorization.status`
of `ready`. Pass the returned terms version and hash, `accepted: true`, a stable
`client_request_id`, and the person's instruction unchanged in `user_instruction`.
The person must grant `habits:activate`. If consent fails, leave the goal pending.

When payment setup or browser review is needed, follow the server's instructions
for that goal. If it directs you to the widget, call `show_habit_contracts` with
the contract ID. Only report `widget_unavailable: true` to `get_habit_setup_links`
if the person says the widget failed. If the server supplies a Deep24 review
link, a guest opens it in the browser used for the original connection. Card
entry takes place on Stripe. Never collect card details in chat.

## Daily use

- `get_accountability_brief` answers "what is due today".
- `report_habit_checkin` records `done`, `not_done` or `unavailable` for one
  occurrence. Use `occurrence_id` from the goal when a day has several times.
  Report before the cutoff. Never mark several occurrences from one vague
  report. For an agreed photo rule, inspect the image, decide against the rule
  only, and send a short assessment with `source: photo`, `assessed_by: agent`.
  Do not send image data, URLs, health or location details in a check-in. For
  a goal with server media verification, follow its upload instructions. A chat
  assessment cannot complete that goal.
- Pausing or cancelling an active goal leaves today's counted occurrences in
  place. Cancelling a pending proposal withdraws it. Resuming starts on the
  next eligible day. Paused days stay uncharged. Confirm dates and explain that
  future misses on a resumed paid goal can be charged again.
- `list_habit_charges` and `request_charge_refund` for money questions. A
  valid charge on a new goal is final; corrections need a reason of
  `duplicate`, `unauthorized` or `technical_error`. Use the tool only on explicit
  request to record a correction. Eligible legacy requests made through Claude go to human review.
  Submitting a request does not itself refund the card.

## What you must not do

- Connecting the plugin does not schedule reminders or proof checks. Confirm
  host scheduling support before offering either. Deep24 still checks deadlines
  and applies the consequences the person accepted.
- Do not infer permission to start a paid goal from a saved card, an earlier
  goal, or a general "go ahead".
- Do not increase a cap, create a duplicate goal to bypass a cap, or collect
  misses that a cap already closed.
