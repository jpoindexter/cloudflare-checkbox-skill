# Cloudflare checkbox skill

A Codex skill for handling a visible Cloudflare or Turnstile checkbox in the
user's regular desktop browser and existing profile. It makes one permitted
click, inspects the result, and hands control back when the check fails or is
uncertain.

**Uses the user's desktop browser. No Codex in-app, embedded, or headless browser.**

## Live recordings

Native macOS runs on 28 October 2026. Each checkbox required a click; afterward,
the demo page reported Cloudflare Siteverify `success: true` for `zerocaptcha.io`.
These are live-sitekey widget demos, not Cloudflare dummy test keys.

| Browser | Observed result | Recording | Final frame |
| --- | --- | --- | --- |
| Brave | Widget success; page-reported server acceptance | [MP4](assets/videos/brave.mp4) | [PNG](assets/posters/brave.png) |
| Safari | Widget success; page-reported server acceptance | [MP4](assets/videos/safari.mp4) | [PNG](assets/posters/safari.png) |
| Chrome | Widget success; page-reported server acceptance | [MP4](assets/videos/chrome.mp4) | [PNG](assets/posters/chrome.png) |

See [verification notes](docs/verification.md) for the test method and limits.
The public copies are cropped to the test page to remove personal tabs,
bookmarks, profile controls, and unrelated content. Results have not been altered.

### Brave

https://github.com/user-attachments/assets/ffc81e31-87c9-417d-a6e4-04a5584ebca0

### Safari

https://github.com/user-attachments/assets/046fed90-30b1-417e-a099-77a0bf38df58

### Chrome

https://github.com/user-attachments/assets/38ef62db-64ec-48f4-a960-5d23ed9e9520

[Video attachment archive](https://github.com/jpoindexter/cloudflare-checkbox-skill/issues/1)

## Requirements

- Codex with a computer-use runtime exposing `mcp__cua_repl` and the `cua` API.
- Permission to inspect and control the user's chosen desktop browser.
- An authorized browser task and an identifiable browser window, profile, and tab.

This is an instruction skill, not a standalone clicker, browser extension, or
CAPTCHA-solving service. It does not grant permissions or add browser control.

## Install

Using the skills CLI:

```sh
npx skills add jpoindexter/cloudflare-checkbox-skill --skill cloudflare-checkbox --global --agent codex
```

Or clone this repository and copy `skills/cloudflare-checkbox` into your
`~/.codex/skills` directory. If it is already installed, update that copy rather
than creating a second installation. It will be available on the next turn.

## Use

```text
Use $cloudflare-checkbox for this check in my Brave browser.
```

The skill permits implicit invocation. For explicit automatic routing during
active browser tasks, add this to your `AGENTS.md`:

```text
When an authorized task encounters a Cloudflare or Turnstile human check in
the user's regular external desktop browser, read and apply cloudflare-checkbox.
Use the existing browser/profile, preserve its one-attempt limit, verify the
visible outcome, and hand off on failure. Never use an in-app or headless browser.
Follow the computer-use tool's confirmation policy.
```

This activates when an agent is already doing browser work; it does not monitor
your browsing in the background.

## Behavior

1. Identify the existing external browser, profile, and target tab.
2. Read fresh page state and let an automatic check settle if applicable.
3. Obtain any confirmation required by the browser tool, then click once.
4. Inspect fresh state. Continue the authorized task only after observed clearance.
5. On failure, a loop, uncertainty, or unsupported controls, show the tab and
   ask the user to finish the check. Reinspect before resuming.

A current instruction such as "click it" for the already identified check
counts as confirmation for that pending click. The skill avoids asking again.
The computer-use tool currently requires action-time confirmation for each new
CAPTCHA challenge; this skill cannot disable that requirement.

## Browser handling

The skill discovers available controls instead of maintaining a browser-name
allowlist. Native desktop control was exercised in Brave, Safari, and Chrome.
Other browsers may work when the runtime can inspect and control their actual
windows, but they have not been verified here.

If two app copies share an identifier, select the exact installed app path.
If the browser has no inspectable window, ask the user to open a normal window
instead of repeatedly relaunching it or creating a new profile.

## Limits

- Widget success is not proof that a protected login, submission, or request succeeded.
- A live widget demo is not proof of passing a full-page Cloudflare WAF challenge.
- No universal success guarantee across sites, devices, browser versions, or sessions.
- No stealth patches, proxy rotation, cookie/token manipulation, solver services,
  challenge-script injection, or routing around tool-policy denials.
- Test footage shows confirmed UI actions, not unattended CAPTCHA solving.

## Contributing

Report the browser, OS, runtime, visible error, and the last observed page state.
Remove personal data, credentials, cookies, and challenge tokens from reports.
Keep changes scoped to browser selection, one-attempt handling, verification, or
manual takeover. Do not weaken the runtime's permission or confirmation rules.

## License and attribution

[MIT](LICENSE) covers the authored skill and documentation. Cloudflare, Turnstile,
browser brands, and third-party UI shown in recordings remain their respective
owners' property. This project is not affiliated with Cloudflare or the browser
vendors. The public demo is operated by ZeroCaptcha; no solver API or paid
solving service was used.
