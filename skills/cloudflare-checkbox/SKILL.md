---
name: cloudflare-checkbox
description: Automatically handle visible Cloudflare or Turnstile human verification checks in the user's regular external desktop browser and existing profile, including Brave, Safari, Chrome, or another controllable desktop browser. Never use a Codex in-app or embedded browser. Discover supported controls, try one permitted UI click after required action-time confirmation, inspect the result, and hand control to the user if it fails or remains uncertain.
---

# Cloudflare checkbox

Use the user's existing external desktop browser, profile, and target tab.
This skill supplies instructions; it adds no browser capabilities and cannot
override tool restrictions.

## Automatic activation

Apply this skill whenever a Cloudflare or Turnstile human verification check
appears during an authorized task in the user's external desktop browser. The user does not
need to name or invoke the skill, and loading it needs no separate approval.
Automatic discovery is enabled in `agents/openai.yaml` through
`policy.allow_implicit_invocation: true`.

Activate only in the browser task already underway. Do not monitor unrelated
tabs or run in the background. Automatic activation preserves the one-click
budget, result verification, manual takeover, and the browser tool's required
confirmation immediately before a CAPTCHA click.

## Browser selection

Use `mcp__cua_repl` for browser/computer interaction. Initialize it with a single
documented entry-point call and read the returned instructions before proceeding.

Reuse an existing valid browser/app handle when the task already has one.
Otherwise inspect `cua.getState()` and match the user's requested browser,
profile, window, and target tab to the observed inventory. Browser names below
are examples, not an allowlist.

- **Native desktop browsers:** Select the user's browser app through native
  computer use when no suitable external tab connection already exists. On macOS,
  pass the observed app name or bundle ID to `cua.getApp()`; for example,
  Brave's identifier is `com.brave.Browser`. Chrome, Safari, Firefox, Edge, Arc,
  Opera, Vivaldi, and other desktop browsers follow the same capability check,
  not a promise of support. On platforms requiring a window ID, use the
  documented window inventory and `cua.getApp({ windowId })`. Inspect fresh
  accessibility state or a screenshot and use supported native UI interaction
  in the existing target tab. Use screenshot coordinates only when native input
  is supported and the target is visibly unambiguous.
- **Ambiguous app identity:** If multiple copies share a bundle ID, use the
  exact installed application path reported by the tool, such as
  `/Applications/Google Chrome.app`. Confirm the returned window and profile
  before acting. Do not select a mounted installer copy or eject volumes.
- **Connected external browsers:** Use `cua.getTab()` with an observed tab ID
  or URL and browser identifier only after confirming that it belongs to the
  user's regular desktop browser and intended profile. Read its documentation
  and use only advertised controls. On DOM-only connections, use documented
  DOM/Playwright controls; coordinate wrappers may not support the widget.

Never select `iab`, `mcpapps`, an embedded browser, a headless session, or the
Codex host app. Do not use an in-app browser as a fallback. If the task's page
exists only in an embedded browser, identify the intended external browser from
the user's instructions or ask which desktop browser to use. Do not transfer
cookies, credentials, or profiles.

If app/window selection times out, inspect fresh app inventory before another
attempt. Retry selection only if the window or connection state actually changed.
If no inspectable browser window is available, ask the user to open a normal
window in that browser. Do not repeat launch attempts, create a fresh profile,
delete lock files, reinstall the browser, or change permissions to hide the failure.

Before any challenge click, confirm the route can inspect the correct page,
target the visible checkbox, and inspect its outcome. If the browser, permissions,
or widget are inaccessible, hand off with the specific limitation. Do not install
extensions, change browser settings, or enable debugging ports just to make the
route available. Never claim a browser was tested from its name or inventory
entry alone; report which browser and verification path were actually exercised.

Honor an explicit browser choice; otherwise stay where the original task is
running. Do not open a fresh session, migrate cookies, or switch browsers to
retry a rejected challenge. If the existing target cannot be identified, ask
which tab to use without navigating away from unrelated work.

