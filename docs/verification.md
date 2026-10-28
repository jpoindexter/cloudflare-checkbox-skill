# Browser verification

## Executed path

- Date: 28 October 2026.
- Platform: native desktop browsers on macOS.
- Public target: https://zerocaptcha.io/captcha-test/cloudflare-turnstile-managed
- Visible sitekey: `0x4AAAAAAFMVDQUY9ZAPO0ni` (not a documented Cloudflare dummy key).
- Input: one ordinary checkbox click per encounter, after the required confirmation.
- Widget result: visible success and a token-ready state.
- Next action: the page's normal **Verify with Cloudflare** button.
- Result: the page displayed **Cloudflare accepted the token**, `success: true`,
  and hostname `zerocaptcha.io`.

| Browser | Installed app version | Mechanism | Result | Evidence |
| --- | --- | --- | --- | --- |
| Brave | 156.1.98.60 | Native app UI in the user's existing profile | Accepted | [Video](../assets/videos/brave.mp4), [final frame](../assets/posters/brave.png) |
| Safari | 27.2 | Native app UI in the user's existing profile | Accepted | [Video](../assets/videos/safari.mp4), [final frame](../assets/posters/safari.png) |
| Chrome | 156.0.8078.74 | Native app UI in the user's existing profile | Accepted | [Video](../assets/videos/chrome.mp4), [final frame](../assets/posters/chrome.png) |

The page performs its own server-side Siteverify request. We observed its rendered
response; we did not independently inspect Cloudflare's server logs. After a
successful verification the page resets the widget, so the final frame can show
an unchecked box beside an accepted verdict. That is not a second click.

## Corrections retained in the skill

- External desktop browsers only. Embedded/in-app and headless sessions are excluded.
- Preserve the user's actual profile and tab.
- Resolve duplicate app identifiers with the exact installed application path.
- Stop repeated launch attempts when no inspectable window is available.
- Do not ask again after a current confirmation for the same pending click.
- Preserve tool-required confirmation for each new CAPTCHA encounter.

## Recording treatment

The recordings are genuine screen captures of native browser windows. Public
exports are cropped to the public test page and may be trimmed to exclude
unrelated browsing. No result states were synthesized, replaced, or accelerated.
Personal browser tabs, bookmarks, profile controls, and unrelated pages are
excluded. Raw desktop recordings are not included in the repository.

## Not established

These runs do not prove universal browser compatibility, a full-page WAF bypass,
unattended solving, or success on another site or future challenge. They do not
verify login or form submission to an unrelated service.

## Reproduce

Use your authorized desktop browser task, read the skill, identify the current
check, obtain any confirmation required by the runtime, click once, and inspect
fresh state. If the check fails or the tool cannot control it, stop and hand off.
A dummy-key rehearsal is useful for UI testing but must be labeled separately
from a live-sitekey run.

App versions were read from the installed applications after the recorded runs.
The public videos are silent H.264/MP4 exports: Brave 20 seconds, Safari and
Chrome 25 seconds each. Private browser chrome is cropped; Brave is trimmed
before navigation to an unrelated page.