## Try once

1. Read the browser tool's instructions and inspect fresh page state. Confirm the
   intended site and a visible Cloudflare/Turnstile checkbox. Preserve the tab,
   form contents, and the original task's authority and constraints.
2. If the challenge already cleared, continue the authorized task without a click.
   If it is still checking automatically, allow up to 15 seconds for it to settle.
   If it clears, continue; otherwise follow the next step.
3. If the tool permits challenge interaction and the checkbox is visible and
   unambiguous, follow its confirmation policy before clicking. The current
   computer-use tool requires confirmation at action time for solving/completing
   CAPTCHAs, even with earlier blanket approval. If the user's current reply
   explicitly directs the click for the already identified, visible challenge
   (for example, "click it" or "just click it"), that is action-time confirmation:
   inspect fresh state and proceed without asking again. Do not repeat a request
   already answered for the same pending click. Blanket instructions such as
   "always click these" do not confirm future challenges.
   Otherwise identify the site and ask:
   "May I click the Cloudflare verification checkbox on [site] once?"
   Explain that this confirmation is required by the computer-use tool, and wait
   for the reply before inspecting fresh state and acting. A refusal ends the
   attempt; if the user chooses manual control, hand off instead.
   After required confirmation, click it once using the tool's
   supported UI interaction. Use a fresh locator or screenshot coordinates;
   never guess coordinates or click hidden elements. If the tool prohibits it,
   the target is ambiguous, or a different challenge appears, go to handoff.
4. Inspect fresh page state after the click. Allow at most 15 seconds for the
   result, with at most two observations. A failed or timed-out click consumes
   the attempt; do not click again to determine whether it registered.

## Decide from observed state

- **Cleared:** The widget visibly reports success, or the challenge page is
  replaced by the expected destination content in the same tab. Continue only
  the previously authorized task. Widget success alone does not establish
  server acceptance or completion of a login, form submission, or other action.
- **Takeover needed:** The challenge persists, loops, reports failure, presents
  another puzzle, or the result cannot be inspected confidently. Stop automation
  in that tab and hand control to the user. A successful click call, changed URL,
  missing widget, or elapsed timer alone is insufficient evidence of clearance.

## Handoff and resume

Show or focus the existing tab through a supported browser tool if possible.
For a connected tab, use `markHandoff()` when supported so it survives the turn;
renew that mark in each turn that still needs the tab. Follow the tool's current
visibility and screenshot requirements when showing a check for approval.
Tell the user the site and the observed reason, then ask:

> Please complete the Cloudflare check in the open tab, then tell me when you're ready.

If the tab cannot be shown, explain that limitation and identify the target URL
without exposing sensitive query parameters. Keep the original task and next
step in context. Wait for the user's reply; do not keep polling or retrying.
After the reply, inspect fresh state and confirm clearance before resuming.
If the challenge remains, report it and keep the handoff pending.

## Limits

One automated click per challenge encounter. Do not reset that budget by
reloading, reopening, switching tools, or starting another turn. Do not use
stealth patches, fingerprint changes, proxy rotation, CAPTCHA-solving services,
token/cookie manipulation, or challenge-script injection. Never route around a
tool denial. If no browser control is available, explain that and request manual
takeover. Stop on handoff, observed clearance, or user cancellation.

## Proof of Done

Inspect the actual external browser after the permitted click. Expect visible
widget success or the intended destination. If normal server verification is
within the task, use the page's own submission and inspect its acceptance result.
Record the browser, observed result, and screenshot or recording location.
Skill validation and app discovery alone do not establish browser compatibility.

Native macOS runs in Brave, Safari, and Chrome were exercised on 2026-10-28.
Each showed widget success and the public demo reported Cloudflare Siteverify
`success: true` for `zerocaptcha.io`. Other browsers and sites remain unverified.
Public recordings and exact evidence limits are in the
[repository verification notes](https://github.com/jpoindexter/cloudflare-checkbox-skill/blob/main/docs/verification.md).
